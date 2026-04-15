# Ejercicio 2: Encontrar la regresión

## Contexto

Un agente de código recibió este prompt: "Añade la funcionalidad de descuentos por volumen al carrito de compras. Si un usuario compra 5 o más unidades del mismo producto, se aplica un 10% de descuento en ese producto."

El agente generó un diff de unas 200 líneas. La nueva feature funciona correctamente, pero **esconde una regresión** que rompe funcionalidad existente.

Tu trabajo: encontrar la regresión.

## Código original (antes del cambio)

### `src/services/cartService.ts`

```typescript
import { Product, CartItem, Cart } from '../models/types';
import { TaxCalculator } from './taxCalculator';
import { InventoryService } from './inventoryService';

export class CartService {
  private taxCalculator: TaxCalculator;
  private inventoryService: InventoryService;

  constructor(taxCalculator: TaxCalculator, inventoryService: InventoryService) {
    this.taxCalculator = taxCalculator;
    this.inventoryService = inventoryService;
  }

  calculateItemTotal(item: CartItem): number {
    return item.product.price * item.quantity;
  }

  calculateSubtotal(cart: Cart): number {
    return cart.items.reduce((sum, item) => sum + this.calculateItemTotal(item), 0);
  }

  calculateTax(cart: Cart): number {
    const subtotal = this.calculateSubtotal(cart);
    return this.taxCalculator.calculate(subtotal, cart.shippingAddress.country);
  }

  calculateTotal(cart: Cart): number {
    const subtotal = this.calculateSubtotal(cart);
    const tax = this.calculateTax(cart);
    const shipping = this.calculateShipping(cart);
    return subtotal + tax + shipping;
  }

  calculateShipping(cart: Cart): number {
    const totalWeight = cart.items.reduce(
      (weight, item) => weight + (item.product.weight * item.quantity), 0
    );
    if (totalWeight === 0) return 0;
    if (cart.shippingAddress.country === 'ES') {
      return totalWeight <= 5 ? 4.99 : 9.99;
    }
    return totalWeight <= 5 ? 14.99 : 24.99;
  }

  async validateStock(cart: Cart): Promise<string[]> {
    const errors: string[] = [];
    for (const item of cart.items) {
      const available = await this.inventoryService.getStock(item.product.id);
      if (available < item.quantity) {
        errors.push(
          `${item.product.name}: solo quedan ${available} unidades (pediste ${item.quantity})`
        );
      }
    }
    return errors;
  }
}
```

## Diff generado por el agente

```diff
--- a/src/services/cartService.ts
+++ b/src/services/cartService.ts
@@ -1,6 +1,7 @@
 import { Product, CartItem, Cart } from '../models/types';
 import { TaxCalculator } from './taxCalculator';
 import { InventoryService } from './inventoryService';
+import { DiscountRule, VolumeDiscount } from '../models/discounts';
 
 export class CartService {
   private taxCalculator: TaxCalculator;
   private inventoryService: InventoryService;
+  private discountRules: DiscountRule[];
 
   constructor(taxCalculator: TaxCalculator, inventoryService: InventoryService) {
     this.taxCalculator = taxCalculator;
     this.inventoryService = inventoryService;
+    this.discountRules = [new VolumeDiscount(5, 0.10)];
   }
 
-  calculateItemTotal(item: CartItem): number {
-    return item.product.price * item.quantity;
+  calculateItemTotal(item: CartItem): number {
+    const baseTotal = item.product.price * item.quantity;
+    const discount = this.getItemDiscount(item);
+    return baseTotal * (1 - discount);
+  }
+
+  getItemDiscount(item: CartItem): number {
+    for (const rule of this.discountRules) {
+      if (rule.applies(item)) {
+        return rule.discountRate;
+      }
+    }
+    return 0;
   }
 
   calculateSubtotal(cart: Cart): number {
     return cart.items.reduce((sum, item) => sum + this.calculateItemTotal(item), 0);
   }
 
-  calculateTax(cart: Cart): number {
-    const subtotal = this.calculateSubtotal(cart);
-    return this.taxCalculator.calculate(subtotal, cart.shippingAddress.country);
+  calculateTax(cart: Cart): number {
+    const subtotalBeforeDiscount = cart.items.reduce(
+      (sum, item) => sum + item.product.price * item.quantity, 0
+    );
+    return this.taxCalculator.calculate(subtotalBeforeDiscount, cart.shippingAddress.country);
   }
 
   calculateTotal(cart: Cart): number {
     const subtotal = this.calculateSubtotal(cart);
     const tax = this.calculateTax(cart);
     const shipping = this.calculateShipping(cart);
     return subtotal + tax + shipping;
   }

   calculateShipping(cart: Cart): number {
     const totalWeight = cart.items.reduce(
       (weight, item) => weight + (item.product.weight * item.quantity), 0
     );
     if (totalWeight === 0) return 0;
     if (cart.shippingAddress.country === 'ES') {
       return totalWeight <= 5 ? 4.99 : 9.99;
     }
     return totalWeight <= 5 ? 14.99 : 24.99;
   }

   async validateStock(cart: Cart): Promise<string[]> {
     const errors: string[] = [];
     for (const item of cart.items) {
       const available = await this.inventoryService.getStock(item.product.id);
       if (available < item.quantity) {
         errors.push(
           `${item.product.name}: solo quedan ${available} unidades (pediste ${item.quantity})`
         );
       }
     }
     return errors;
   }
+
+  getAppliedDiscounts(cart: Cart): Array<{product: string; discount: string}> {
+    return cart.items
+      .filter(item => this.getItemDiscount(item) > 0)
+      .map(item => ({
+        product: item.product.name,
+        discount: `${this.getItemDiscount(item) * 100}%`
+      }));
+  }
 }
```

### Nuevo archivo: `src/models/discounts.ts`

```typescript
import { CartItem } from './types';

export interface DiscountRule {
  applies(item: CartItem): boolean;
  discountRate: number;
}

export class VolumeDiscount implements DiscountRule {
  minQuantity: number;
  discountRate: number;

  constructor(minQuantity: number, discountRate: number) {
    this.minQuantity = minQuantity;
    this.discountRate = discountRate;
  }

  applies(item: CartItem): boolean {
    return item.quantity >= this.minQuantity;
  }
}
```

### Archivo de tests: `tests/cartService.test.ts` (nuevos tests añadidos)

```typescript
describe('Volume Discounts', () => {
  test('should apply 10% discount for 5+ items', () => {
    const cart = createCart([
      { product: productA, quantity: 5 }  // 100 EUR x 5 = 500, -10% = 450
    ]);
    const service = new CartService(taxCalc, inventory);
    expect(service.calculateItemTotal(cart.items[0])).toBe(450);
  });

  test('should not apply discount for less than 5 items', () => {
    const cart = createCart([
      { product: productA, quantity: 4 }  // 100 EUR x 4 = 400, sin descuento
    ]);
    const service = new CartService(taxCalc, inventory);
    expect(service.calculateItemTotal(cart.items[0])).toBe(400);
  });

  test('should list applied discounts', () => {
    const cart = createCart([
      { product: productA, quantity: 5 },
      { product: productB, quantity: 2 }
    ]);
    const service = new CartService(taxCalc, inventory);
    const discounts = service.getAppliedDiscounts(cart);
    expect(discounts).toHaveLength(1);
    expect(discounts[0].product).toBe('Product A');
  });
});
```

## Tu tarea

1. **Lee el diff completo** usando el método de 3 pasadas
2. **Identifica la regresión**: algo que funcionaba antes ahora está roto
3. Responde a estas preguntas:
   - ¿Qué funcionalidad se rompe?
   - ¿En qué método específico está la regresión?
   - ¿Por qué el agente introdujo este cambio?
   - ¿Cómo lo corregirías?

## Pista

La feature de descuentos funciona correctamente. La regresión está en cómo un método existente cambió su comportamiento para "adaptarse" a los descuentos.

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Identificar que la regresión existe y en qué área está |
| **Notable** | Identificar el método exacto y explicar el problema |
| **Excelente** | Explicar por qué el agente lo hizo, proponer corrección y un test de regresión |

---

[← Ejercicio anterior](01-review-pr-ia.md) | [Siguiente ejercicio: Poda de complejidad →](03-poda-complejidad.md)
