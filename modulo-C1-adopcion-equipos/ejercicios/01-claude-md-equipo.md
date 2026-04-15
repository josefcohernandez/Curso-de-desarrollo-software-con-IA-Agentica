# Ejercicio: Crear un CLAUDE.md de Equipo

## Objetivo

Crear un CLAUDE.md completo para un proyecto ejemplo que cubra todas las áreas necesarias para que un equipo de desarrollo trabaje de forma consistente con IA agéntica.

**Tiempo estimado: 15 minutos**

---

## Escenario

Eres el tech lead de un equipo de 6 developers que trabaja en **TaskFlow**, una aplicación web de gestión de proyectos. El stack es:

- **Backend**: Node.js + Express + TypeScript
- **Frontend**: React + TypeScript + Tailwind CSS
- **Base de datos**: PostgreSQL con Prisma ORM
- **Tests**: Jest (unit) + Playwright (e2e)
- **CI/CD**: GitHub Actions
- **Estructura**: monorepo con pnpm workspaces (`packages/api`, `packages/web`, `packages/shared`)

El equipo acaba de decidir adoptar IA agéntica y necesita un CLAUDE.md que establezca las convenciones.

---

## Instrucciones

### Parte 1: CLAUDE.md Básico

Crea un archivo `CLAUDE.md` que incluya como mínimo:

1. **Build y Test**: todos los comandos necesarios para compilar, testear y verificar el proyecto
2. **Arquitectura**: descripción de la estructura del monorepo y las dependencias entre packages
3. **Convenciones de código**: naming, imports, estilo de commits

### Parte 2: Restricciones y Permisos

Añade al CLAUDE.md:

4. **Restricciones**: qué archivos o directorios no deben modificarse sin aprobación
5. **Permisos**: qué puede hacer el agente libremente y qué requiere confirmación
6. **Decisiones arquitectónicas**: las reglas de diseño vigentes que el agente debe respetar

### Parte 3: Límites de la IA

Añade una sección que defina:

7. **Tareas que NO se delegan a IA**: basándote en el análisis de riesgos del [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md)
8. **Proceso de revisión**: reglas de code review para código generado por IA

---

## Criterios de Éxito

```markdown
- [ ] El CLAUDE.md tiene todas las secciones (build, arquitectura, convenciones, restricciones, permisos, decisiones, límites)
- [ ] Los comandos de build y test son específicos y ejecutables
- [ ] Las restricciones son claras (qué archivos, quién aprueba)
- [ ] Los permisos usan allow/ask/deny de forma coherente
- [ ] Las decisiones arquitectónicas son concretas, no genéricas
- [ ] Hay una sección explícita de "qué no delegar a IA"
- [ ] Un nuevo miembro del equipo podría usar el CLAUDE.md sin preguntar nada
```

---

## Entregable

Un archivo `CLAUDE.md` listo para subir al repositorio del proyecto. Puedes usar como punto de partida el template de [03-convenciones-equipo.md](../teoria/03-convenciones-equipo.md) y adaptarlo al escenario de TaskFlow.

---

## Reflexión

Tras crear el CLAUDE.md, pregúntate:

- ¿Hay alguna convención implícita en tu equipo real que no está documentada?
- ¿Qué restricciones pondrías si fuera tu proyecto real?
- ¿Cómo actualizarías este documento cuando cambie la arquitectura?

---

## Navegación

| | |
|---|---|
| **Siguiente ejercicio** | [02-plan-adopcion.md](02-plan-adopcion.md) |
| **Módulo** | [README del módulo](../README.md) |
