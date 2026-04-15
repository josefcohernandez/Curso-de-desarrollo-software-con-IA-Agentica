# Iteración y Corrección de Rumbo

## Introducción

El prompt perfecto al primer intento es la excepción, no la regla. El desarrollo con agentes de código es **iterativo**: escribes un prompt, evalúas el resultado, y ajustas. La habilidad no está en acertar a la primera, sino en saber **cómo y cuándo corregir** de forma eficiente.

La diferencia entre un usuario novato y uno experto no es que el experto escribe prompts perfectos — es que el experto corrige en 1-2 iteraciones lo que al novato le toma 5-6.

---

## Patrón de Corrección Progresiva

Cuando recibes un resultado del agente, evalúa qué porcentaje es correcto y actúa según este esquema:

| Porcentaje correcto | Estrategia | Ejemplo de acción |
|---------------------|------------|-------------------|
| **80-100%** | Corrección puntual | "Cambia el nombre de la variable `data` a `userProfile` en la línea 23" |
| **50-80%** | Refinamiento del enfoque | "El enfoque es correcto, pero necesito que además manejes el caso cuando el array está vacío" |
| **20-50%** | Redirect parcial | "La estructura del componente está bien, pero la lógica de estado es incorrecta. Usa useReducer en lugar de useState para manejar los 3 estados" |
| **< 20%** | **Parar. Limpiar. Replantear** | Haz `/clear`, analiza qué falló en tu prompt, y reescríbelo desde cero |

### Ejemplo: corrección al 80%

```text
# Resultado del agente: creó el endpoint pero olvidó la validación de input

Bien, el endpoint funciona. Añade validación con zod para el body:
- email: string, formato email válido
- name: string, mínimo 2 caracteres, máximo 100
- age: number, opcional, entre 18 y 120
Si la validación falla, devuelve 400 con los errores específicos.
```

### Ejemplo: parar y replantear (< 20%)

```text
# El agente implementó algo completamente diferente a lo esperado.
# No corrijas — el contexto está contaminado.

/clear

# Nuevo prompt replanteado con más contexto y restricciones:
Necesito un endpoint POST /api/invitations que...
[prompt completo reescrito con lo aprendido del intento anterior]
```

---

## La Regla de las 2 Correcciones

> Si corriges más de **2 veces** el mismo aspecto del resultado, el problema está en tu prompt original, no en el agente.

Esto es contraintuitivo. Cuando el agente no hace lo que quieres, la reacción natural es decirle "no, hazlo así". Pero si llevas 3 correcciones sobre el mismo tema (estilo de código, enfoque arquitectónico, formato de respuesta), lo que necesitas es:

1. Hacer `/clear` para limpiar el contexto acumulado
2. Analizar qué faltaba en tu prompt original
3. Reescribir el prompt incluyendo esa información desde el principio

### Por qué funciona

El contexto de la conversación acumula tokens con cada iteración. Después de 3-4 correcciones, el agente tiene que reconciliar tu prompt original, su primera implementación, tus correcciones (a veces contradictorias), y su segunda implementación. Es más eficiente empezar limpio.

---

## Feedback Específico vs. Feedback Vago

La calidad de tu feedback determina la calidad de la corrección:

| Feedback vago (ineficaz) | Feedback específico (eficaz) |
|--------------------------|------------------------------|
| "Eso no es lo que quería" | "Quiero que use `async/await`, no callbacks" |
| "Hazlo mejor" | "Extrae la validación a una función separada llamada `validateUserInput`" |
| "No me gusta el estilo" | "Usa early returns en lugar de `if/else` anidados" |
| "Está mal" | "La función `calculateTotal` no aplica el descuento cuando `quantity > 10`" |
| "Repítelo" | "Regenera solo la función `parseConfig`, el resto está bien" |
| "Más simple" | "Reemplaza el switch de 40 líneas por un objeto map: `{ admin: fn1, user: fn2 }`" |

### Patrón para feedback efectivo

```text
[Qué está bien] + [Qué cambiar] + [Cómo cambiarlo]
```

Ejemplo:

```text
La estructura del componente y el manejo de errores están bien.
Cambia el fetching de datos: en lugar de useEffect + fetch,
usa react-query con la misma URL. Ya tenemos react-query configurado
en src/lib/query-client.ts.
```

---

## Cuándo Usar `/clear`

El comando `/clear` (o equivalente en tu herramienta) reinicia el contexto de la conversación. Úsalo cuando:

| Situación | Por qué limpiar |
|-----------|-----------------|
| Llevas 3+ correcciones sobre lo mismo | El contexto acumulado confunde más que ayuda |
| Cambias de tarea completamente | El contexto de la tarea anterior es ruido |
| El agente entra en un bucle | Sigue repitiendo el mismo error tras cada corrección |
| La conversación es muy larga | La calidad degrada cuando el contexto se acerca al límite |
| El primer intento fue desastroso (< 20%) | Mejor empezar limpio que corregir desde una base mala |

---

## Estrategia de Iteración Eficiente

### Paso 1: Evalúa antes de reaccionar

No corrijas inmediatamente. Lee todo el resultado del agente, verifica si los tests pasan, y **luego** decide qué corregir.

### Paso 2: Agrupa las correcciones

Si necesitas 3 cambios, ponlos todos en un solo mensaje de feedback, no en 3 mensajes separados:

```text
Tres ajustes al resultado:
1. En src/auth.ts:15 — usa bcrypt.compare() en lugar de comparación directa
2. En src/auth.ts:28 — añade try/catch al bloque de verificación JWT
3. En test/auth.test.ts — añade un test para el caso de password vacío
```

### Paso 3: Verifica después de la corrección

```text
Tras los cambios, ejecuta npm test -- test/auth.test.ts y reporta.
```

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| Corrección progresiva | Evalúa el % correcto y ajusta la estrategia |
| Regla de las 2 correcciones | Más de 2 correcciones al mismo aspecto = reescribir el prompt |
| Feedback específico | Qué está bien + qué cambiar + cómo cambiarlo |
| `/clear` | Limpiar contexto cuando la conversación se contamina |
| Agrupar correcciones | Un mensaje con todos los ajustes, no varios mensajes |

---

## Siguiente

Ya completaste toda la teoría del Módulo A1. Continúa con los [ejercicios prácticos](../ejercicios/01-comparativa-prompts.md) para poner en práctica lo aprendido.
