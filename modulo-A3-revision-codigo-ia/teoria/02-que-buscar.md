# Qué buscar en una revisión de código IA

## El checklist de revisión por prioridad

No todo tiene la misma urgencia. Este checklist está ordenado por **impacto potencial**: empieza por los items críticos y baja hasta los de baja prioridad.

### Prioridad Crítica — Bloquean el merge

| Qué buscar | Por qué es crítico | Ejemplo |
|------------|---------------------|---------|
| Imports y dependencias que no existen | El código no compilará o fallará en runtime | `import { nonExistentUtil } from './utils'` |
| Vulnerabilidades de seguridad (OWASP top 10) | Exposición directa a ataques | SQL sin parametrizar, XSS, path traversal |
| Datos sensibles en el código | Filtración de credenciales | API keys hardcodeadas, secrets en logs |

**Cómo verificar:**

```bash
# Buscar posibles secrets hardcodeados
git diff --cached | grep -iE "(api_key|secret|password|token)\s*[=:]"

# Verificar que todos los imports resuelven
# (en proyectos Node.js)
npx tsc --noEmit

# Buscar queries SQL sin parametrizar
git diff --cached | grep -iE "SELECT.*\+.*\"|INSERT.*\+.*\""
```

### Prioridad Alta — Requieren cambios antes de merge

| Qué buscar | Por qué es importante | Ejemplo |
|------------|----------------------|---------|
| Lógica de negocio incorrecta o incompleta | El feature no hace lo que debería | Validación que acepta inputs inválidos |
| Regresiones en código existente | Rompe funcionalidad que ya funcionaba | Cambio en función compartida sin actualizar callers |
| Edge cases no manejados | Fallos en producción con datos reales | División por cero, arrays vacíos, null/undefined |

**Ejemplo de regresión sutil:**

```javascript
// ANTES (funcionaba correctamente)
function getUser(id) {
  const user = db.findById(id);
  if (!user) throw new NotFoundError(`User ${id} not found`);
  return user;
}

// DESPUÉS (el agente "mejoró" la función)
function getUser(id) {
  const user = db.findById(id);
  return user || null; // Cambió el contrato: ahora devuelve null en vez de lanzar error
  // Todos los callers que hacían try/catch ya no capturan el caso de "no encontrado"
}
```

### Prioridad Media — Deberían corregirse

| Qué buscar | Por qué importa | Ejemplo |
|------------|-----------------|---------|
| Over-engineering y complejidad innecesaria | Aumenta el coste de mantenimiento | Factory pattern para crear un solo tipo de objeto |
| Inconsistencia con patrones del proyecto | Confunde a otros desarrolladores | Usar callbacks donde el proyecto usa async/await |
| Tests insuficientes o superficiales | Falsa sensación de seguridad | Tests que solo cubren el happy path |

**Ejemplo de over-engineering:**

```typescript
// Lo que el agente generó: factory + strategy + interface
interface PaymentStrategy {
  process(amount: number): Promise<PaymentResult>;
}

class PaymentStrategyFactory {
  static create(type: string): PaymentStrategy {
    switch (type) {
      case 'stripe': return new StripePayment();
      default: throw new Error('Unknown type');
    }
  }
}

// Lo que necesitabas: una función
async function processStripePayment(amount: number): Promise<PaymentResult> {
  return stripe.charges.create({ amount });
}
```

### Prioridad Baja — Corregir si hay tiempo

| Qué buscar | Impacto | Ejemplo |
|------------|---------|---------|
| Estilo y naming inconsistente | Legibilidad | camelCase donde el proyecto usa snake_case |
| Comentarios innecesarios o incorrectos | Ruido / desinformación | Comentarios que describen lo obvio o lo que ya no es verdad |

## Cómo aplicar el checklist en la práctica

### Estrategia rápida (5 minutos)

Para cambios pequeños (<50 líneas):

1. Verificar que no hay secrets ni dependencias fantasma (Crítico)
2. Leer la lógica de negocio línea por línea (Alto)
3. Comprobar que hay tests para los cambios (Medio)

### Estrategia completa (15-20 minutos)

Para cambios grandes (>100 líneas):

1. **Scope** (1 min): `git diff --stat` — ¿cuántos archivos y líneas?
2. **Críticos** (3 min): buscar imports rotos, secrets, SQL sin parametrizar
3. **Lógica** (5 min): leer cada archivo cambiado entendiendo el flujo
4. **Regresiones** (3 min): para cada función modificada, verificar que los callers siguen funcionando
5. **Edge cases** (3 min): ¿qué pasa con null, vacío, duplicado, concurrente?
6. **Tests** (3 min): ¿cubren los casos nuevos y los edge cases?
7. **Estilo** (2 min): ¿es consistente con el resto del proyecto?

## La regla del "qué falta"

La habilidad más importante en la revisión de código IA no es encontrar lo que está mal — es encontrar **lo que falta**:

- ¿Falta validación de entrada?
- ¿Falta manejo de errores para un caso específico del negocio?
- ¿Falta la migración de base de datos para el nuevo campo?
- ¿Falta la actualización del contrato de API / documentación?
- ¿Falta el test para el edge case que motivó este cambio?

> **Tip**: al revisar un PR generado por IA, lee primero el prompt original que le diste al agente. Compara lo que pediste con lo que se generó. Lo que falta suele estar en esa diferencia.

---

[← Anterior: Por qué es diferente](01-por-que-es-diferente.md) | [Siguiente: Red flags de código IA →](03-red-flags-codigo-ia.md)
