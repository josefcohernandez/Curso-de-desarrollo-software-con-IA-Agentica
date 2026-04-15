# Ejercicio 3: Rescue de Prompt Fallido

## Objetivo

Dado un prompt que produjo resultados incorrectos, diagnosticar **por qué** falló y reescribirlo aplicando las técnicas del módulo. Este ejercicio entrena la habilidad de analizar fallos de prompting — una habilidad que usarás cada día.

**Tiempo estimado: 15 minutos**

---

## Escenario 1: El Refactor Destructivo

### Prompt original

```text
Refactoriza el archivo src/utils/helpers.ts, está muy desordenado.
```

### Lo que hizo el agente

- Eliminó 4 funciones que consideró "redundantes" (pero se usaban en otros archivos)
- Renombró 6 funciones siguiendo una convención diferente a la del proyecto
- Movió algunas funciones a un nuevo archivo `src/utils/string-helpers.ts`
- 8 tests empezaron a fallar por las funciones eliminadas y renombradas

### Diagnóstico

Responde estas preguntas:

1. Qué componentes del prompt faltaban? (Revisa los 5 componentes de la [teoría 02](../teoria/02-anatomia-buen-prompt.md))
2. Qué anti-patrón de prompting se cometió?
3. Por qué el agente eliminó funciones que se usaban?

### Tu tarea

Reescribe el prompt para que el refactor sea seguro. Incluye:
- Objetivo claro del refactor
- Restricciones que prevengan la eliminación de funciones usadas
- Cómo verificar que nada se rompió
- Uso de Plan Mode

---

## Escenario 2: La Feature que Crece Sin Control

### Prompt original

```text
Add a comments system to tasks. Users should be able to add comments,
edit them, delete them, and also add reactions to comments like thumbs up,
heart, etc. Also add mentions (@user) in comments that send notifications.
And make sure comments are paginated and support rich text with markdown.
```

### Lo que hizo el agente

- Creó un modelo Comment con 15 campos
- Añadió 8 nuevos endpoints
- Implementó un sistema de reacciones con tabla propia
- Añadió parsing de mentions con regex
- Integró una librería de markdown a HTML
- El PR resultante tiene 47 archivos modificados y 1,200 líneas nuevas
- Hay inconsistencias entre los endpoints (algunos paginan, otros no)
- Las notificaciones de mentions no funcionan correctamente

### Diagnóstico

Responde estas preguntas:

1. Cuántas tareas distintas hay en este prompt?
2. Qué anti-patrón de prompting es este?
3. Por qué las inconsistencias son un resultado predecible de este prompt?

### Tu tarea

Descompón este prompt en una secuencia de prompts independientes. Escribe el primero de la secuencia (el más básico: CRUD de comentarios) usando el patrón Add Feature. Luego lista los prompts siguientes como títulos de una línea.

---

## Escenario 3: El Bug que No Se Resuelve

### Prompt original

```text
There's a bug in the payment system. Fix it.
```

### Intercambio con el agente (resumido)

```text
Agente: "I've looked at the payment code and found several potential issues.
I've refactored the error handling in payment-service.ts and added null checks."

Usuario: "No, that's not the bug. The bug is with the amounts."

Agente: "I see. I've updated the amount calculation to use BigNumber
for precision. I also added rounding to 2 decimal places."

Usuario: "No! The bug is that discounts aren't applied."

Agente: "I've added discount application logic to the calculateTotal function."

Usuario: "The discount logic already existed, it's just not being called."
```

Después de 4 iteraciones, el agente sigue sin resolver el bug.

### Diagnóstico

Responde estas preguntas:

1. Qué información crítica faltaba en el prompt original?
2. En qué punto debería el usuario haber hecho `/clear` y replanteado?
3. Qué regla de la [teoría 06](../teoria/06-iteracion-correccion.md) se violó?

### Tu tarea

Escribe el prompt que debería haberse escrito desde el principio, usando el patrón Fix Bug. Incluye el error exacto, la reproducción, y dónde está el código relevante. Asume que el bug es: la función `applyDiscount()` en `src/services/payment-service.ts` existe pero nunca se llama desde `calculateTotal()`.

---

## Criterios de Evaluación

Para cada escenario:

| Criterio | Puntos |
|----------|--------|
| Diagnóstico correcto (identifica el problema del prompt original) | 3 |
| Prompt reescrito incluye los 5 componentes relevantes | 3 |
| El nuevo prompt previene el fallo específico que ocurrió | 2 |
| El scope está correctamente acotado | 2 |

**Puntuación total**: 30 puntos (10 por escenario).

---

## Reflexión

1. En cuál de los 3 escenarios te has visto reflejado?
2. Qué patrón de fallo cometes más frecuentemente: prompt vago, scope creep, o falta de contexto?
3. Cómo vas a cambiar tu forma de escribir prompts a partir de ahora?

---

## Siguiente

Continúa con [Ejercicio 04: Cookbook Personal](04-cookbook-personal.md).
