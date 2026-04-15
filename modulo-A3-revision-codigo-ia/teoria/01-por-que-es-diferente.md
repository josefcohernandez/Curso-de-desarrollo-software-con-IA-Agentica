# Por qué la revisión de código IA es diferente

## El problema fundamental

El código generado por IA se **lee bien**. Se compila. Pasa el linter. Tiene nombres de variables razonables. Y eso es exactamente lo que lo hace peligroso.

Cuando un junior escribe código malo, suele ser **obvio**: typos, nombres confusos, estructuras desordenadas. Cuando un agente de código escribe código malo, parece **profesional** — pero puede estar sutilmente equivocado.

## Patrones de error: humano vs IA

| Aspecto | Errores humanos | Errores de IA |
|---------|----------------|---------------|
| **Tipo** | Puntuales y localizados | Coherentes internamente, incorrectos externamente |
| **Visibilidad** | Suelen ser evidentes al leer | Difíciles de detectar sin ejecutar |
| **Causa raíz** | Typos, off-by-one, copy-paste | Falta de comprensión del contexto de negocio |
| **Ejemplo** | `i <= array.length` (off-by-one) | Función que valida emails pero no sigue las reglas de negocio del proyecto |
| **Consistencia** | Inconsistente — errores aleatorios | Consistente — el mismo error en todo el código generado |

### Ejemplo concreto

Un humano podría escribir:

```javascript
// Error humano: typo evidente
function calcualteTotal(items) {
  let total = 0;
  for (let i = 0; i <= items.length; i++) { // off-by-one
    total += items[i].price;
  }
  return total;
}
```

Un agente IA podría escribir:

```javascript
// Error IA: se lee perfecto, pero ignora la regla de negocio
// de que los items con status 'draft' no deben sumarse
function calculateTotal(items) {
  return items.reduce((total, item) => total + item.price * item.quantity, 0);
}
```

El código del agente es más limpio, más idiomático — y está **equivocado** porque no filtra los items con `status === 'draft'` que el proyecto requiere excluir.

## El riesgo del "rubber stamp"

El **rubber stamp** (sello de goma) es el acto de aprobar código generado por IA porque "se ve bien" sin verificarlo realmente. Es el error más frecuente y más peligroso del desarrollo asistido por IA.

**Estadísticas a considerar:**
- Estudios de GitHub y Accenture indican que los desarrolladores aceptan aproximadamente un **30% de las sugerencias de código IA sin modificaciones** (el ~42% del código total es asistido por IA, pero la tasa de aceptación directa es menor)
- De ese 30%, un porcentaje no trivial contiene bugs sutiles que solo se manifiestan en producción
- La probabilidad de rubber stamp aumenta con la fatiga y la presión de deadlines

### Por qué ocurre

1. **Sesgo de autoridad**: "la IA sabe más que yo" — especialmente en tecnologías que no dominamos
2. **Fatiga de revisión**: después de varias iteraciones, el desarrollador pierde concentración
3. **Presión de velocidad**: "ya tarda menos que hacerlo yo, no voy a perder tiempo revisando"
4. **Aspecto profesional**: el código generado suele tener buen formato, nombres razonables y comentarios — parece correcto

## Revisar código IA no es como revisar código de un junior

| Revisando a un junior | Revisando a un agente IA |
|-----------------------|--------------------------|
| Errores predecibles por nivel de experiencia | Errores impredecibles por falta de comprensión real |
| Puedes asumir intención correcta, implementación incorrecta | No puedes asumir ninguna de las dos |
| Los errores son localizados | Los errores pueden estar distribuidos coherentemente |
| El junior aprende de la revisión | El agente no aprende entre sesiones (sin memoria persistente) |
| Puedes preguntar "¿por qué hiciste esto?" | Puedes preguntar, pero la respuesta es una racionalización post-hoc |

## La mentalidad correcta

No se trata de desconfiar de la IA. Se trata de **verificar con el mismo rigor** que aplicarías a cualquier código — o más.

> **Regla de oro**: si no puedes explicar qué hace cada línea del código generado a un compañero, no deberías aceptarlo.

La revisión de código IA requiere tres ajustes mentales:

1. **Asumir inocencia, verificar todo**: el código puede ser correcto, pero debes comprobarlo
2. **Buscar lo que falta, no solo lo que hay**: la IA a menudo omite edge cases, validaciones o contexto de negocio
3. **Cuestionar lo inesperado**: si aparecen archivos, dependencias o abstracciones que no pediste, pregúntate por qué

---

[Siguiente: Qué buscar en una revisión →](02-que-buscar.md)
