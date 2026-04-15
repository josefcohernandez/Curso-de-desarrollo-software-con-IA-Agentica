# Ejercicio 2: Un Prompt para Cada Patrón

## Objetivo

Dado un repositorio de ejemplo, escribir un prompt para cada uno de los 6 patrones aprendidos en la teoría (Fix Bug, Add Feature, Explore, Refactor, Code Review, Migrate). Este ejercicio te obliga a pensar en cómo adaptar la estructura del prompt al tipo de tarea.

**Tiempo estimado: 15 minutos**

---

## Contexto del Proyecto

Trabajas en **TaskFlow**, una aplicación de gestión de tareas con la siguiente estructura:

```text
taskflow/
├── src/
│   ├── api/
│   │   ├── routes/
│   │   │   ├── tasks.ts          # CRUD de tareas
│   │   │   ├── projects.ts       # CRUD de proyectos
│   │   │   └── users.ts          # Gestión de usuarios
│   │   └── middleware/
│   │       ├── auth.ts           # Autenticación JWT
│   │       └── rate-limiter.ts   # Rate limiting
│   ├── services/
│   │   ├── task-service.ts       # Lógica de negocio de tareas
│   │   ├── project-service.ts    # Lógica de negocio de proyectos
│   │   └── notification-service.ts  # Envío de notificaciones (email)
│   ├── repositories/
│   │   ├── task-repository.ts    # Queries a DB para tareas
│   │   └── project-repository.ts # Queries a DB para proyectos
│   └── models/
│       └── index.ts              # Modelos Prisma
├── test/
│   ├── tasks.test.ts             # 34 tests
│   ├── projects.test.ts          # 21 tests
│   └── users.test.ts             # 18 tests
├── package.json                  # Express, Prisma, jsonwebtoken, nodemailer
├── prisma/schema.prisma
└── docker-compose.yml            # PostgreSQL + Redis
```

**Detalles adicionales**:
- Stack: Node.js + Express + TypeScript + Prisma + PostgreSQL
- Autenticación: JWT con roles (admin, member, viewer)
- Las tareas tienen: título, descripción, estado (todo, in_progress, done), prioridad (low, medium, high), asignado a un usuario, pertenecen a un proyecto
- El API tiene 73 tests en total que pasan

---

## Tarea

Escribe un prompt para cada patrón. Usa los escenarios descritos a continuación.

### Patrón 1: Fix Bug

**Escenario**: al marcar una tarea como `done`, el `notification-service.ts` debería enviar un email al creador de la tarea, pero el email se envía al usuario asignado en lugar de al creador.

Escribe el prompt siguiendo el template de Fix Bug de la [teoría 03](../teoria/03-patrones-por-tarea.md).

### Patrón 2: Add Feature

**Escenario**: necesitas añadir un sistema de etiquetas (tags) a las tareas. Los usuarios deben poder crear tags, asignar múltiples tags a una tarea, y filtrar tareas por tag.

Escribe el prompt siguiendo el template de Add Feature.

### Patrón 3: Explore Codebase

**Escenario**: acabas de unirte al equipo y necesitas entender cómo funciona el sistema de notificaciones antes de añadir notificaciones push.

Escribe el prompt siguiendo el template de Explore.

### Patrón 4: Refactor

**Escenario**: `task-service.ts` tiene 600 líneas con lógica mezclada de validación, permisos y negocio. Quieres separar responsabilidades para mejorar testabilidad.

Escribe el prompt siguiendo el template de Refactor.

### Patrón 5: Code Review

**Escenario**: un compañero acaba de subir un PR que añade la funcionalidad de "tareas recurrentes" (tareas que se recrean automáticamente cada X días). Necesitas revisar su código.

Escribe el prompt siguiendo el template de Code Review.

### Patrón 6: Migrate

**Escenario**: necesitas migrar el envío de emails de `nodemailer` (SMTP directo) a `@sendgrid/mail` (API de SendGrid) para mejorar la fiabilidad en producción.

Escribe el prompt siguiendo el template de Migrate.

---

## Rúbrica de Evaluación

Para cada prompt, verifica que cumple estos criterios:

| Criterio | Puntos | Descripción |
|----------|--------|-------------|
| **Usa el patrón correcto** | 2 | La estructura del prompt corresponde al tipo de tarea |
| **Incluye archivos específicos** | 2 | Referencia archivos concretos del proyecto, no menciones vagas |
| **Tiene restricciones claras** | 2 | Dice qué NO hacer o qué respetar |
| **Incluye verificación** | 2 | Especifica cómo comprobar que el resultado es correcto |
| **Scope acotado** | 2 | Una sola tarea, sin scope creep |

**Puntuación**: 10 puntos por prompt, 60 puntos en total.
- 50-60: Excelente dominio de los patrones
- 40-49: Buen nivel, algunos patrones necesitan práctica
- 30-39: Revisar la teoría de los patrones con menor puntuación
- < 30: Releer la [teoría 03](../teoria/03-patrones-por-tarea.md) completa antes de continuar

---

## Reflexión

Después de completar los 6 prompts:

1. Cuál fue el patrón más fácil de escribir? Por qué?
2. Cuál fue el más difícil? Qué información te faltaba?
3. En tu trabajo diario, cuál de los 6 patrones usarías más frecuentemente?

---

## Siguiente

Continúa con [Ejercicio 03: Rescue de Prompt Fallido](03-rescue-prompt-fallido.md).
