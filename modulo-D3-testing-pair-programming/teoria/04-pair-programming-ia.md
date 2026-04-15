# AI Pair Programming: Patrones de Colaboración en Tiempo Real

## Introducción

Trabajar con un agente de IA no es "pedir y recibir". Es una colaboración en tiempo real con un compañero que tiene capacidades diferentes a las tuyas: puede leer miles de archivos en segundos pero no entiende el contexto de negocio; genera código rápidamente pero puede confundir patrones similares. La clave está en elegir el **modo de colaboración** correcto para cada situación.

---

## Los 5 Modos de Pair Programming con IA

| Modo | Tú haces | La IA hace | Cuándo usarlo |
|------|----------|------------|---------------|
| **Driver/Navigator** | Escribes el código | Observa, sugiere dirección, detecta errores | Código complejo en dominio que conoces bien |
| **Ping-pong TDD** | Escribes el test | Implementa para que pase el test; luego tú escribes el siguiente | Features nuevas con spec clara |
| **Mob programming** | Defines la tarea y supervisas | Implementa de principio a fin | Tareas bien definidas con baja ambigüedad |
| **Investigación asistida** | Preguntas y evalúas respuestas | Explora, busca en el codebase, propone hipótesis | Debugging, exploración de código desconocido |
| **Delegación completa** | Defines la tarea, revisas al final | Todo: planificar, implementar, testear | Tareas repetitivas o perfectamente acotadas |

---

## Modo 1: Driver/Navigator

### Tú eres el driver, la IA es el navigator

Este es el modo más conservador. Tú escribes cada línea de código. La IA te guía, sugiere y detecta problemas.

### Cuándo usarlo

- Estás trabajando en lógica de negocio crítica que requiere tu conocimiento de dominio
- El código es complejo y necesitas pensar cada decisión
- Quieres aprender mientras codeas (la IA explica alternativas)

### Prompts típicos

```text
Estoy implementando el cálculo de impuestos para pedidos internacionales.
Voy a escribir el código paso a paso. En cada paso, revisa lo que escribo 
y sugiere: ¿hay edge cases que no estoy considerando? ¿Hay una forma más 
robusta de hacer esto?
No escribas código por mí, solo guíame.
```

### Señal para cambiar de modo

Si la IA sugiere exactamente lo que ibas a escribir 3+ veces seguidas, el problema es más simple de lo que pensabas. Cambia a **Mob programming** o **Delegación completa**.

---

## Modo 2: Ping-Pong TDD

### Tú escribes tests, la IA implementa

Este es el modo más disciplinado y produce los mejores resultados en features nuevas.

### El ciclo

1. Tú escribes un test que falla (Red)
2. La IA implementa el código mínimo para que pase (Green)
3. Tú revisas y opcionalmente refactorizas (Refactor)
4. Tú escribes el siguiente test
5. Repetir

### Prompt inicial

```text
Vamos a hacer Ping-pong TDD. Yo voy a escribir los tests uno a uno en 
tests/test_pricing.py. Tu trabajo es:
1. Implementar en src/pricing.py el código MÍNIMO para que el test pase
2. No anticipar tests futuros, no añadir funcionalidad extra
3. Después de cada implementación, esperar mi siguiente test

Aquí va el primer test:
[pegar test]
```

### Ventajas de este modo

- Produce código con cobertura del 100% por construcción
- Evita la "sobre-ingeniería anticipatoria" del agente
- El humano mantiene el control de la spec (qué se construye)
- Cada ciclo es verificable y reversible

### Señal para cambiar de modo

Si estás escribiendo tests triviales o repetitivos, cambia a **Delegación completa** para los tests restantes.

---

## Modo 3: Mob Programming

### Tú defines, la IA ejecuta, tú supervisas

Similar a mob programming con humanos: hay un "conductor" (la IA) y un "observador" (tú) que guía a alto nivel.

### Prompt típico

```text
Implementa el endpoint PUT /api/tasks/:id con las siguientes reglas:
- Solo el owner puede editar la tarea
- Validar todos los campos con Zod
- Retornar 404 si no existe, 403 si no es owner, 200 si OK
- Escribir tests de integración para cada caso

Voy a ir supervisando tu progreso. Si algo no me convence, te interrumpo.
```

### Cuándo funciona bien

- La tarea está bien definida (tienes spec, criterios de aceptación)
- El stack tecnológico es estándar (la IA conoce los patrones)
- Puedes verificar el resultado visualmente o con tests

### Señal para cambiar de modo

Si necesitas interrumpir al agente más de 2 veces en la misma implementación, cambia a **Driver/Navigator** o **Ping-pong TDD**.

---

## Modo 4: Investigación Asistida

### Tú preguntas, la IA explora y propone hipótesis

Este modo es ideal para debugging y para entender código que no escribiste.

### Prompts típicos

```text
El endpoint /api/reports tarda 15 segundos en responder cuando hay más 
de 1000 registros. Investiga:
1. ¿Dónde está el cuello de botella? ¿Query, serialización, red?
2. Muéstrame el plan de ejecución de las queries que se disparan
3. Propón 3 hipótesis ordenadas por probabilidad
No implementes nada todavía, solo investiga.
```

```text
Este módulo de autenticación fue escrito hace 2 años por alguien que ya 
no está en el equipo. Necesito entenderlo:
1. ¿Cuál es el flujo completo de login?
2. ¿Dónde se validan los tokens?
3. ¿Hay vulnerabilidades evidentes?
Explícame como si fuera nuevo en el proyecto.
```

### Señal para cambiar de modo

Una vez que tienes la hipótesis correcta y sabes qué cambiar, cambia a **Ping-pong TDD** (si quieres tests) o **Mob programming** (si la solución es clara).

---

## Modo 5: Delegación Completa

### Tú defines, revisas al final

El modo más "manos libres". Solo funciona bien para tareas repetitivas, bien acotadas y de bajo riesgo.

### Cuándo usarlo

- Generar boilerplate (CRUD, migrations, schemas)
- Tareas repetitivas en lote (renombrar, reformatear, migrar)
- Tests para código ya existente y estable
- Documentación técnica (JSDoc, docstrings)

### Prompt típico

```text
Genera endpoints CRUD completos para el recurso "Project":
- Model, controller, routes, validators, tests
- Sigue los patrones exactos de src/resources/tasks/ como referencia
- Incluye tests para cada endpoint
Cuando termines, dime qué archivos creaste para que pueda revisarlos.
```

### Regla de seguridad

Nunca uses delegación completa para:

- Lógica de negocio con reglas no documentadas
- Código que toca seguridad, autenticación o pagos
- Refactors que afectan a muchos módulos
- Cualquier tarea donde no puedas verificar el resultado en menos de 5 minutos

---

## Cuándo Cambiar de Modo: Guía Rápida

| Señal | Acción |
|-------|--------|
| La IA genera 3+ respuestas incorrectas | Cambiar a **Driver/Navigator** (necesitas guiar más) |
| Estás escribiendo código trivial manualmente | Cambiar a **Mob** o **Delegación** (estás perdiendo tiempo) |
| No entiendes el código o el bug | Cambiar a **Investigación** (necesitas explorar primero) |
| Quieres máxima calidad en feature nueva | Cambiar a **Ping-pong TDD** (disciplina de tests) |
| Todo fluye sin interrupciones | Cambiar a **Delegación** (la IA puede sola) |
| Interrumpes al agente 2+ veces | Cambiar a **Driver/Navigator** (la tarea necesita más guía) |

---

## Resumen

- No existe un modo "mejor": cada situación requiere un modo diferente
- La habilidad está en **detectar cuándo cambiar** de modo, no en elegir uno y quedarse
- Los modos conservadores (Driver/Navigator, Ping-pong TDD) producen mejor calidad pero son más lentos
- Los modos delegativos (Mob, Delegación) son más rápidos pero requieren verificación rigurosa
- La investigación asistida es subestimada: úsala antes de implementar cuando no entiendas el problema
