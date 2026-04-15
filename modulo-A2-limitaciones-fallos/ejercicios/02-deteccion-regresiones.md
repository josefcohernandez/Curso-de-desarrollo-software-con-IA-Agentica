# Ejercicio 2: Detección de Regresiones

## Objetivo

Analizar un diff que "arregla un bug" en una función de cálculo de descuentos y encontrar la regresión silenciosa que introduce. Luego, escribir un test que la habría detectado.

**Tiempo estimado: 10 minutos**

---

## Código Original

La función `calculateDiscount` y sus consumidores llevan 6 meses en producción sin problemas:

```javascript
// pricing.js
function calculateDiscount(price, discountPercent) {
  if (discountPercent < 0 || discountPercent > 100) {
    return price; // Sin descuento si el porcentaje es inválido
  }
  return price - (price * discountPercent / 100);
}

module.exports = { calculateDiscount };
```

```javascript
// cart.js — consumidor 1
const { calculateDiscount } = require("./pricing");

function getCartTotal(items, discountPercent) {
  let total = 0;
  for (const item of items) {
    total += calculateDiscount(item.price, discountPercent);
  }
  return total.toFixed(2); // Formatea como string con 2 decimales
}
```

```javascript
// invoice.js — consumidor 2
const { calculateDiscount } = require("./pricing");

function generateInvoiceLine(product, discountPercent) {
  const finalPrice = calculateDiscount(product.price, discountPercent);
  const tax = finalPrice * 0.21; // 21% IVA
  return {
    product: product.name,
    originalPrice: product.price,
    discount: product.price - finalPrice,
    finalPrice: finalPrice,
    tax: tax,
    totalWithTax: finalPrice + tax
  };
}
```

---

## El Bug Reportado

Un tester reporta: "Cuando paso `discountPercent = 0`, la función debería devolver el precio original, pero no queda claro en el código que 0% es un caso válido. Además, no se valida que `price` sea un número positivo."

---

## El Diff del Agente

El agente propone este fix:

```diff
 // pricing.js
-function calculateDiscount(price, discountPercent) {
-  if (discountPercent < 0 || discountPercent > 100) {
-    return price; // Sin descuento si el porcentaje es inválido
-  }
-  return price - (price * discountPercent / 100);
+function calculateDiscount(price, discountPercent) {
+  if (typeof price !== "number" || price < 0) {
+    return { finalPrice: 0, discount: 0, error: "Precio inválido" };
+  }
+  if (typeof discountPercent !== "number" || discountPercent < 0 || discountPercent > 100) {
+    return { finalPrice: price, discount: 0, error: "Descuento inválido" };
+  }
+  const discount = price * discountPercent / 100;
+  return { finalPrice: price - discount, discount, error: null };
 }
```

---

## Tu Tarea

1. **Identifica la regresión**: el diff arregla el bug reportado, pero introduce un cambio que rompe los consumidores. Describe exactamente qué se rompe y en qué archivo.

2. **Explica el impacto**: para cada consumidor (`cart.js` e `invoice.js`), describe qué error se producirá o qué comportamiento incorrecto habrá.

3. **Escribe un test** que habría detectado la regresión antes de hacer merge:

```javascript
// test/pricing.test.js — completa este test
const { calculateDiscount } = require("../pricing");

describe("calculateDiscount", () => {
  test("debe retornar un número, no un objeto", () => {
    // TU CÓDIGO AQUÍ
    // Este test fallaría con el nuevo código y pasaría con el original
  });

  test("calcula correctamente un descuento del 20%", () => {
    // TU CÓDIGO AQUÍ
  });

  test("retorna el precio original con descuento 0%", () => {
    // TU CÓDIGO AQUÍ
  });
});
```

---

## Criterio de Éxito

- **Regresión identificada**: la función cambió el tipo de retorno de `number` a `object`
- **Impacto descrito**: `cart.js` falla en `.toFixed(2)` (no puedes llamar `.toFixed` en un objeto), `invoice.js` calcula mal el IVA (multiplica un objeto por 0.21, dando `NaN`)
- **Test escrito**: al menos un test que verifique que el retorno es un número

---

## Reflexión

Esta regresión es especialmente peligrosa porque:

- El agente hizo exactamente lo que se pidió (mejorar la validación)
- El nuevo código es objetivamente "mejor" en aislamiento
- Pero **cambió el contrato** de la función sin verificar quién la consume
- Si no existieran tests previos, este cambio habría llegado a producción
