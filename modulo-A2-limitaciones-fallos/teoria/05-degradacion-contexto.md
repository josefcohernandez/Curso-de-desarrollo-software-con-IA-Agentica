# Degradación por Contexto Saturado

## Qué Es

A medida que una conversación con un agente de código se alarga, la calidad de las respuestas **se deteriora progresivamente**. El agente empieza a olvidar instrucciones, contradecir decisiones previas y generar código inconsistente. Este fenómeno se llama degradación por contexto saturado.

No es un bug — es una limitación fundamental de cómo funcionan los modelos de lenguaje. La ventana de contexto es finita, y cuando se llena, la información más antigua pierde influencia en las respuestas.

---

## Cómo Se Manifiesta

### 1. Contradice decisiones anteriores

```text
Intercambio #5:  "Usaremos PostgreSQL como base de datos principal"
Intercambio #35: "He configurado la conexión a MongoDB para persistir los datos"
```

El agente tomó una decisión explícita al principio de la sesión y la ignora completamente después, sin avisar del cambio.

### 2. Duplica código existente

El agente crea una nueva función `validateEmail()` cuando ya implementó una idéntica 20 mensajes antes. No recuerda que ya existe, así que la reimplementa con un nombre ligeramente diferente.

### 3. Ignora restricciones del prompt inicial

```text
Prompt inicial: "No uses librerías externas. Solo JavaScript nativo."
Intercambio #40: import axios from "axios";
```

Las instrucciones del primer mensaje pierden peso a medida que se acumula más contexto posterior.

### 4. La calidad baja perceptiblemente

Las respuestas se vuelven más genéricas, menos adaptadas al proyecto específico. El agente empieza a dar soluciones "de libro" en lugar de soluciones contextualizadas.

---

## Cuándo Ocurre

La degradación no tiene un punto exacto, pero típicamente se manifiesta:

| Situación | Riesgo de degradación |
|-----------|----------------------|
| 10-20 intercambios con poco código | Bajo |
| 20-30 intercambios normales | Medio — empieza a aparecer |
| 30-50 intercambios o muchos archivos leídos | Alto — muy probable |
| 50+ intercambios | Casi seguro — calidad comprometida |

Factores que **aceleran** la degradación:
- Leer muchos archivos grandes (cada `Read` consume contexto)
- Pegar snippets largos de documentación o logs
- Cambiar de tema frecuentemente dentro de la misma sesión
- Múltiples iteraciones de corrección sobre el mismo código

---

## Técnicas de Prevención

### 1. Reset de contexto entre tareas

La técnica más efectiva. Cada tarea independiente debería ser una sesión limpia:

```text
# Tarea 1: implementar endpoint de usuarios
[... 15 intercambios ...]
[reinicia la sesión o usa el comando de limpieza de contexto]

# Tarea 2: corregir bug en autenticación
[empieza con contexto fresco]
```

### 2. Compactación con instrucciones

Cuando necesitas mantener la sesión pero el contexto se está llenando, usa la función de compactación de tu herramienta o resume manualmente la conversación. Incluye instrucciones sobre qué preservar:

```text
[compacta o resume preservando]: estamos usando PostgreSQL, la estructura de la API sigue
REST, el archivo principal es src/server.ts, y acabamos de terminar el
módulo de usuarios.
```

### 3. Subagentes para investigación

En herramientas que soportan subagentes, delega las tareas de investigación a un agente secundario. Esto mantiene el contexto principal limpio:

```text
Usa un subagente para buscar todos los archivos que importan el módulo
de autenticación y dame un resumen de cómo lo usan.
```

El subagente consume su propio contexto, y solo el resumen vuelve a tu sesión principal.

### 4. Dividir tareas largas en fases

En lugar de una sesión maratón, estructura el trabajo en fases con checkpoints:

```text
Fase 1: Diseño de la estructura de datos (10-15 intercambios → sesión nueva)
Fase 2: Implementación del backend (10-15 intercambios → sesión nueva)
Fase 3: Tests e integración (10-15 intercambios → sesión nueva)
```

Al iniciar cada fase, reinyecta solo el contexto necesario:

```text
Estamos en la Fase 2. En la Fase 1 decidimos:
- Esquema de base de datos: [resumen breve]
- Endpoints necesarios: [lista]
- Formato de respuesta: [ejemplo]

Implementa el primer endpoint: GET /api/users
```

### 5. La regla de los 20 minutos

> Si llevas más de 20 minutos en la misma sesión sin reiniciar o limpiar contexto, probablemente deberías hacerlo.

Esta regla heurística funciona porque 20 minutos de interacción activa suelen corresponder a 25-35 intercambios, que es la zona donde empieza la degradación.

---

## Señales de Que Debes Reiniciar la Sesión

Detente y limpia la sesión si observas cualquiera de estas señales:

- El agente propone algo que contradice una decisión anterior de la sesión
- Genera código que ya existe en el proyecto (duplicación)
- Las respuestas se sienten más genéricas o menos precisas que al principio
- El agente "olvida" restricciones que le diste al inicio
- Recibes errores de contexto lleno o respuestas truncadas

---

## Resumen

- La degradación por contexto es inevitable en sesiones largas — no es un bug, es una limitación
- Se manifiesta como contradicciones, duplicaciones, ignorar restricciones y baja de calidad
- Empieza a ser notable tras 30-50 intercambios o cuando se leen muchos archivos
- Tu arsenal: sesiones nuevas entre tareas, compactación o resumen con instrucciones, subagentes y fases separadas
- La regla de los 20 minutos es tu mejor heurística: si llevas más de 20 minutos, limpia
