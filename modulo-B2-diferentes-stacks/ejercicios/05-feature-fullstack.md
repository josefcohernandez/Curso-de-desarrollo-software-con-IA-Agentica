# Ejercicio 5: Implementar un Feature Fullstack

## Objetivo

Implementar un feature completo que cruza frontend (React), backend (Express API) y base de datos (PostgreSQL), integrando los patrones de todo el módulo.

## Escenario

App de gestión de tareas existente. Tabla `tasks`: id, title, description, status (todo/in_progress/done), timestamps. Frontend React + backend Express + PostgreSQL.

## Feature: "Favoritos"

Marcar tareas como favoritas y filtrar para ver solo favoritas. Diseño: tabla `favorites` separada (no booleano en tasks), endpoints `POST/DELETE /api/tasks/:id/favorite`, icono heart/star toggle en UI.

## Instrucciones

### Paso 1: Migración

```markdown
Create migration for favorites: new table 'favorites' with
task_id (FK to tasks, PK, CASCADE delete) + created_at.
Include UP and DOWN. Test: up, insert, down, up.
```

### Paso 2: Backend API (TDD)

```markdown
Add endpoints: POST /api/tasks/:id/favorite (201, idempotent),
DELETE /api/tasks/:id/favorite (204, idempotent).
GET /api/tasks includes is_favorite boolean.
GET /api/tasks?favorites=true filters.
Write integration tests FIRST:
- Favorite/unfavorite task, idempotent cases, 404 for missing task,
  is_favorite in GET response, favorites filter.
```

### Paso 3: Frontend

```markdown
Add FavoriteButton component (heart icon, filled/empty toggle,
optimistic update with rollback on error).
Hook useFavorite(taskId): { isFavorite, toggle, isLoading }.
TaskList filter toggle "Show favorites only".
Tests: renders correct icon state, toggle works, filter works.
```

### Paso 4: Verificación end-to-end

- [ ] Migración sin errores
- [ ] Tests backend pasan
- [ ] Tests frontend pasan
- [ ] Flujo completo: marcar en UI, API, DB, reflejo en UI
- [ ] Desmarcar funciona correctamente
- [ ] Filtro favoritas funciona

## Criterios de evaluación

| Criterio | Peso | Descripción |
|----------|------|-------------|
| Funcionalidad E2E | 25% | Feature funciona punta a punta |
| Tests | 25% | Integration (backend) + unit (frontend) pasan |
| Consistencia | 20% | Código nuevo sigue patrones existentes |
| Migración | 15% | UP/DOWN funcionan, FK con CASCADE |
| TypeScript | 15% | Types actualizados en todas las capas, sin `any` |

## Entregables

- Migración (up + down)
- `backend/src/routes/favorites.ts` + integration tests
- `frontend/src/components/FavoriteButton.tsx` + tests
- `frontend/src/hooks/useFavorite.ts`
- Types actualizados en ambas capas
