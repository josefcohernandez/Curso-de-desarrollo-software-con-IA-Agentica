# Over-engineering — Cuando el Agente Hace Demasiado

## Qué Es

Over-engineering es cuando el agente añade complejidad no solicitada: abstracciones prematuras, error handling defensivo excesivo, helpers innecesarios, features no pedidas. El código resultante funciona, pero es **innecesariamente complejo** para el problema que resuelve.

Es el tipo de fallo **más frecuente** de los agentes de código. No es malicioso ni aleatorio — tiene una causa clara.

---

## Por Qué Ocurre

Los modelos de lenguaje están entrenados con código de repositorios públicos donde abundan:
- Abstracciones "bien diseñadas" con interfaces genéricas
- Error handling exhaustivo para todo caso posible
- Patrones de diseño aplicados en cada oportunidad
- Documentación detallada en cada función

El agente reproduce estos patrones porque en su training data representan "código de calidad". Sin restricciones explícitas, **tiende a sobre-construir** porque no distingue entre "esto debería ser robusto" y "esto ya está en un contexto controlado".

---

## 5 Señales de Alarma

### 1. Archivo utils innecesario

Le pides una función simple y crea un archivo separado con funciones extra:

```javascript
// Pediste: "Añade una función que formatee precios"
// El agente crea src/utils/formatting.js con 3 funciones exportadas
export function formatPrice(amount, currency = "EUR", locale = "es-ES") { /* ... */ }
export function formatPercentage(value, decimals = 2) { /* ... */ }
export function formatDate(date, format = "long") { /* ... */ }

// Lo que necesitabas: una línea en el archivo donde se usa
const formatted = `${price.toFixed(2)} €`;
```

### 2. Try/catch para código que no puede fallar

```javascript
// El agente envuelve operaciones seguras en try/catch
function getFullName(user) {
  try {
    const first = user.firstName || "";
    const last = user.lastName || "";
    return `${first} ${last}`.trim();
  } catch (error) {
    console.error("Error formatting user name:", error);
    return "Unknown User";
  }
}

// Si user existe (validado antes de llamar), esto NUNCA falla
// Lo necesario:
function getFullName(user) {
  return `${user.firstName || ""} ${user.lastName || ""}`.trim();
}
```

### 3. Interfaces genéricas para una sola implementación

```javascript
// Pediste: "Crea un servicio que envíe emails con SendGrid"
// El agente genera: clase base, implementación, y wrapper — 3 clases
class EmailProvider { async send() { throw new Error("Not implemented"); } }
class SendGridProvider extends EmailProvider { async send() { /* real */ } }
class EmailService {
  constructor(provider) { this.provider = provider; }
  async send(to, subject, body) { return this.provider.send(to, subject, body); }
}

// Solo tienes UN proveedor. Todo se reduce a:
async function sendEmail(to, subject, body) { /* implementación directa */ }
```

### 4. "Mejoras" no solicitadas

```text
Prompt: "Añade validación de email al formulario de registro"

El agente además:
- Refactoriza todo el formulario para usar un custom hook
- Añade debounce a todos los inputs (no solo al email)
- Crea un sistema de mensajes de error i18n-ready
- Implementa auto-save del formulario en localStorage
```

### 5. JSDoc en funciones privadas triviales

```javascript
/**
 * Checks if a value is a non-empty string.
 * @param {unknown} value - The value to check
 * @returns {boolean} True if the value is a non-empty string
 */
function isNonEmptyString(value) {
  return typeof value === "string" && value.length > 0;
}
// 8 líneas de documentación para 1 línea de lógica obvia
```

---

## Cómo Prevenir

### 1. Ser explícito en el prompt

```text
Implementa SOLO la validación de email. No refactorices código existente.
No crees archivos nuevos — añade la lógica directamente en el archivo
del formulario.
```

### 2. Incluir reglas en CLAUDE.md

```markdown
## Reglas de diseño

- No añadas abstracciones para un solo uso
- No añadas error handling para casos que no pueden ocurrir en este contexto
- No modifiques código fuera del scope pedido
- Tres líneas similares son mejores que una abstracción prematura
- No crees archivos utils/ para funciones usadas en un solo lugar
- No añadas JSDoc a funciones privadas cuyo nombre ya explica su propósito
```

### 3. Revisar el diff antes de aceptar

El indicador más fiable es el **tamaño del diff**: ejecuta `git diff --stat`. Si pediste un cambio de 5 líneas y el diff muestra 80 en 4 archivos, investiga. Pregúntate: "¿Pedí esto?", "¿Cuántos archivos tocó?" y "¿Hay código nuevo usado en un solo lugar?".

---

## La Regla Práctica

> Si el agente generó más del doble de líneas de las que esperabas, revisa qué sobra antes de aceptar. El código más fácil de mantener es el que no existe.

---

## Resumen

- Over-engineering es el fallo más frecuente de los agentes de código
- Ocurre por sesgo del training data hacia código "robusto" y "completo"
- Las 5 señales: utils innecesarios, try/catch excesivo, interfaces genéricas, mejoras no pedidas, documentación excesiva
- Prevenir: prompts explícitos, reglas en CLAUDE.md, revisión del diff
- Si el diff es mucho mayor de lo esperado, investiga antes de aceptar
