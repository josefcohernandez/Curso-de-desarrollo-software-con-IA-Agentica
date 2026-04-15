# Ejercicio 2: Crear un Prompt Robusto para CI/CD

## Objetivo

Crear un prompt para revisión automática de PRs en CI/CD que produzca output JSON estructurado. Testearlo con 5 PRs de ejemplo para verificar robustez y consistencia.

## Contexto

Un prompt de CI/CD debe funcionar sin intervención humana. No hay "segunda oportunidad" — el prompt debe producir un resultado correcto y parseable la primera vez. Este ejercicio te enseña a construir ese nivel de robustez.

---

## Instrucciones

### Paso 1: Define el JSON schema

Define la estructura exacta del output que tu pipeline downstream espera:

```json
{
  "approved": true,
  "confidence": 0.85,
  "issues": [
    {
      "severity": "medium",
      "file": "src/api/users.ts",
      "line": 42,
      "category": "security",
      "description": "Input no validado antes de query a base de datos"
    }
  ],
  "summary": "PR añade endpoint de búsqueda de usuarios. Sin issues críticos.",
  "tests_status": "all_passing"
}
```

### Paso 2: Escribe el prompt

Escribe un prompt que cumpla los 5 principios (determinismo, fail-safe, verificación, restricciones, output estructurado):

```text
Analiza los cambios en este PR comparando la branch actual con main.

Para cada archivo modificado, verifica:
1. SEGURIDAD: ¿Hay inputs no validados, inyección SQL/XSS, secrets expuestos?
2. TESTS: ¿Los cambios tienen cobertura de tests? ¿Qué tests faltan?
3. REGRESIONES: ¿El cambio puede romper funcionalidad existente?
4. CONVENCIONES: ¿El código sigue las convenciones del proyecto (lee CLAUDE.md)?

Reglas estrictas:
- Si encuentras un issue con severity "critical", approved DEBE ser false
- Si no puedes verificar algo con certeza, marca approved=false y explica en issues
- NUNCA apruebes si tienes dudas — es preferible un falso negativo
- Máximo 10 issues, priorizados por severity (critical > high > medium)

Output JSON obligatorio con este schema:
{
  "approved": boolean,
  "confidence": number (0.0-1.0),
  "issues": [{"severity": "critical|high|medium", "file": string, "line": integer, "category": "security|testing|regression|convention", "description": string}],
  "summary": string (máximo 100 caracteres),
  "tests_status": "all_passing|some_failing|no_tests|could_not_run"
}
```

### Paso 3: Prepara 5 PRs de test

Crea 5 branches con cambios específicos para testear el prompt:

| PR | Contenido | Resultado esperado |
|----|-----------|-------------------|
| **PR 1: Happy path** | Feature limpia con tests | `approved: true`, 0 issues |
| **PR 2: Seguridad** | Endpoint con SQL injection | `approved: false`, issue critical de security |
| **PR 3: Sin tests** | Feature nueva sin tests | `approved: false`, issue high de testing |
| **PR 4: Regresión** | Cambio que modifica interfaz pública | `approved: false`, issue de regression |
| **PR 5: Edge case** | PR con solo cambios en README | `approved: true`, 0 issues |

### Paso 4: Ejecuta y verifica

Para cada PR:

```bash
git checkout <branch-del-pr>

RESULT=$(claude -p "$(cat prompts/pr-review.txt)" \
  --allowedTools "Read,Grep,Glob,Bash(npm test)" \
  --max-turns 15 \
  --max-budget-usd 1.00 \
  --output-format json)

echo "$RESULT" | jq .
```

Verifica para cada PR:
- [ ] El output es JSON válido (parseable con `jq`)
- [ ] `approved` tiene el valor esperado
- [ ] Los issues detectados son correctos (sin falsos positivos graves)
- [ ] `summary` es descriptivo y conciso

### Paso 5: Itera sobre el prompt

Si algún PR produce un resultado incorrecto:
1. Identifica qué instrucción falta o es ambigua
2. Ajusta el prompt
3. Re-ejecuta los 5 PRs para verificar que no has roto los que funcionaban

---

## Criterios de Éxito

- [ ] El prompt cumple los 5 principios (determinismo, fail-safe, verificación, restricciones, output estructurado)
- [ ] Los 5 PRs de test producen JSON válido
- [ ] Al menos 4 de 5 PRs producen el resultado esperado
- [ ] Has iterado al menos una vez sobre el prompt para mejorar precisión

## Tiempo Estimado

15 minutos
