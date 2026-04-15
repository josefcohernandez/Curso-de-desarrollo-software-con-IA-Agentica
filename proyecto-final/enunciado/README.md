# Enunciado: Sprint de Desarrollo con IA Agéntica

## Contexto

Vas a simular un sprint de desarrollo completo de 3 "días" (3 sesiones de 1.5-2 horas cada una). El proyecto es una **API REST para gestión de tareas** (todo list avanzada) con estas restricciones:

- Todo el desarrollo se realiza con asistencia de IA agéntica
- Se deben aplicar las técnicas de cada módulo del curso
- Se evalúa tanto el resultado como el **proceso**
- Debes documentar tus prompts, decisiones y correcciones

---

## Stack Sugerido

Puedes elegir el stack con el que te sientas cómodo. Sugerencias:

| Opción | Backend | Base de datos | Tests |
|--------|---------|---------------|-------|
| A (Node.js) | Express + TypeScript | SQLite con Prisma | Jest + Supertest |
| B (Python) | FastAPI | SQLite con SQLAlchemy | pytest + httpx |
| C (Go) | Gin | SQLite con GORM | testing + httptest |

---

## Día 1: Especificación, Diseño y Primera Feature

**Tiempo estimado: 1.5-2 horas**

### Fase 1.1: Entrevista de Requerimientos

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| A1 (Prompting) | Prompt de exploración para definir requerimientos |

Usa el agente de IA como si fuera un product manager al que entrevistas. Pídele que te ayude a definir los requerimientos funcionales y no funcionales de la API.

**Entregable**: `SPEC.md` con:
- Requerimientos funcionales (CRUD de tareas, categorías, prioridades, filtros)
- Requerimientos no funcionales (rendimiento, seguridad, escalabilidad)
- Modelo de datos (entidades y relaciones)
- Endpoints de la API con métodos HTTP, rutas y respuestas esperadas

### Fase 1.2: Diseño de Arquitectura

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| A1 (Prompting) + B1 (Escenarios greenfield) | Prompt de diseño con restricciones |

Usa el agente para diseñar la arquitectura del proyecto.

**Entregable**: `ARCHITECTURE.md` con:
- Estructura de directorios
- Capas de la aplicación (routes, handlers/controllers, services, repositories)
- Dependencias externas con justificación
- Decisiones de diseño documentadas

### Fase 1.3: Scaffolding del Proyecto

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| B2 (Stacks) | Prompt de scaffolding con configuración de CI |

Usa el agente para generar la estructura base del proyecto.

**Entregable**: Proyecto inicializado con:
- Estructura de directorios según la arquitectura
- Configuración de tests
- Configuración de linting
- Script de CI básico
- README del proyecto

### Fase 1.4: Primera Feature — CRUD de Tareas

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| A1 (Patrones: Add Feature) | Prompt de feature con TDD |

Implementa el CRUD completo de tareas con TDD.

**Entregable**:
- Endpoints: `POST /tasks`, `GET /tasks`, `GET /tasks/:id`, `PUT /tasks/:id`, `DELETE /tasks/:id`
- Tests para cada endpoint (happy path + error cases)
- Validación de input
- Respuestas con códigos HTTP correctos

---

## Día 2: Features Adicionales, Debugging y Revisión

**Tiempo estimado: 1.5-2 horas**

### Fase 2.1: Feature — Autenticación JWT

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| A1 (Patrones: Add Feature) + B2 (Backend) | Prompt con restricciones de seguridad |

Añade autenticación JWT a la API.

**Entregable**:
- Endpoints: `POST /register`, `POST /login`
- Middleware de autenticación
- Protección de endpoints de tareas (solo el owner puede ver/editar sus tareas)
- Tests de autenticación (token válido, expirado, missing, malformado)

### Fase 2.2: Inyectar Bug y Debuggear

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| A4 (Debugging) | Debugging sistemático con agente |

Introduce deliberadamente un bug sutil en el código (por ejemplo, un off-by-one en la paginación, o un race condition en una actualización). Luego, usando las técnicas del Módulo A4, pide al agente que lo encuentre y lo corrija.

**Entregable**:
- Descripción del bug inyectado
- Prompt de debugging utilizado
- Proceso de diagnóstico documentado
- Fix implementado con test que verifica la corrección

### Fase 2.3: Code Review del Día 1

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| A3 (Revisión) | Code review con checklist y priorización |

Revisa todo el código del Día 1 usando las técnicas del Módulo A3.

**Entregable**: Documento de review con hallazgos clasificados:
- Críticos (bugs, seguridad)
- Importantes (calidad, mantenibilidad)
- Sugerencias (estilo, mejoras opcionales)

### Fase 2.4: Refactor Basado en Review

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| A2 (Limitaciones) | Refactor acotado, evitando over-engineering |

Aplica las correcciones identificadas en el review, con cuidado de no caer en over-engineering.

**Entregable**:
- Código refactorizado
- Tests pasando (sin regresiones)
- Justificación de qué se cambió y qué se dejó igual

---

## Día 3: Integración, Equipo y Documentación

**Tiempo estimado: 1.5-2 horas**

### Fase 3.1: Feature — Búsqueda y Filtrado

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| B1 (Escenarios end-to-end) | Feature fullstack con tests completos |

Añade búsqueda y filtrado a la lista de tareas.

**Entregable**:
- `GET /tasks?status=pending&priority=high&q=deploy`
- Paginación con `?page=1&limit=20`
- Ordenamiento con `?sort=created_at&order=desc`
- Tests para cada combinación de filtros

### Fase 3.2: Configurar CLAUDE.md de Equipo

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| C1 (Adopción) | CLAUDE.md con convenciones, permisos y restricciones |

Crea un CLAUDE.md para el proyecto como si fuera a ser mantenido por un equipo de 5 developers.

**Entregable**: `CLAUDE.md` con:
- Comandos de build y test
- Convenciones del proyecto
- Restricciones del agente
- Decisiones arquitectónicas vigentes

### Fase 3.3: Auditoría de Seguridad y Privacidad

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| C2 (Ética) | Auditoría con checklist de seguridad |

Revisa el proyecto completo buscando problemas de seguridad y privacidad.

**Entregable**: Documento con:
- Vulnerabilidades encontradas (si las hay)
- Verificación de OWASP top 5
- Verificación de que no hay secrets hardcodeados
- Recomendaciones de mejora

### Fase 3.4: Retrospectiva y Métricas

| Módulo del curso | Técnica a aplicar |
|-----------------|-------------------|
| C1 (Métricas) | Informe con métricas cuantitativas y cualitativas |

Documenta tu experiencia con métricas concretas.

**Entregable**: `RETROSPECTIVE.md` con:
- Tiempo por fase (real vs estimado)
- Número de iteraciones con el agente por tarea
- Prompts que funcionaron bien vs los que necesitaron reescribirse
- Calidad percibida del código generado (1-10 por fase)
- Momentos donde el pensamiento crítico fue decisivo
- Lecciones aprendidas

---

## Resumen de Entregables

| Día | Fase | Entregable |
|-----|------|------------|
| 1 | 1.1 | `SPEC.md` |
| 1 | 1.2 | `ARCHITECTURE.md` |
| 1 | 1.3 | Proyecto scaffolded con CI |
| 1 | 1.4 | CRUD de tareas con tests |
| 2 | 2.1 | Autenticación JWT con tests |
| 2 | 2.2 | Bug debugging documentado |
| 2 | 2.3 | Documento de code review |
| 2 | 2.4 | Código refactorizado |
| 3 | 3.1 | Búsqueda y filtrado con tests |
| 3 | 3.2 | `CLAUDE.md` |
| 3 | 3.3 | Auditoría de seguridad |
| 3 | 3.4 | `RETROSPECTIVE.md` |

---

## Consejos

- **No busques la perfección en el código** — busca la calidad en el proceso
- **Documenta tus prompts** — los mejores y los peores son igual de valiosos para aprender
- **Usa `/clear` entre fases** no relacionadas para evitar contaminación de contexto
- **Si el agente se atasca**, aplica la regla de las 2 correcciones del Módulo A1
- **El Día 3 es tan importante como los otros** — la documentación y retrospectiva son parte del oficio

---

## Navegación

| | |
|---|---|
| **Rúbrica** | [Criterios de evaluación](../criterios-evaluacion/rubrica.md) |
| **Proyecto** | [README del proyecto](../README.md) |
