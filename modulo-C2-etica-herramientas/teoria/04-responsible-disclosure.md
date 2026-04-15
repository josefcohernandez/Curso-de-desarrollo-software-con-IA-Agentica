# Responsible Disclosure y Seguridad

## Introducción

Trabajar con un agente de IA plantea dos escenarios de seguridad distintos: el agente **encuentra** una vulnerabilidad existente en tu código, o el agente **introduce** una vulnerabilidad nueva. Cada escenario requiere un protocolo diferente.

---

## Escenario 1: El Agente Encuentra una Vulnerabilidad

Durante una exploración de codebase o una tarea de debugging, el agente puede identificar una vulnerabilidad de seguridad — en tu propio código o en una dependencia.

### Protocolo

1. **No publicar los detalles en un PR público**. Si el repositorio es público, no incluir el detalle de la vulnerabilidad en la descripción del PR ni en los commits
2. **Reportar internamente al equipo de seguridad** (o al responsable de seguridad si no hay equipo dedicado)
3. **Si es en una dependencia open source**, seguir el proceso de responsible disclosure del proyecto:
   - Buscar si el proyecto tiene una política de seguridad (`SECURITY.md`)
   - Reportar a través del canal indicado (generalmente un email de seguridad o GitHub Security Advisories)
   - No publicar la vulnerabilidad hasta que haya un fix disponible
4. **Documentar internamente** qué se encontró, cuándo, y qué acción se tomó

### Ejemplo de flujo

```text
Agente encuentra SQL injection en src/api/search.js
    ↓
Developer verifica que es una vulnerabilidad real (no false positive)
    ↓
Crear issue privado / ticket de seguridad (no público)
    ↓
Fix con PR marcado como security fix
    ↓
Review prioritario por miembro senior
    ↓
Deploy urgente si afecta a producción
    ↓
Post-mortem documentado
```

---

## Escenario 2: El Agente Introduce una Vulnerabilidad

Este es el escenario más insidioso. El agente genera código que funciona correctamente pero tiene una vulnerabilidad de seguridad. Los tipos más comunes:

### Vulnerabilidades frecuentes en código generado por IA

| Vulnerabilidad | Ejemplo | Por qué la IA lo genera |
|----------------|---------|------------------------|
| **SQL injection** | Concatenación de strings en queries | Parece más simple que prepared statements |
| **XSS** | Renderizar input del usuario sin sanitizar | El código "funciona" sin sanitización |
| **Path traversal** | `fs.readFile(userInput)` sin validación | No considera inputs maliciosos |
| **Secrets hardcodeados** | API key directamente en el código | Es la forma más directa de hacer funcionar algo |
| **IDOR** | Acceso a recursos sin verificar ownership | La autorización es la capa que más se olvida |
| **SSRF** | Fetch a URLs proporcionadas por el usuario | No considera que el input puede apuntar a servicios internos |

### Cómo detectar antes de que llegue a producción

1. **Code review con checklist de seguridad**: usar la checklist del [Apéndice B](../../recursos/checklists/checklist-revision-codigo-ia.md) con foco en la sección de seguridad
2. **SAST (Static Application Security Testing)**: herramientas como Semgrep, SonarQube o CodeQL que detectan patrones inseguros automáticamente
3. **Hooks de seguridad**: configurar un hook que ejecute un scanner de seguridad después de cada edición del agente
4. **Tests de seguridad**: escribir tests específicos para los OWASP Top 10

### Ejemplo de hook de seguridad

```bash
#!/bin/bash
# Hook post-edit: ejecutar Semgrep en archivos modificados
files=$(git diff --name-only --diff-filter=AM)
if [ -n "$files" ]; then
  semgrep --config=auto $files
  if [ $? -ne 0 ]; then
    echo "SECURITY: Semgrep encontró posibles vulnerabilidades"
    echo "Revisa los hallazgos antes de continuar"
  fi
fi
```

---

## OWASP Top 10 como Checklist

Para cada PR con código generado por IA, verifica al menos los 5 más relevantes:

| # | Vulnerabilidad OWASP | Qué buscar en el código |
|---|---------------------|------------------------|
| A01 | Broken Access Control | ¿Se verifica autorización en cada endpoint? |
| A02 | Cryptographic Failures | ¿Se usan algoritmos obsoletos? ¿Secrets en código? |
| A03 | Injection | ¿SQL, NoSQL, OS command injection? ¿Se usan prepared statements? |
| A07 | Identification and Authentication Failures | ¿Sesiones seguras? ¿Tokens con expiración? |
| A09 | Security Logging and Monitoring Failures | ¿Se loguean eventos de seguridad? ¿Sin datos sensibles en logs? |

> Referencia completa: [OWASP Top 10](https://owasp.org/www-project-top-ten/)

---

## Integración en el Workflow del Equipo

### Para equipos que ya usan CI/CD

Añadir un paso de SAST al pipeline:

```yaml
# .github/workflows/security.yml
name: Security Scan
on: [pull_request]

jobs:
  sast:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - name: Run Semgrep
        uses: semgrep/semgrep-action@v1
        with:
          config: >-
            p/owasp-top-ten
            p/javascript
            p/typescript
```

### Para equipos sin CI/CD

Ejecutar manualmente antes de cada merge:

```bash
# Instalar Semgrep
pip install semgrep

# Ejecutar contra el diff de la rama
semgrep --config=auto --diff-depth 2
```

---

## Cultura de Seguridad

La IA no introduce más vulnerabilidades que un developer humano promedio — pero introduce vulnerabilidades **diferentes**. La clave es:

- No asumir que el código IA es seguro porque "fue generado por una IA avanzada"
- No asumir que es inseguro por defecto — usar las mismas herramientas de verificación que usarías con código humano
- Mantener la seguridad como parte del proceso, no como una revisión post-hoc

---

## Navegación

| | |
|---|---|
| **Anterior** | [03-compliance-regulacion.md](03-compliance-regulacion.md) |
| **Siguiente** | [05-comparativa-herramientas.md](05-comparativa-herramientas.md) |
| **Módulo** | [README del módulo](../README.md) |
