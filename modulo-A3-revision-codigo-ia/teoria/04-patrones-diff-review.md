# Patrones de diff review para código IA

## El método de lectura en 3 pasadas

Cuando revisas un diff generado por IA, no intentes entenderlo todo de golpe. Usa un sistema de 3 pasadas con tiempos acotados.

### Primera pasada: Scope (10 segundos)

**Objetivo**: entender el tamaño y alcance del cambio.

```bash
git diff --stat
```

Salida ejemplo:

```text
 src/controllers/userController.ts   |  45 +++++++++++-----
 src/services/userService.ts         |  23 ++++++
 src/utils/validation.ts             |  67 ++++++++++++++++++++++
 src/models/user.ts                  |   3 +-
 tests/userController.test.ts        |  38 +++++++++++++
 package.json                        |   1 +
 6 files changed, 158 insertions(+), 19 deletions(-)
```

**Preguntas a responder:**
- ¿Cuántos archivos cambian? Si pediste un cambio en 1 archivo y tocan 6, investiga
- ¿Hay archivos nuevos? ¿Son necesarios?
- ¿Se tocan archivos que no deberían tocarse? (package.json, configuración, modelos)
- ¿El volumen de cambios es proporcional a lo que pediste?

### Segunda pasada: Lógica (2 minutos)

**Objetivo**: verificar que la lógica de negocio es correcta.

Lee el diff completo centrado en:

```bash
git diff
```

**Puntos de atención:**
- ¿Los condicionales son correctos? (`>` vs `>=`, `&&` vs `||`)
- ¿Los edge cases están cubiertos? (null, vacío, duplicado)
- ¿Las funciones modificadas mantienen su contrato? (mismos parámetros, mismo tipo de retorno, misma semántica de errores)
- ¿La lógica nueva se integra bien con la existente?

### Tercera pasada: Calidad (1 minuto)

**Objetivo**: verificar consistencia y limpieza.

- ¿Es consistente con el estilo del proyecto? (naming, patrones, formato)
- ¿Hay código muerto o sin usar?
- ¿Los tests son significativos?
- ¿Hay TODOs, console.logs o comentarios de debug?

## El "test del diff"

> Si el diff toca archivos que no mencionaste en tu prompt, investiga por qué.

Este es el test más simple y más efectivo. Lo que no pediste explícitamente debería justificarse.

**Señales de alarma en el scope:**

| Lo que pediste | Lo que el diff toca | Posible problema |
|----------------|---------------------|------------------|
| "Añade validación al form" | 2 archivos nuevos de utils | Duplicación de lógica existente |
| "Arregla el bug del login" | Cambia el modelo de User | Posible regresión en otros flujos |
| "Añade el endpoint /health" | Modifica package.json | Dependencia nueva innecesaria |
| "Mejora el rendimiento del query" | Cambia 12 archivos | Refactor no solicitado |

## La pregunta clave

Hazte esta pregunta después de leer el diff:

> **"¿Podría explicar este código a un compañero sin consultar lo que le pedí al agente?"**

Si la respuesta es **no**, hay dos posibilidades:
1. El código es demasiado complejo → pide simplificación
2. No entiendes lo que hace → no deberías aceptarlo

En ambos casos, la acción correcta es **no hacer merge**.

## Herramientas para la revisión

### En la terminal

```bash
# Scope: archivos y líneas cambiadas
git diff --stat

# Diff completo con contexto
git diff

# Solo nombres de archivos cambiados (útil para scope rápido)
git diff --name-only

# Diff de un archivo específico
git diff -- src/services/userService.ts

# Buscar patrones problemáticos en el diff
git diff | grep -n "console\.log\|TODO\|FIXME\|as any\|catch.*console"
```

### Con el agente

Después de que el agente genere código, pídele un resumen antes de revisar:

```text
Show me a summary of what you changed and why.
For each file: what changed and what was the reason.
```

Esto te da un mapa mental antes de leer el diff completo. Compara el resumen del agente con lo que ves en `git diff --stat` — si no coinciden, hay algo que investigar.

### Revisión asistida

También puedes usar el agente como segundo par de ojos:

```text
Review your own changes critically. 
Look for: missing edge cases, potential regressions, 
unnecessary complexity, and inconsistencies with 
the existing codebase patterns.
```

Esto no reemplaza tu revisión — pero puede surfacear problemas que el propio agente reconoce al re-examinar su código.

## Patrones de revisión por tamaño del cambio

| Tamaño | Líneas | Estrategia | Tiempo |
|--------|--------|------------|--------|
| Pequeño | <30 | Lectura completa línea por línea | 2 min |
| Mediano | 30-100 | 3 pasadas estándar | 5 min |
| Grande | 100-300 | 3 pasadas + revisión por archivo | 15 min |
| Muy grande | >300 | Pedir al agente que divida en PRs más pequeños | Variable |

> **Regla práctica**: si un cambio generado por IA supera las 300 líneas, es mejor pedir al agente que lo divida en cambios incrementales que puedas revisar uno a uno.

---

[← Anterior: Red flags de código IA](03-red-flags-codigo-ia.md) | [Siguiente: Deuda técnica silenciosa →](05-deuda-tecnica-silenciosa.md)
