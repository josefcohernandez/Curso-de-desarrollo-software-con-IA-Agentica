# Ejercicio 2: Refactorizar un Módulo Monolítico para IA

## Objetivo

Tomar un módulo monolítico y refactorizarlo para mejorar su AI-readability sin cambiar la funcionalidad. Añadir README, types y tests como spec.

## Contexto

Muchos proyectos tienen módulos que funcionan pero son difíciles de entender para un agente: un solo archivo grande, sin tipos, con tests que verifican implementación en vez de comportamiento. Este ejercicio te enseña a transformar ese tipo de módulo.

---

## Instrucciones

### Paso 1: Identifica el módulo monolítico

Busca en tu proyecto (o usa el ejemplo a continuación) un módulo con estas características:
- Un archivo grande (>200 líneas) con múltiples responsabilidades
- Sin types explícitos o con `any` frecuente
- Tests que verifican "cómo" funciona (mock de implementación) en vez de "qué" hace

**Ejemplo de módulo monolítico** (si no tienes uno propio):

```javascript
// utils/order-processor.js — 300 líneas, sin tipos, múltiples responsabilidades
function processOrder(data) {
  // Valida el pedido (líneas 1-50)
  if (!data.items || data.items.length === 0) throw new Error("bad");
  // Calcula el total (líneas 51-100)
  let total = 0;
  for (const item of data.items) { total += item.price * item.qty; }
  // Aplica descuentos (líneas 101-180)
  if (data.coupon) { /* lógica compleja */ }
  // Llama a Stripe (líneas 181-250)
  const charge = stripe.charges.create({ amount: total });
  // Envía email (líneas 251-300)
  sendEmail(data.email, "Order confirmed", { orderId: charge.id });
  return { orderId: charge.id, total };
}
```

### Paso 2: Divide responsabilidades

Separa el módulo monolítico en archivos con responsabilidad única:

```text
src/orders/
├── README.md              # Nuevo: qué hace este módulo
├── index.ts               # Nuevo: exporta la API pública
├── order.types.ts         # Nuevo: interfaces y tipos
├── order.validator.ts     # Validación de pedidos
├── order.calculator.ts    # Cálculo de totales y descuentos
├── order.service.ts       # Orquestación del flujo completo
└── __tests__/
    ├── order.validator.test.ts
    ├── order.calculator.test.ts
    └── order.service.test.ts
```

### Paso 3: Añade types

Crea un archivo de tipos que documente las interfaces públicas:

```typescript
// order.types.ts
export interface OrderItem {
  productId: string;
  name: string;
  price: number;    // En centavos
  quantity: number;
}

export interface OrderRequest {
  customerId: string;
  items: OrderItem[];
  couponCode?: string;
  shippingAddress: Address;
}

export interface OrderResult {
  success: boolean;
  orderId: string;
  totalCents: number;
  discountApplied: number;
  error?: OrderError;
}
```

### Paso 4: Escribe tests como spec

Reescribe los tests para que documenten el comportamiento esperado:

```typescript
describe("OrderCalculator", () => {
  it("should sum item prices multiplied by quantity", () => {
    // El agente entiende: total = sum(price * quantity)
  });

  it("should apply 10% discount for coupon SAVE10", () => {
    // El agente entiende: coupon SAVE10 = 10% off
  });

  it("should not allow negative totals after discount", () => {
    // El agente entiende: total mínimo es 0
  });
});
```

### Paso 5: Crea el README del módulo

```markdown
# Orders Module

## Responsabilidad
Procesa pedidos: valida items, calcula totales con descuentos,
procesa pago y envía confirmación.

## API pública
- `processOrder(request: OrderRequest): Promise<OrderResult>`

## Dependencias externas
- Stripe SDK (pagos)
- Email service (confirmaciones)

## Testing
- `npm test -- --testPathPattern=orders`
```

### Paso 6: Verifica con el agente

Pide al agente una tarea sobre el módulo refactorizado:

```text
Añade soporte para cupones de descuento por porcentaje y por valor fijo
al módulo de orders. Lee el README y los tests existentes primero.
```

Observa si el agente:
- Lee el README antes de explorar código
- Respeta los tipos existentes
- Añade tests que siguen el patrón existente

---

## Criterios de Éxito

- [ ] El módulo está dividido en archivos con responsabilidad única
- [ ] Las interfaces públicas tienen tipos completos
- [ ] Los tests describen comportamiento, no implementación
- [ ] Hay un README que explica el módulo
- [ ] La funcionalidad original no ha cambiado
- [ ] El agente trabaja mejor con el módulo refactorizado

## Tiempo Estimado

20 minutos
