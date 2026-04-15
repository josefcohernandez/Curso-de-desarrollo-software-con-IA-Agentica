# Workflow de debugging con agente

## Los 4 pasos en detalle

Este es el workflow completo con prompt templates para cada fase. Sigue los pasos en orden — la tentación de saltar directamente al fix es la causa principal de debugging ineficiente.

---

## Paso 1: Reproducir

### Objetivo

Confirmar que puedes reproducir el bug consistentemente y recopilar toda la información necesaria para el agente.

### Antes de hablar con el agente

Asegúrate de tener:

- El **error exacto** (mensaje de error completo, no parafraseado)
- El **stack trace** completo (si existe)
- Los **pasos para reproducir** (numerados, específicos)
- El **entorno**: versiones de lenguaje, OS, base de datos, etc.
- **Qué esperabas** que ocurriera vs qué ocurrió realmente

### Prompt template

```text
Este error ocurre al ejecutar [comando/acción]:

[pegar error/stack trace completo]

Pasos para reproducir:
1. [paso 1]
2. [paso 2]
3. [paso 3]

Entorno: [Node 20 / Python 3.12 / etc.], [macOS / Linux], [PostgreSQL 15 / MongoDB 7]

Comportamiento esperado: [qué debería pasar]
Comportamiento actual: [qué pasa realmente]
```

### Ejemplo real

```text
Este error ocurre al intentar crear un pedido desde el frontend:

TypeError: Cannot read properties of undefined (reading 'map')
    at OrderSummary (src/components/OrderSummary.tsx:23:41)
    at renderWithHooks (node_modules/react-dom/cjs/react-dom.development.js:14985:18)
    at mountIndeterminateComponent (node_modules/react-dom/cjs/react-dom.development.js:17811:13)

Pasos para reproducir:
1. Login como usuario regular (no admin)
2. Añadir 2 productos al carrito
3. Ir a checkout
4. Click en "Confirmar pedido"

Entorno: Node 20, React 18, macOS, PostgreSQL 15

Comportamiento esperado: se crea el pedido y se muestra la confirmación
Comportamiento actual: pantalla en blanco con el error anterior en la consola
```

### Lo que NO hacer

- No parafrasear el error: "me sale un error de tipos" — pega el error exacto
- No omitir el stack trace: es la pista más valiosa
- No asumir dónde está el problema: "creo que es en el backend" — deja que la evidencia lo diga

---

## Paso 2: Aislar

### Objetivo

Identificar el scope del problema y generar hipótesis antes de buscar la causa raíz.

### Prompt template

```text
Investiga este error. No lo arregles todavía.
Dime:
1. Qué archivos están involucrados
2. Cuál es la cadena de llamadas (call chain) que lleva al error
3. Cuáles son las 2-3 hipótesis más probables para la causa raíz
4. Para cada hipótesis, qué evidencia la apoyaría o descartaría
```

### Por qué "no lo arregles todavía"

Esta instrucción es crucial. Sin ella, el agente tiende a:
1. Leer el stack trace superficialmente
2. Proponer un fix rápido para el síntoma
3. Introducir una solución que enmascara el problema real

Al pedirle que **solo investigue**, le obligas a pensar antes de actuar — exactamente el mismo principio que aplicamos con los humanos.

### Ejemplo de respuesta esperada

Un buen agente debería responder algo como:

```text
Archivos involucrados:
- src/components/OrderSummary.tsx (donde falla)
- src/hooks/useOrder.ts (donde se obtienen los datos)
- src/api/orderApi.ts (llamada al backend)
- src/controllers/orderController.ts (endpoint del backend)

Cadena de llamadas:
OrderSummary.tsx → useOrder() → orderApi.createOrder() → backend /api/orders

Hipótesis:
1. El backend devuelve un formato diferente al esperado
   - Evidencia: comparar el tipo esperado en el frontend con la respuesta real del API
2. El hook useOrder no maneja el estado de loading/error
   - Evidencia: verificar si hay un estado intermedio donde data es undefined
3. El endpoint cambió su formato de respuesta recientemente
   - Evidencia: revisar commits recientes en orderController.ts
```

---

## Paso 3: Diagnosticar

### Objetivo

Verificar cada hipótesis sistemáticamente hasta confirmar la causa raíz.

### Prompt template (por hipótesis)

```text
Hipótesis: [descripción de la hipótesis]
Verifica esta hipótesis:
1. [qué comprobar específicamente]
2. [qué resultado confirmaría la hipótesis]
3. [qué resultado la descartaría]
```

### Ejemplo

```text
Hipótesis: el backend devuelve un formato diferente al esperado.
Verifica:
1. Compara la interfaz OrderResponse del frontend con lo que devuelve orderController.create()
2. Si los campos no coinciden, esa es la causa
3. Si coinciden exactamente, descartamos esta hipótesis y pasamos a la siguiente
```

### Iterar hasta confirmar

```text
┌─────────────────┐
│ Hipótesis 1     │──── Confirmada? ──── Sí ──── Pasar al Fix
│                 │          │
│                 │          No
│                 │          │
│                 │          ▼
│ Hipótesis 2     │──── Confirmada? ──── Sí ──── Pasar al Fix
│                 │          │
│                 │          No
│                 │          │
│                 │          ▼
│ Hipótesis 3     │──── Confirmada? ──── Sí ──── Pasar al Fix
│                 │          │
│                 │          No
│                 │          │
│                 │          ▼
│ Pedir nuevas    │
│ hipótesis al    │
│ agente          │
└─────────────────┘
```

### Prompt para nuevas hipótesis

```text
Las hipótesis anteriores se han descartado:
- Hipótesis 1: descartada porque [razón]
- Hipótesis 2: descartada porque [razón]

Con esta nueva información, propón 2-3 hipótesis alternativas.
```

---

## Paso 4: Fix + Validar

### Objetivo

Implementar la corrección solo después de confirmar la causa raíz, y validar que no introduce regresiones.

### Prompt template

```text
La causa confirmada es: [descripción precisa de la causa raíz]

Implementa el fix. Verifica que:
1. El caso original ya no falla
2. Los tests existentes siguen pasando
3. El edge case [caso específico] también está cubierto
4. Añade un test de regresión que falle sin el fix y pase con él
```

### La importancia del test de regresión

El test de regresión es la pieza más valiosa del debugging. Garantiza que este bug concreto no vuelva a ocurrir:

```javascript
// Test de regresión: verifica que el bug #247 no regresa
test('should handle order response with empty items array (bug #247)', async () => {
  // El backend puede devolver items: [] para pedidos cancelados
  mockApi.createOrder.mockResolvedValue({ id: '1', items: [], status: 'cancelled' });
  
  const { result } = renderHook(() => useOrder());
  await act(() => result.current.createOrder(orderData));
  
  // Antes del fix, esto causaba: TypeError: Cannot read properties of undefined (reading 'map')
  expect(result.current.error).toBeNull();
  expect(result.current.order.items).toEqual([]);
});
```

### Validación final

Antes de dar el bug por resuelto:

```text
Resume lo que hicimos:
1. Cuál era el bug
2. Cuál era la causa raíz
3. Qué fix se aplicó
4. Qué tests se añadieron
5. ¿Hay algún otro lugar del código que pueda tener el mismo problema?
```

Esta última pregunta es clave: si el mismo patrón defectuoso existe en otros lugares, hay que corregirlos todos.

---

[← Anterior: El debugging amplificado](01-debugging-amplificado.md) | [Siguiente: Análisis de logs con IA →](03-analisis-logs.md)
