# Ejercicio 3: Crear un CLAUDE.md Completo y Verificar su Impacto

## Objetivo

Crear un CLAUDE.md completo para un proyecto existente que cubra arquitectura, testing, convenciones y restricciones. Luego verificar que el agente produce mejor código con esta documentación.

## Contexto

La documentación como contrato es uno de los mecanismos más efectivos para mejorar los resultados de un agente. Este ejercicio mide el impacto real de un CLAUDE.md bien escrito comparando el comportamiento del agente antes y después.

---

## Instrucciones

### Paso 1: Mide el baseline (antes)

Abre una sesión con tu agente de código en un proyecto que **no tenga** CLAUDE.md. Pide una tarea concreta:

```text
Añade un endpoint GET /api/health que devuelva el estado de la aplicación
incluyendo: versión de la app, estado de la base de datos (connected/disconnected)
y uptime en segundos.
```

Observa y documenta:
- ¿Dónde crea el archivo? ¿Es la ubicación correcta?
- ¿Qué patrón de error handling usa? ¿Es el del proyecto?
- ¿Qué naming convention sigue? ¿Coincide con el proyecto?
- ¿Añade tests? ¿Siguen el patrón existente?
- ¿Cuántas correcciones necesitas hacer?

Anota el resultado en una tabla:

| Aspecto | Resultado sin CLAUDE.md |
|---------|------------------------|
| Ubicación del archivo | ... |
| Error handling | ... |
| Naming | ... |
| Tests | ... |
| Correcciones necesarias | ... |

### Paso 2: Crea el CLAUDE.md

Crea un archivo `CLAUDE.md` en la raíz del proyecto con las 4 secciones esenciales:

**Sección 1 — Arquitectura**: describe la estructura del proyecto, qué hay en cada directorio y cómo se relacionan los módulos.

**Sección 2 — Testing**: documenta los comandos exactos para ejecutar tests, las convenciones de testing y los requisitos (base de datos, variables de entorno).

**Sección 3 — Convenciones**: lista todas las reglas de naming, estructura, error handling, imports, validación y patrones que sigue el equipo.

**Sección 4 — Restricciones**: documenta lo que el agente NO debe hacer (archivos prohibidos, comandos peligrosos, dependencias no permitidas).

### Paso 3: Opcionalmente añade `.claude/rules/`

Si tu proyecto tiene áreas especializadas, crea reglas específicas:

```text
.claude/
└── rules/
    ├── api-endpoints.md     # Cómo crear endpoints: router, controller, service
    ├── database.md          # Cómo crear migraciones, naming de tablas
    └── testing.md           # Patrones de tests, mocks, fixtures
```

### Paso 4: Mide el resultado (después)

Abre una **nueva sesión** con el agente (sin contexto de la sesión anterior). Pide la misma tarea:

```text
Añade un endpoint GET /api/health que devuelva el estado de la aplicación
incluyendo: versión de la app, estado de la base de datos (connected/disconnected)
y uptime en segundos.
```

Observa y documenta los mismos aspectos:

| Aspecto | Sin CLAUDE.md | Con CLAUDE.md |
|---------|---------------|---------------|
| Ubicación del archivo | ... | ... |
| Error handling | ... | ... |
| Naming | ... | ... |
| Tests | ... | ... |
| Correcciones necesarias | ... | ... |

### Paso 5: Itera sobre el CLAUDE.md

Si el agente aún comete errores que podrían evitarse con mejor documentación:
1. Identifica qué regla falta o no es suficientemente clara
2. Añádela al CLAUDE.md
3. Repite el test con una tarea diferente

---

## Criterios de Éxito

- [ ] Has medido el comportamiento del agente antes del CLAUDE.md
- [ ] Tu CLAUDE.md cubre las 4 secciones: arquitectura, testing, convenciones, restricciones
- [ ] Has verificado una mejora medible en el comportamiento del agente
- [ ] Has iterado al menos una vez para mejorar la documentación
- [ ] El CLAUDE.md es específico de tu proyecto, no genérico

## Tiempo Estimado

20 minutos
