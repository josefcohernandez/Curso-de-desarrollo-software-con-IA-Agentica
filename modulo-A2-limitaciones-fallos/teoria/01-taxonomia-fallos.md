# Taxonomía de Fallos de Agentes de Código

## Introducción

Cada vez que un agente de código genera una respuesta, existe la posibilidad de que contenga errores. Estos errores no son aleatorios — siguen patrones predecibles que podemos clasificar, medir y prevenir. Conocer la taxonomía de fallos te convierte en un **revisor eficaz** capaz de detectar problemas antes de que lleguen a producción.

Este documento presenta los 8 tipos de fallo más comunes organizados por frecuencia, peligrosidad y método de detección.

---

## Los 8 Tipos de Fallo

| Tipo de fallo | Qué es | Frecuencia | Peligrosidad | Detección |
|---------------|--------|------------|--------------|-----------|
| **Alucinación de API** | Inventa funciones, métodos o imports que no existen | Alta | Media | Compilación / ejecución falla |
| **Alucinación de lógica** | Código que compila pero hace algo diferente a lo pedido | Media | Alta | Tests unitarios / revisión manual |
| **Regresión silenciosa** | Arregla un bug pero introduce otro en otro lugar | Media | Muy alta | Test suite completa / revisión de diff |
| **Over-engineering** | Añade abstracciones, error handling o features no pedidas | Muy alta | Media | Revisión de diff / conteo de líneas |
| **Divergencia de estilo** | Código correcto pero inconsistente con el proyecto | Alta | Baja | Linters / revisión visual |
| **Degradación por contexto** | Pierde el hilo en sesiones largas, contradice instrucciones | Media | Alta | Monitoreo de coherencia temporal |
| **Sesgo de recencia** | Prioriza patrones del training data reciente sobre los del proyecto | Media | Media | Comparación con convenciones existentes |
| **Confianza excesiva** | Afirma que algo funciona sin haberlo verificado | Alta | Muy alta | Ejecución real del código |

---

## Detalle por Tipo

### 1. Alucinación de API

El agente genera código que referencia imports, funciones o métodos que simplemente no existen. Es el fallo más visible porque **el código no compila ni ejecuta**.

```python
# Ejemplo de alucinación: este import no existe
from fastapi.security import OAuth2TokenBearer  # No existe — lo correcto es OAuth2PasswordBearer
```

**Cuándo ocurre**: especialmente con librerías menos populares, versiones recientes o APIs que cambiaron entre versiones.

**Tu defensa**: ejecutar siempre el código antes de aceptar.

### 2. Alucinación de Lógica

El código compila y ejecuta sin errores, pero produce un resultado diferente al esperado. Es más peligroso que la alucinación de API porque **parece correcto**.

```javascript
// Pedido: filtrar usuarios activos
// Generado: filtra usuarios INactivos (lógica invertida)
const activeUsers = users.filter(u => !u.isActive);
```

**Tu defensa**: tests que validen el comportamiento esperado, no solo que no haya errores.

### 3. Regresión Silenciosa

El agente resuelve lo que le pediste pero rompe algo en otro lugar. Es el fallo **más peligroso** porque puede pasar completamente desapercibido.

**Cuándo ocurre**: al modificar funciones compartidas, cambiar firmas, refactorizar utils.

**Tu defensa**: ejecutar la test suite completa, no solo los tests del área modificada.

### 4. Over-engineering

El agente añade complejidad no solicitada: abstracciones prematuras, helpers para un solo uso, interfaces genéricas para una sola implementación. Es el fallo **más frecuente**.

```typescript
// Pedido: una función que sume dos números
// Generado: un framework de operaciones matemáticas
interface MathOperation {
  execute(a: number, b: number): number;
}

class AddOperation implements MathOperation {
  execute(a: number, b: number): number {
    return a + b;
  }
}

// Lo que necesitabas:
function add(a: number, b: number): number {
  return a + b;
}
```

**Tu defensa**: revisar el diff y preguntarte "¿pedí esto?".

### 5. Divergencia de Estilo

El código es correcto funcionalmente pero no sigue las convenciones del proyecto: nomenclatura diferente, patrones de error handling distintos, formato inconsistente.

**Cuándo ocurre**: cuando el agente no tiene acceso a las convenciones del proyecto (CLAUDE.md, ESLint config, etc.).

**Tu defensa**: incluir reglas de estilo en tu CLAUDE.md o archivo de configuración.

### 6. Degradación por Contexto

En sesiones largas, el agente empieza a olvidar instrucciones previas, contradecir decisiones anteriores o generar código duplicado. La calidad se deteriora gradualmente.

**Cuándo ocurre**: típicamente tras 30-50 intercambios, o cuando se han leído muchos archivos grandes.

**Tu defensa**: `/clear` entre tareas, `/compact` antes de llenar la ventana.

### 7. Sesgo de Recencia

El agente propone soluciones basadas en patrones recientes de su training data, ignorando las convenciones del proyecto. Puede sugerir React Server Components en un proyecto que usa Next.js Pages Router, o async/await donde el proyecto usa callbacks consistentemente.

**Tu defensa**: ser explícito sobre las convenciones del proyecto en el prompt o CLAUDE.md.

### 8. Confianza Excesiva

El agente afirma que el código funciona sin haberlo ejecutado. Dice "esto debería funcionar correctamente" cuando en realidad tiene bugs. Es especialmente peligroso porque el usuario tiende a confiar.

```text
Agente: "He implementado la función de autenticación. Debería funcionar correctamente."
Realidad: La función tiene un bug sutil que permite bypass en ciertos edge cases.
```

**Tu defensa**: nunca confiar en "debería funcionar" — ejecutar y verificar siempre.

---

## Matriz de Riesgo y Acción

| Nivel de riesgo | Tipos | Acción requerida |
|-----------------|-------|------------------|
| **Crítico** | Regresión silenciosa, Confianza excesiva | Test suite completa + revisión manual obligatoria |
| **Alto** | Alucinación de lógica, Degradación de contexto | Tests unitarios + ejecución del código |
| **Medio** | Alucinación de API, Over-engineering, Sesgo de recencia | Compilación + revisión de diff |
| **Bajo** | Divergencia de estilo | Linter + revisión visual |

---

## Flujo de Verificación Recomendado

Para cualquier cambio generado por IA, aplica este flujo en orden:

1. **Compilar/ejecutar** — descarta alucinaciones de API
2. **Revisar el diff** — descarta over-engineering y divergencia de estilo
3. **Ejecutar tests** — descarta alucinaciones de lógica
4. **Ejecutar test suite completa** — descarta regresiones silenciosas
5. **Verificar coherencia con la sesión** — descarta degradación de contexto

No todos los cambios requieren los 5 pasos, pero los cambios en código compartido o funciones críticas sí.

---

## Resumen

- Los fallos de agentes de código siguen patrones predecibles y clasificables
- La **regresión silenciosa** y la **confianza excesiva** son los más peligrosos porque pasan desapercibidos
- El **over-engineering** es el más frecuente — los modelos tienden a sobre-construir
- Tu mejor defensa es un flujo de verificación sistemático: compilar → diff → tests → suite completa
- Conocer la taxonomía te permite adaptar tu nivel de revisión al riesgo de cada cambio
