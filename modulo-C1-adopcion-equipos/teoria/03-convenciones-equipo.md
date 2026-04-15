# Convenciones de Equipo para IA

## Introducción

Cuando cada developer configura su herramienta de IA de forma diferente, los resultados son inconsistentes: estilos de código distintos, permisos arbitrarios, y nadie sabe qué puede hacer el agente y qué no. Las convenciones de equipo eliminan esta variabilidad y hacen que la IA sea **predecible y fiable** para todo el equipo.

---

## Qué Acordar como Equipo

### 1. CLAUDE.md Compartido

El `CLAUDE.md` (o su equivalente en otras herramientas) es el documento más importante para la adopción en equipo. Define las reglas que el agente seguirá para **todos** los miembros.

Debe incluir:
- Comandos de build, test y lint
- Convenciones de estilo y naming
- Arquitectura del proyecto (módulos, capas, dependencias)
- Restricciones explícitas (qué NO modificar)
- Decisiones arquitectónicas vigentes

### 2. Skills del Equipo

Workflows repetibles que cualquier miembro del equipo puede invocar con el mismo resultado:
- Skill de code review con la checklist del equipo
- Skill de deploy con verificaciones previas
- Skill de generación de tests para un módulo nuevo

### 3. Reglas de Permisos

| Acción | Permiso recomendado | Justificación |
|--------|---------------------|---------------|
| Editar archivos del proyecto | Allow | Operación básica del agente |
| Ejecutar tests | Allow | Verificación, no riesgo |
| Ejecutar build/lint | Allow | Verificación, no riesgo |
| Instalar dependencias | Ask | Puede afectar a todo el equipo |
| Crear archivos nuevos | Allow con restricción | Evitar proliferación de utils innecesarios |
| Push a repositorio | Deny o Ask | Requiere revisión humana |
| Deploy | Deny | Siempre requiere aprobación humana |
| Modificar archivos core | Ask | Áreas sensibles del proyecto |

### 4. Política de Code Review

Acuerdo fundamental: **el código generado por IA se revisa igual que el código humano**, con atención extra a los red flags documentados en el [Módulo A3](../../modulo-A3-revision-codigo-ia/README.md):

- Imports inexistentes
- Lógica de negocio incorrecta por falta de contexto
- Over-engineering (abstracciones innecesarias)
- Edge cases no cubiertos
- Código que "parece correcto" pero tiene bugs sutiles

### 5. Política de Commits

Opciones comunes:

| Enfoque | Pros | Contras |
|---------|------|---------|
| Sin marcador especial | Simple, sin fricción | No se puede rastrear código IA |
| Co-authored-by en commit | Trazabilidad sin esfuerzo | Puede generar ruido en el log |
| Tag en PR description | Información en el nivel correcto | Requiere disciplina |

**Recomendación**: incluir una nota en la PR description cuando el código fue generado con asistencia significativa de IA. No es necesario marcar cada commit individual.

### 6. Límites: Qué NO Se Delega a IA

Basándose en el análisis del [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md), el equipo debe acordar qué tareas no se delegan:

- Decisiones de arquitectura críticas (la IA puede proponer, el equipo decide)
- Código safety-critical sin verificación formal
- Manejo de datos sensibles (API keys, PII) sin revisión manual
- Refactors que afectan a módulos mantenidos por otros equipos

---

## Template Completo de CLAUDE.md de Equipo

```markdown
# CLAUDE.md — [Nombre del Proyecto]

## Build y Test

- `npm run build` para compilar el proyecto
- `npm test` para ejecutar toda la test suite
- `npm run test:unit` para tests unitarios únicamente
- `npm run test:e2e` para tests end-to-end
- `npm run lint` para verificar estilo
- `npm run lint:fix` para corregir errores de estilo automáticamente

## Arquitectura

- Monorepo con pnpm workspaces
- Backend: Express + TypeScript en `packages/api/`
- Frontend: React + TypeScript en `packages/web/`
- Shared types: `packages/shared/`
- Base de datos: PostgreSQL con Prisma ORM

## Convenciones de Código

- TypeScript strict mode en todos los packages
- Archivos en kebab-case: `user-service.ts`, `auth-middleware.ts`
- Tests junto al código: `user-service.test.ts`
- Commits en formato conventional commits: `feat:`, `fix:`, `refactor:`, `test:`, `docs:`
- Imports ordenados: externos primero, internos después, types al final

## Restricciones

- NO modificar archivos en `packages/shared/types/` sin aprobación del tech lead
- NO añadir dependencias nuevas sin discutirlo en el canal #tech-decisions
- NO modificar las migraciones de base de datos existentes — crear nuevas migraciones
- Ejecutar SIEMPRE `npm test` antes de crear un PR
- Máximo 400 líneas cambiadas por PR (dividir PRs grandes)

## Decisiones Arquitectónicas Vigentes

- Usamos repository pattern para acceso a datos (no queries directas)
- Autenticación con JWT, refresh tokens en httpOnly cookies
- Logging con pino, structured JSON format
- Error handling centralizado en middleware, no en cada handler

## Permisos del Agente

- Puede editar cualquier archivo excepto los listados en Restricciones
- Puede ejecutar tests, build y lint
- NO puede instalar dependencias sin confirmación
- NO puede hacer push ni deploy
```

---

## Proceso para Crear Convenciones

1. **Reunión inicial** (30-60 min): los early adopters presentan su experiencia y proponen las primeras convenciones
2. **Borrador**: un miembro del equipo escribe el primer CLAUDE.md basándose en la reunión
3. **Review como PR**: el CLAUDE.md se sube como PR y todo el equipo revisa y comenta
4. **Iteración**: se incorpora el feedback y se mergea
5. **Revisión mensual**: cada mes se revisan las convenciones y se ajustan según la experiencia acumulada

---

## Señales de que las Convenciones Funcionan

- El código generado por IA es consistente independientemente de quién lo generó
- Los PRs con código IA pasan code review sin comentarios de estilo
- Los nuevos miembros pueden empezar a usar la IA productivamente en 1-2 días
- El CLAUDE.md se actualiza regularmente (no es un documento olvidado)

---

## Navegación

| | |
|---|---|
| **Anterior** | [02-resistencia-cambio.md](02-resistencia-cambio.md) |
| **Siguiente** | [04-metricas-productividad.md](04-metricas-productividad.md) |
| **Módulo** | [README del módulo](../README.md) |
