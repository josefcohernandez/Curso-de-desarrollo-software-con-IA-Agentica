# Patrones de Orquestación Multi-Agente

## Cuándo Usar Múltiples Agentes

No toda tarea requiere múltiples agentes. Usar más agentes de los necesarios introduce overhead de coordinación sin beneficio.

| Situación | Un solo agente | Múltiples agentes |
|-----------|---------------|-------------------|
| Tarea secuencial con contexto compartido | Si | No |
| N tareas independientes entre sí | No | Si (fan-out) |
| Generación + revisión de calidad | No | Si (writer/reviewer) |
| Proyecto complejo con múltiples módulos | Depende | Si (jerárquico) |
| Debugging con múltiples hipótesis | No | Si (investigación paralela) |

**Regla práctica**: si la tarea requiere que el agente mantenga contexto de todo lo anterior para hacer lo siguiente, usa un solo agente. Si las subtareas son independientes, usa múltiples.

---

## Los 5 Patrones de Coordinación

### Patrón 1: Fan-Out

N agentes procesan N tareas independientes en paralelo.

```text
┌─────────────┐
│ Orquestador │
└──────┬──────┘
       │
  ┌────┼────┐
  ▼    ▼    ▼
[A1] [A2] [A3]   ← N agentes, N tareas independientes
  │    │    │
  ▼    ▼    ▼
[R1] [R2] [R3]   ← N resultados
```

**Cuándo usar**: migración de ficheros, documentación masiva, refactoring repetitivo, actualización de dependencias en múltiples paquetes.

**Ejemplo**: migrar 50 ficheros de CommonJS a ESM, cada uno procesado por un agente independiente con límite de presupuesto.

**Clave**: cada tarea debe ser completamente independiente. Si el resultado de A1 afecta a A2, no es fan-out.

### Patrón 2: Pipeline

Agente A genera, Agente B revisa, Agente C integra. Cada etapa transforma o valida el output de la anterior.

```text
[Agente A] ──genera──▶ [Agente B] ──revisa──▶ [Agente C] ──integra──▶ Resultado
  Writer                 Reviewer                Merger
```

**Cuándo usar**: quality gates, procesamiento en fases donde cada fase tiene un rol especializado.

**Ejemplo**: Agente A escribe tests desde una spec, Agente B verifica que los tests cubren todos los requisitos, Agente C ejecuta los tests y reporta resultados.

### Patrón 3: Jerárquico

Un lead coordina y asigna tareas; teammates ejecutan de forma independiente.

```text
       ┌──────┐
       │ Lead │
       └──┬───┘
     ┌────┼────┐
     ▼    ▼    ▼
   [T1] [T2] [T3]   ← Teammates con tareas asignadas
```

**Cuándo usar**: proyectos complejos que requieren coordinación, Agent Teams de Claude Code.

**Ejemplo**: el lead analiza un proyecto y divide el trabajo: T1 implementa el backend, T2 el frontend, T3 los tests de integración.

### Patrón 4: Writer/Reviewer

Un agente escribe, otro revisa de forma independiente. El reviewer no tiene contexto del proceso de escritura, solo ve el resultado.

```text
[Writer] ──código──▶ [Reviewer] ──feedback──▶ [Writer] ──corrección──▶ Final
```

**Cuándo usar**: code review automatizado, revisión de seguridad, verificación de documentación contra implementación.

**Ejemplo**: un agente genera documentación de API desde el código; otro agente verifica que la documentación es precisa comparándola con la implementación real.

**Ventaja clave**: el reviewer detecta errores que el writer no ve porque tiene una perspectiva fresca.

### Patrón 5: Investigación Paralela

N agentes exploran N hipótesis del mismo problema simultáneamente.

```text
       ┌─────────────┐
       │  Problema X  │
       └──────┬───────┘
         ┌────┼────┐
         ▼    ▼    ▼
       [H1] [H2] [H3]   ← 3 hipótesis en paralelo
         │    │    │
         ▼    ▼    ▼
       [?]  [OK]  [?]    ← Una encuentra la solución
```

**Cuándo usar**: debugging complejo donde el problema puede estar en múltiples capas, exploración de soluciones alternativas.

**Ejemplo**: un bug de performance — un agente investiga la capa de base de datos, otro la capa de caché, otro la capa de red. El primero que encuentra evidencia concreta dirige la investigación.

---

## Comunicación Entre Agentes

Los agentes no se comunican directamente entre sí. La comunicación ocurre a través de artefactos compartidos:

| Mecanismo | Cuándo usar | Ejemplo |
|-----------|-------------|---------|
| **Ficheros compartidos** | Specs, planes, código generado | `spec.md` que un agente escribe y otro lee |
| **Tasks y mensajes** | Orquestación con Agent Teams (Claude Code) | `SendMessage`, `TaskCreate`, `TaskUpdate` |
| **Git branches / worktrees** | Aislamiento de cambios entre agentes | Cada agente trabaja en su propia branch |
| **Output estructurado** | Pipeline donde cada etapa produce JSON | Agente A produce `report.json`, Agente B lo consume |

### Ejemplo de comunicación por ficheros

```text
1. Orquestador crea spec.md con los requisitos
2. Writer lee spec.md y genera código en src/feature/
3. Writer crea implementation-notes.md con decisiones tomadas
4. Reviewer lee spec.md + código + implementation-notes.md
5. Reviewer crea review-feedback.md con hallazgos
6. Writer lee review-feedback.md y aplica correcciones
```

---

## Anti-Patrones Multi-Agente

### Demasiados agentes para una tarea simple

Si la tarea la puede hacer un agente en 5 minutos, lanzar 3 agentes tarda más por el overhead de coordinación. La orquestación tiene un coste fijo.

### Agentes sin contexto suficiente

Cada agente empieza desde cero. Si no le das suficiente contexto (spec, archivos relevantes, restricciones), reinventa la rueda o toma decisiones inconsistentes con los demás.

### Sin mecanismo de merge

Si dos agentes modifican el mismo archivo, necesitas una estrategia de merge. Sin ella, uno sobrescribe al otro. Soluciones: asignar archivos exclusivos por agente, usar worktrees separados, o tener un agente "merger" que integra.

### Sin verificación post-orquestación

Después de que N agentes terminen, alguien (humano o agente) debe verificar que el resultado conjunto es coherente. Tests de integración, linting y revisión de conflictos son esenciales.

---

## Resumen de Selección de Patrón

| Necesito... | Patrón |
|-------------|--------|
| Procesar N cosas iguales en paralelo | Fan-out |
| Generar y luego verificar calidad | Pipeline o Writer/Reviewer |
| Coordinar un proyecto complejo | Jerárquico |
| Tener revisión independiente | Writer/Reviewer |
| Explorar múltiples soluciones a un problema | Investigación paralela |
