# Prompts para CI/CD y Ejecución Desatendida

## La Diferencia Fundamental

En uso interactivo, el humano corrige el rumbo: si el agente se desvía, le dices "no, eso no" y ajusta. En CI/CD, **el prompt debe funcionar al 100% sin intervención**. No hay segunda oportunidad.

Esto cambia radicalmente cómo se escriben los prompts:

| Aspecto | Prompt interactivo | Prompt para CI/CD |
|---------|-------------------|-------------------|
| Ambigüedad | El humano clarifica | Fallo silencioso o resultado incorrecto |
| Error handling | El humano decide | El prompt debe especificar qué hacer ante errores |
| Output | Texto libre legible | JSON estructurado parseable por scripts |
| Scope | Flexible, puede expandirse | Estrictamente limitado por restricciones |
| Verificación | El humano revisa | El prompt incluye auto-verificación |

---

## Los 5 Principios de Prompts Robustos

### 1. Determinismo

La misma entrada debe producir la misma salida. Evita instrucciones ambiguas que dependan de la "interpretación" del agente.

```text
# Malo: resultado impredecible
Revisa este PR y comenta lo que te parezca importante.

# Bueno: resultado determinista
Analiza los cambios en este PR comparando la branch actual vs main.
Para cada archivo modificado, verifica:
1. ¿Hay errores de sintaxis?
2. ¿Los tests cubren los cambios?
3. ¿Hay vulnerabilidades de seguridad (OWASP top 10)?
Reporta solo problemas encontrados, no sugerencias de estilo.
```

### 2. Fail-Safe

Ante la duda, el agente no debe hacer nada (exit 1) en lugar de asumir. Esto previene cambios incorrectos en producción.

```text
# Instrucción fail-safe
Si no puedes determinar con certeza si un cambio es seguro,
responde con {"approved": false} y explica qué no pudiste verificar.
NUNCA apruebas un PR si tienes dudas.
```

### 3. Verificación Incluida

El prompt incluye un paso de verificación como parte de la tarea, no como algo opcional.

```text
Después de analizar el PR:
1. Ejecuta `npm test` y verifica que todos los tests pasan
2. Ejecuta `npm run lint` y verifica que no hay errores
3. Si alguno falla, incluye el error en el reporte
```

### 4. Restricciones Explícitas

Usa los mecanismos de la herramienta para limitar qué puede hacer el agente:

```bash
claude -p "$PROMPT" \
  --allowedTools "Read,Grep,Glob" \
  --disallowedTools "Edit,Write,Bash" \
  --max-turns 15 \
  --max-budget-usd 1.00
```

| Restricción | Propósito |
|-------------|-----------|
| `--allowedTools` | Solo herramientas de lectura para análisis |
| `--disallowedTools` | Prohibir modificaciones en pipelines de review |
| `--max-turns` | Limitar iteraciones para evitar loops infinitos |
| `--max-budget-usd` | Control de costes por ejecución |

### 5. Output Estructurado

Usa `--output-format json` o `--json-schema` para que el output sea parseable por scripts downstream.

```bash
claude -p "$PROMPT" \
  --output-format json \
  --json-schema '{
    "type": "object",
    "properties": {
      "approved": {"type": "boolean"},
      "issues": {
        "type": "array",
        "items": {
          "type": "object",
          "properties": {
            "severity": {"enum": ["critical", "high", "medium"]},
            "file": {"type": "string"},
            "line": {"type": "integer"},
            "description": {"type": "string"}
          },
          "required": ["severity", "file", "description"]
        }
      },
      "summary": {"type": "string"}
    },
    "required": ["approved", "issues", "summary"]
  }'
```

---

## Prompt Template para CI/CD: Review de PRs

```text
Analiza los cambios en este PR (branch: $BRANCH vs main).

Verifica para cada archivo modificado:
1. No hay vulnerabilidades de seguridad (OWASP top 10)
2. Los tests cubren los cambios (o explica qué tests faltan)
3. No hay regresiones obvias (cambios que rompen funcionalidad existente)
4. El código sigue las convenciones del proyecto (lee CLAUDE.md si existe)

Output JSON con formato:
{
  "approved": boolean,
  "issues": [
    {
      "severity": "critical" | "high" | "medium",
      "file": "ruta/al/archivo.ts",
      "line": 42,
      "description": "Descripción del problema encontrado"
    }
  ],
  "summary": "Resumen de una línea del estado del PR"
}

Reglas:
- Si encuentras un issue "critical", approved DEBE ser false
- Si no puedes determinar algo, marca approved=false con un issue explicando por qué
- No incluyas sugerencias de estilo — solo problemas funcionales y de seguridad
- Máximo 10 issues (prioriza por severidad si hay más)
```

---

## Integración con GitHub Actions

```yaml
name: AI PR Review
on:
  pull_request:
    types: [opened, synchronize]

jobs:
  ai-review:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
        with:
          fetch-depth: 0

      - name: AI Review
        env:
          ANTHROPIC_API_KEY: ${{ secrets.ANTHROPIC_API_KEY }}
        run: |
          RESULT=$(claude -p "Analiza los cambios en este PR..." \
            --allowedTools "Read,Grep,Glob" \
            --max-turns 15 \
            --max-budget-usd 1.00 \
            --output-format json)

          APPROVED=$(echo "$RESULT" | jq -r '.approved')
          if [ "$APPROVED" = "false" ]; then
            echo "::warning::PR no aprobado por AI Review"
            echo "$RESULT" | jq -r '.issues[] | "- [\(.severity)] \(.file): \(.description)"'
          fi
```

---

## Checklist para Prompts de CI/CD

| Aspecto | Verificación |
|---------|-------------|
| Determinismo | ¿Produce el mismo resultado con la misma entrada? |
| Fail-safe | ¿Ante la duda, no hace nada o reporta incertidumbre? |
| Verificación | ¿Incluye un paso de verificación como parte de la tarea? |
| Restricciones | ¿Tiene `--allowedTools`, `--max-turns`, `--max-budget-usd`? |
| Output | ¿Produce JSON parseable con schema definido? |
| Edge cases | ¿Funciona con PRs vacíos, PRs enormes, PRs con conflictos? |
