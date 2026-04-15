# Regresiones Silenciosas — El Peligro Más Grave

## Qué Son

Una regresión silenciosa ocurre cuando el agente resuelve correctamente el problema que le pediste, pero **rompe algo en otro lugar del código** que nadie verifica. El cambio pasa revisión porque el foco está en lo que se arregló, no en lo que se rompió.

Es el tipo de fallo más peligroso porque:
- El código compila y ejecuta sin errores
- Los tests del área modificada pasan
- El bug introducido se manifiesta en otra parte del sistema, a veces días después

---

## Por Qué Ocurren

### 1. Contexto limitado del agente

El agente no "ve" todo el codebase a la vez. Cuando modifica una función, puede no saber que esa función es llamada desde 12 archivos diferentes con expectativas distintas.

### 2. Funciones compartidas

Las funciones utilitarias, helpers y módulos compartidos son los puntos de mayor riesgo. Un cambio en la firma o el tipo de retorno puede afectar a todos los consumidores:

```javascript
// Antes: retorna un número
function calculateTotal(items) {
  return items.reduce((sum, item) => sum + item.price, 0);
}

// El agente "mejora" la función para retornar un objeto
function calculateTotal(items) {
  const total = items.reduce((sum, item) => sum + item.price, 0);
  return { total, itemCount: items.length, currency: "USD" };
}

// 5 archivos que hacían esto ahora están ROTOS:
const price = calculateTotal(cartItems);
display.textContent = `$${price.toFixed(2)}`;  // ERROR: price.toFixed is not a function
```

### 3. Refactors de nombres y firmas

Renombrar funciones o cambiar el orden de parámetros puede romper código que el agente no revisó.

---

## 3 Ejemplos Reales

### Ejemplo 1: Cambio de formato en endpoint

**Pedido**: "El endpoint /api/users devuelve un error cuando no hay usuarios. Arréglalo."

**Lo que hizo el agente**: cambió la respuesta de array vacío a un objeto con metadatos:

```javascript
// Antes
app.get("/api/users", (req, res) => {
  const users = db.getUsers();
  res.json(users);  // Retornaba [] cuando no había usuarios
});

// Después (fix del agente)
app.get("/api/users", (req, res) => {
  const users = db.getUsers();
  res.json({ data: users, count: users.length, success: true });
});
```

**Regresión**: el frontend esperaba un array directo y hacía `response.map()`. Al recibir un objeto, falla silenciosamente mostrando una lista vacía en lugar de error.

### Ejemplo 2: Refactor de utilidad

**Pedido**: "Refactoriza `formatDate` para que soporte timestamps Unix además de objetos Date."

```javascript
// Antes
function formatDate(date) {
  return date.toISOString().split("T")[0];  // "2024-03-15"
}

// Después (refactor del agente)
function formatDate(input) {
  const date = typeof input === "number" ? new Date(input) : input;
  return date.toLocaleDateString("en-US");  // "3/15/2024" — formato diferente
}
```

**Regresión**: cambió el formato de salida de `YYYY-MM-DD` a `M/D/YYYY`. Todos los archivos que parseaban o comparaban el string de fecha ahora fallan silenciosamente.

### Ejemplo 3: Actualización de dependencia

**Pedido**: "Actualiza la librería de validación a la última versión."

```javascript
// Antes (v2.x)
const { validate } = require("class-validator");
const errors = validate(userInput);
if (errors.length > 0) { /* manejar errores */ }

// Después (v3.x — breaking change)
const { validate } = require("class-validator");
const errors = await validate(userInput);  // Ahora es async
if (errors.length > 0) { /* manejar errores */ }
```

**Regresión**: el agente actualizó solo el archivo que le indicaste, pero hay 8 archivos más que llaman a `validate()` sin `await`, y ahora reciben una Promise en lugar de un array.

---

## Cómo Prevenir Regresiones

### 1. Tests como red de seguridad

Si no hay tests, el **primer paso** antes de cualquier cambio es añadir tests al código existente:

```text
Antes de modificar la función calculateTotal, escribe tests que cubran
su comportamiento actual. Luego haz el cambio y verifica que los tests
siguen pasando.
```

### 2. Ejecutar la test suite completa

No solo los tests del área modificada — **toda la suite**: `npm test` (no `npm test -- --testPathPattern=...`).

### 3. Identificar callers antes de modificar

```text
Antes de modificar la función formatDate, búscame todos los archivos
que la importan o la llaman. Verifica que el cambio no rompe ninguno.
```

### 4. Revisar el diff completo con git

Ejecuta `git diff --stat`. Si solo se modificó 1 archivo pero la función se usa en 10, probablemente faltan cambios.

### 5. La pregunta clave

> "¿Quién más usa esto? ¿Y qué espera recibir?"

Si no puedes responder con certeza, no aceptes el cambio hasta verificar.

---

## Resumen

- Las regresiones silenciosas son cambios que arreglan lo pedido pero rompen algo en otro lugar
- Son el fallo más peligroso porque pasan desapercibidos en la revisión normal
- Las causas principales son: contexto limitado, funciones compartidas y refactors de firmas
- Tu defensa: tests previos, suite completa, identificar callers y revisar el diff
- Si el agente modifica código compartido sin verificar los consumidores, detén y pide verificación
