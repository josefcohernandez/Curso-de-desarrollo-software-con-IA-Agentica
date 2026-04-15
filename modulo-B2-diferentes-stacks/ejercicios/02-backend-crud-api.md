# Ejercicio 2: Crear una REST API CRUD con Tests de Integración

## Objetivo

Generar una API REST completa con el patrón "OpenAPI-first", con tests de integración contra una base de datos real.

## Escenario

Backend de una **librería online**. API para gestionar el catálogo de libros. Modelo `Book`: title, author, isbn (13 dígitos), price (>= 0), stock (>= 0), timestamps.

## Instrucciones

### Paso 1: Elige Express.js + TypeScript + SQLite o FastAPI + Python + SQLite

### Paso 2: Proporciona al agente esta especificación y pide implementación

Endpoints: `GET /api/books` (paginación + search), `POST /api/books` (201/400), `GET /api/books/:id` (200/404), `PUT /api/books/:id` (200/400/404), `DELETE /api/books/:id` (204/404).

```markdown
Implement the Bookstore API. Stack: [Express+TS / FastAPI].
Database: SQLite (file for dev, in-memory for tests).
Requirements:
- CRUD endpoints with input validation (isbn: 13 digits,
  price >= 0, stock >= 0, title max 200 chars, author max 100)
- Pagination: page + limit params, response with data/total/page/limit
- Search by title or author
- Proper error responses (400 validation, 404 not found)
- DB migration (up + down) for books table
- Integration tests with real in-memory SQLite
Structure: routes/, models/, validation/, tests/
```

### Paso 3: Verifica los tests

Deben cubrir al mínimo:

- [ ] Crear libro con datos válidos (201)
- [ ] Crear con ISBN inválido (400)
- [ ] Crear con campos faltantes (400)
- [ ] Obtener libro existente (200) e inexistente (404)
- [ ] Listar con paginación y buscar por título
- [ ] Actualizar existente (200) e inexistente (404)
- [ ] Eliminar (204) e inexistente (404)

### Paso 4: Revisa la calidad

- [ ] Validaciones coinciden con las restricciones (isbn 13 dígitos, price >= 0, etc.)
- [ ] Paginación devuelve metadata correcta
- [ ] Tests son independientes entre sí
- [ ] Migración incluye DOWN (rollback)

## Criterios de evaluación

| Criterio | Peso | Descripción |
|----------|------|-------------|
| Validación correcta | 30% | Todas las restricciones implementadas |
| Tests | 25% | Los tests mínimos pasan con DB real |
| Estructura | 20% | Código organizado en capas |
| Paginación/Search | 15% | Funcionan correctamente |
| Errores | 10% | Respuestas consistentes y descriptivas |

## Entregables

- Código fuente de la API completa
- Migración de base de datos (up + down)
- Tests de integración pasando
- `package.json` / `requirements.txt`
