# Ejercicio 02 — Spec para Feature en Proyecto Existente

**Tiempo estimado**: 25 minutos  
**Dificultad**: Intermedio  
**Prerequisito**: haber completado el Ejercicio 01 y leído el fichero de teoría 06 (SDD en contexto)

---

## Contexto

En el mundo real, rara vez empiezas desde cero. La mayoría de las features se añaden a codebases existentes, y la spec tiene que convivir con el código que ya existe: respetar la arquitectura, documentar los puntos de integración, y definir explícitamente qué código no se va a tocar.

En este ejercicio, partes de una API REST de tareas ya funcional y necesitas añadirle un sistema de etiquetas (tags). El reto está en que la spec tiene que capturar tanto el estado actual del sistema como la nueva funcionalidad, y definir el contrato de integración entre ambos.

---

## Estado actual del sistema

La API de tareas existente tiene estos endpoints funcionando:

```text
POST   /api/v1/tasks           → Crear tarea
GET    /api/v1/tasks           → Listar tareas (filtro por status)
GET    /api/v1/tasks/:id       → Obtener tarea por ID
PATCH  /api/v1/tasks/:id       → Actualizar tarea
DELETE /api/v1/tasks/:id       → Borrar tarea

Autenticación: JWT en header Authorization: Bearer <token>
Base de datos: PostgreSQL con tabla tasks (id, user_id, title, description, status, created_at)
```

Los tests de integración existentes cubren: CRUD completo, autenticación, autorización (un usuario no puede ver tareas de otro).

---

## La nueva feature: sistema de tags

El producto quiere que los usuarios puedan etiquetar sus tareas con tags de texto libre. Una tarea puede tener múltiples tags. Los usuarios pueden filtrar tareas por tag. Los tags son globales (no por usuario — "trabajo" es el mismo tag para todos).

Esta descripción tiene varias decisiones de diseño sin tomar. Tu trabajo en este ejercicio es descubrirlas y documentarlas.

---

## Instrucciones paso a paso

### Paso 1: Análisis del estado actual (5 minutos)

Antes de hacer la entrevista, pide al agente que documente el comportamiento actual del sistema para la sección de la spec:

```text
Tengo una API REST de tareas con los siguientes endpoints:
[lista de endpoints de arriba]

Tabla de base de datos: tasks (id, user_id, title, description, status, created_at)
Autenticación: JWT

Sin hacer cambios, documenta en formato spec:
1. Los endpoints actuales con sus inputs y outputs esperados
2. Las reglas de negocio actuales (autorización, validación)
3. Los archivos que NO deben modificarse al añadir la nueva feature
```

Este análisis es el "estado actual" que servirá de base para la nueva spec.

### Paso 2: Entrevista para la nueva feature (10 minutos)

Usa este prompt para la entrevista de la nueva feature:

```text
Voy a añadir un sistema de tags a la API de tareas documentada.
Descripción básica: los usuarios pueden añadir tags de texto libre a sus tareas,
y filtrar tareas por tag.

Entrevístame sobre los requisitos de la nueva feature.
Presta especial atención a:
- Cómo se integra con el sistema de tareas existente
- Qué comportamientos en edge cases (tag que no existe, borrar tag de una tarea, etc.)
- Impacto en los endpoints existentes (¿cambia la respuesta de GET /tasks?)
- Restricciones para no romper la funcionalidad existente
Una pregunta a la vez.
```

**Orientación para responder las preguntas previsibles**:

- *¿Los tags son globales o por usuario?* → Globales. "trabajo" es el mismo tag para Alice y para Bob.
- *¿Hay límite de tags por tarea?* → 10 tags máximo por tarea.
- *¿Los tags tienen un ID propio o solo son strings?* → Piénsalo. Esta es una decisión de diseño con implicaciones en la base de datos.
- *¿Qué pasa si se borra un tag de una tarea: se elimina el tag globalmente?* → No, solo se desasocia de esa tarea.
- *¿Los tags aparecen en la respuesta de GET /tasks?* → Sí, siempre. Esta es una decisión de impacto en los tests existentes.
- *¿Los tags tienen un endpoint propio (CRUD de tags)?* → Solo GET /tags (listar los tags existentes). No se crean explícitamente — se crean al asociarse a una tarea por primera vez.

### Paso 3: Generar la spec de integración (10 minutos)

Pide al agente que genere la spec completa:

```text
Con el análisis del estado actual y las decisiones de la entrevista,
genera SPEC-TAGS.md para la feature de tags.

La spec debe incluir:
1. Contexto: qué existe, qué se añade
2. Requisitos Funcionales de la nueva feature (RF-01, RF-02, ...)
3. Impacto en endpoints existentes (qué cambia, qué no cambia)
4. Modelo de datos: la nueva tabla y sus relaciones con la tabla tasks existente
5. Nuevos endpoints
6. Edge cases y manejo de errores
7. Plan de testing: incluir que los tests existentes deben seguir pasando
8. Fases de implementación
9. Exclusiones y restricciones: archivos que NO se modifican

Presta especial atención a la sección de "impacto en endpoints existentes":
documenta explícitamente qué cambia en las respuestas actuales.
```

---

## Entregables

1. **SPEC-TAGS.md** generado en el Paso 3
2. **Respuesta** a las preguntas de reflexión

---

## Reflexión

1. ¿La spec documenta claramente qué código del sistema existente NO se puede tocar? ¿Dónde lo hace?
2. ¿La spec especifica cómo cambia la respuesta de `GET /api/v1/tasks` al añadir tags? ¿Los tests existentes necesitan actualizarse?
3. ¿Elegiste tags como strings directos o como entidades con ID propio? ¿Por qué? ¿Qué implicaciones tiene?
4. Si un desarrollador del equipo que no participó en la entrevista tiene que implementar esta feature, ¿puede hacerlo solo con SPEC-TAGS.md? ¿Qué le faltaría?

---

## Criterios de Evaluación

| Criterio | Excelente | Aceptable | Necesita mejora |
|----------|-----------|-----------|-----------------|
| Documentación del estado actual | Estado actual documentado claramente como base de la spec | Estado actual mencionado pero incompleto | No hay documentación del estado actual |
| Contrato de no agresión | Lista explícita de archivos/endpoints que no se modifican | Mención general de no romper tests | No hay restricciones de integración |
| Impacto en endpoints existentes | Documentado exactamente qué cambia en cada respuesta | Mencionado pero impreciso | No documentado |
| Modelo de datos de integración | Tabla de tags + tabla de relación task_tags con FK correctas | Modelo de datos básico | Sin modelo de datos |
| Plan de testing | Tests existentes + tests de regresión + tests de nueva feature | Solo tests de nueva feature | Sin plan de testing |
