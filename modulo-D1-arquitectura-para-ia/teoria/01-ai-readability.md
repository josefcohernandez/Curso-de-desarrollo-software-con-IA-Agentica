# AI-Readability como Atributo de Calidad

## Qué es AI-readability

La AI-readability es la facilidad con la que un agente de código puede entender, navegar y modificar tu código. Es análoga a la legibilidad humana, pero con criterios diferentes.

Un agente no lee código como un humano:
- **No tiene intuición**: no "sabe" lo que hace un archivo por su ubicación o nombre
- **No reconoce convenciones implícitas**: si no está escrito, no existe
- **No recuerda entre sesiones**: cada conversación empieza desde cero
- **Tiene un contexto limitado**: no puede tener todo el proyecto en mente a la vez

Esto significa que un proyecto perfectamente legible para humanos puede ser confuso para un agente, y viceversa. La AI-readability no reemplaza la legibilidad humana — la complementa.

---

## Los 5 Pilares de la AI-Readability

| Pilar | Qué significa | Ejemplo positivo | Ejemplo negativo |
|-------|---------------|------------------|------------------|
| **Modularidad explícita** | Cada módulo tiene una responsabilidad clara y documentada | `auth/` con README que explica el flujo de autenticación | Funciones de auth dispersas en 15 ficheros sin documentación |
| **Interfaces como contrato** | Las APIs internas tienen tipos, validación y documentación | TypeScript con types exportados y validación Zod | JavaScript sin tipos, sin JSDoc, sin validación |
| **Tests como especificación** | Los tests documentan el comportamiento esperado | Tests descriptivos: `"should reject expired tokens"` | Tests que solo verifican implementación: `"should call verify()"` |
| **Convenciones explícitas** | Las reglas del proyecto están escritas, no asumidas | CLAUDE.md con convenciones de naming y estructura | "Todos saben que usamos camelCase" |
| **Bajo acoplamiento** | Los módulos se pueden entender de forma aislada | Dependency injection, interfaces claras entre módulos | Imports circulares, variables globales compartidas |

### Pilar 1: Modularidad explícita

Un agente opera mejor cuando puede enfocarse en un módulo sin necesidad de entender todo el proyecto. Esto requiere:

- Directorios con responsabilidad clara y documentada
- Un punto de entrada visible (index, main, exports)
- Dependencias declaradas, no implícitas

**Pregunta de evaluación**: ¿puede el agente trabajar en `src/payments/` sin leer nada de `src/users/`?

### Pilar 2: Interfaces como contrato

Los tipos son la forma más eficiente de comunicar expectativas a un agente:

```typescript
// Bueno: el agente sabe exactamente qué recibe y qué devuelve
interface PaymentResult {
  success: boolean;
  transactionId: string;
  amount: number;
  currency: "USD" | "EUR" | "GBP";
  error?: PaymentError;
}

async function processPayment(request: PaymentRequest): Promise<PaymentResult> {
  // ...
}
```

```javascript
// Malo: el agente tiene que adivinar la estructura
async function processPayment(data) {
  // ¿Qué campos tiene data? ¿Qué devuelve?
}
```

### Pilar 3: Tests como especificación

Un test bien escrito le dice al agente qué debe hacer el código mejor que cualquier comentario:

```typescript
describe("PaymentService", () => {
  it("should process a valid payment and return transaction ID", async () => {
    // El agente entiende: input válido → éxito con transactionId
  });

  it("should reject payments exceeding daily limit of $10,000", async () => {
    // El agente entiende: hay un límite diario de $10,000
  });

  it("should retry failed payments up to 3 times with exponential backoff", async () => {
    // El agente entiende: hay retry logic con límite y backoff
  });
});
```

### Pilar 4: Convenciones explícitas

Todo lo que "todos saben" pero nadie ha escrito es invisible para el agente. Documenta en CLAUDE.md o `.claude/rules/`:

- Naming conventions (archivos, funciones, variables)
- Estructura de directorios y dónde va cada cosa
- Patrones de error handling
- Qué herramientas usar para qué (npm vs yarn, jest vs vitest)

### Pilar 5: Bajo acoplamiento

El agente toma mejores decisiones cuando puede entender un módulo aislado. El acoplamiento alto fuerza al agente a cargar demasiado contexto, aumentando el riesgo de error.

Señales de alto acoplamiento:
- Imports circulares
- Variables globales compartidas
- Módulos que no compilan/funcionan sin otros módulos específicos
- Cambiar un archivo requiere cambiar 5 más

---

## Métricas Proxy de AI-Readability

No puedes medir AI-readability directamente, pero estas preguntas sirven como proxy:

| Pregunta | Mide |
|----------|------|
| ¿Puede el agente añadir una feature sin leer más de 5 archivos? | Modularidad |
| ¿Puede el agente ejecutar tests de un módulo sin configuración manual? | Independencia |
| ¿Los tests fallan de forma descriptiva (qué esperaba vs qué recibió)? | Tests como spec |
| ¿Las convenciones de naming están documentadas en algún fichero? | Convenciones explícitas |
| ¿Puede el agente entender `src/payments/` sin leer `src/users/`? | Bajo acoplamiento |
| ¿Los tipos cubren las interfaces públicas del módulo? | Interfaces como contrato |

Si respondes "no" a 3 o más preguntas, tu proyecto tiene oportunidades significativas de mejora en AI-readability.

---

## Relación con Otras Cualidades de Software

La AI-readability no es un atributo aislado. Se refuerza mutuamente con:

- **Mantenibilidad**: código fácil de entender para agentes es fácil de entender para humanos nuevos en el equipo
- **Testabilidad**: los tests como spec requieren código testable
- **Modularidad**: la modularidad explícita es buena arquitectura independientemente de la IA

Invertir en AI-readability es invertir en calidad de software general. No es "adaptar el código para la IA" — es hacer explícito lo que siempre debería haber estado explícito.
