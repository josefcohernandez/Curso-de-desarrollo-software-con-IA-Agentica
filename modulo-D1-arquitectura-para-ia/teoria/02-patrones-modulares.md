# Patrones de Arquitectura que Facilitan la Asistencia de IA

## Introducción

Estos 5 patrones no son nuevos — son buenas prácticas de ingeniería de software. Lo que cambia es que ahora tienen un beneficio adicional: hacen que los agentes de código sean dramáticamente más efectivos. Un proyecto que sigue estos patrones obtiene mejores resultados de la IA con menos esfuerzo.

---

## Patrón 1: Módulos Autónomos con README

Cada directorio tiene un `README.md` que explica qué hace, cómo se usa, sus dependencias y cómo testearlo. El agente lee el README antes de explorar el código, tomando mejores decisiones desde el inicio.

### Estructura recomendada

```text
src/payments/
├── README.md          # "Este módulo procesa pagos con Stripe..."
├── index.ts           # Exporta la API pública
├── payment.service.ts # Lógica de negocio
├── payment.types.ts   # Tipos e interfaces
├── payment.validator.ts
└── __tests__/
    ├── payment.service.test.ts
    └── payment.validator.test.ts
```

### Contenido mínimo del README de módulo

```markdown
# Payments Module

## Responsabilidad
Procesa pagos con Stripe. Gestiona creación de charges, refunds y webhooks.

## API pública
- `processPayment(request: PaymentRequest): Promise<PaymentResult>`
- `refundPayment(transactionId: string): Promise<RefundResult>`

## Dependencias
- Stripe SDK (`stripe`)
- Módulo `users` (para obtener el customer ID)
- Módulo `config` (para API keys)

## Testing
- `npm test -- --testPathPattern=payments` ejecuta solo los tests de este módulo
- Requiere variable de entorno `STRIPE_TEST_KEY` (ver `.env.example`)
```

### Por qué funciona

Sin README, el agente necesita leer todos los archivos del módulo para entender qué hace. Con README, el agente tiene un mapa que le permite:
- Saber qué archivos son relevantes para la tarea
- Entender las dependencias sin rastrear imports
- Ejecutar tests sin adivinar el comando

---

## Patrón 2: Types como Documentación Viva

Los tipos (TypeScript, Python type hints, Rust types) son la mejor documentación para agentes. Un agente con types genera código correcto al primer intento con mucha más frecuencia que sin ellos.

### Ejemplo con TypeScript

```typescript
// payment.types.ts — El agente usa esto como contrato

export interface PaymentRequest {
  customerId: string;
  amount: number;         // En centavos (ej: 1000 = $10.00)
  currency: Currency;
  description: string;
  metadata?: Record<string, string>;
}

export type Currency = "USD" | "EUR" | "GBP";

export interface PaymentResult {
  success: boolean;
  transactionId: string;
  amount: number;
  currency: Currency;
  processedAt: Date;
  error?: PaymentError;
}

export interface PaymentError {
  code: "insufficient_funds" | "card_declined" | "expired_card" | "processing_error";
  message: string;
  retryable: boolean;
}
```

### Ejemplo con Python type hints

```python
from dataclasses import dataclass
from enum import Enum
from typing import Optional

class Currency(Enum):
    USD = "USD"
    EUR = "EUR"
    GBP = "GBP"

@dataclass
class PaymentRequest:
    customer_id: str
    amount: int          # En centavos (ej: 1000 = $10.00)
    currency: Currency
    description: str

@dataclass
class PaymentResult:
    success: bool
    transaction_id: str
    amount: int
    currency: Currency
    error: Optional["PaymentError"] = None
```

### Inversión vs. retorno

Invertir en tipos es invertir en productividad con IA. El tiempo que dedicas a tipar interfaces públicas se recupera multiplicado cada vez que el agente genera código que respeta esos tipos sin necesidad de corrección.

---

## Patrón 3: Tests como Spec

Los tests bien escritos le dicen al agente exactamente qué debe hacer el código. Este patrón convierte los tests en la fuente de verdad del comportamiento esperado.

### Prompt pattern asociado

```text
Lee los tests en src/payments/__tests__/ y asegúrate de que tu implementación
los pase todos. No modifiques los tests.
```

### Tests que funcionan como spec

```typescript
describe("PaymentService.processPayment", () => {
  it("should return success with transactionId for valid payment", async () => {
    const request: PaymentRequest = {
      customerId: "cus_123",
      amount: 5000,
      currency: "USD",
      description: "Monthly subscription",
    };
    const result = await service.processPayment(request);
    expect(result.success).toBe(true);
    expect(result.transactionId).toMatch(/^txn_/);
  });

  it("should reject amounts below minimum of 50 cents", async () => {
    const request: PaymentRequest = {
      customerId: "cus_123",
      amount: 49,
      currency: "USD",
      description: "Too small",
    };
    const result = await service.processPayment(request);
    expect(result.success).toBe(false);
    expect(result.error?.code).toBe("processing_error");
  });

  it("should enforce daily limit of $10,000 per customer", async () => {
    // Simular que el customer ya gastó $9,500 hoy
    await simulateDailySpend("cus_123", 950000);
    const request: PaymentRequest = {
      customerId: "cus_123",
      amount: 60000, // $600, excede el límite diario
      currency: "USD",
      description: "Over limit",
    };
    const result = await service.processPayment(request);
    expect(result.success).toBe(false);
    expect(result.error?.code).toBe("insufficient_funds");
  });
});
```

El agente que lee estos tests entiende: hay un mínimo de 50 centavos, hay un límite diario de $10,000, los IDs de transacción empiezan con `txn_`.

---

## Patrón 4: Configuración en un Solo Lugar

Toda la configuración en un directorio `/config` con valores tipados. El agente no tiene que buscar en 10 archivos para entender la configuración.

```typescript
// config/index.ts
export const config = {
  stripe: {
    apiKey: env("STRIPE_API_KEY"),
    webhookSecret: env("STRIPE_WEBHOOK_SECRET"),
  },
  payments: {
    minAmountCents: 50,
    dailyLimitCents: 1_000_000, // $10,000
    maxRetries: 3,
    retryDelayMs: 1000,
  },
  database: {
    url: env("DATABASE_URL"),
    maxConnections: envInt("DB_MAX_CONNECTIONS", 10),
  },
} as const;
```

**Beneficio para el agente**: cuando necesita cambiar un límite o agregar una configuración, sabe exactamente dónde buscar y qué formato usar.

---

## Patrón 5: Capa de Abstracción para Servicios Externos

Envolver (wrap) APIs externas en una interfaz propia. Si cambias de proveedor, el agente solo toca un archivo.

```typescript
// services/payment-gateway.interface.ts
export interface PaymentGateway {
  charge(amount: number, currency: Currency, token: string): Promise<ChargeResult>;
  refund(chargeId: string): Promise<RefundResult>;
  getTransaction(id: string): Promise<Transaction>;
}

// services/stripe-gateway.ts
export class StripeGateway implements PaymentGateway {
  constructor(private stripe: Stripe) {}

  async charge(amount: number, currency: Currency, token: string): Promise<ChargeResult> {
    const stripeCharge = await this.stripe.charges.create({
      amount,
      currency: currency.toLowerCase(),
      source: token,
    });
    return this.mapToChargeResult(stripeCharge);
  }
  // ...
}
```

### Por qué funciona para IA

Cuando el agente necesita migrar de Stripe a otro proveedor, tiene una tarea clara: implementar `PaymentGateway` con el nuevo SDK. No necesita entender ni modificar el resto del sistema. La interfaz le dice exactamente qué métodos necesita y qué tipos debe respetar.

---

## Resumen: Checklist de Patrones

| Patrón | Pregunta de verificación |
|--------|--------------------------|
| Módulos con README | ¿Cada directorio principal tiene un README con responsabilidad, API y tests? |
| Types como docs | ¿Las interfaces públicas tienen tipos completos y descriptivos? |
| Tests como spec | ¿Un agente puede implementar el código leyendo solo los tests? |
| Config centralizada | ¿Toda la configuración está en un solo lugar con valores tipados? |
| Abstracción de servicios | ¿Los servicios externos están detrás de una interfaz propia? |
