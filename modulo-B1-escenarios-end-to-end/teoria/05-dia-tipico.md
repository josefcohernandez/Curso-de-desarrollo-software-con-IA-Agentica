# Escenario: El Día Típico de un Desarrollador con IA

## El problema

Has aprendido los workflows individuales: onboarding, incidentes, greenfield, legacy. Pero una jornada de trabajo no es un solo workflow — es una mezcla de tareas de distinta naturaleza, duración y prioridad. La pregunta no es "¿cómo uso IA para una tarea?" sino "¿cómo organizo mi día para ser productivo con IA sin agotarme?"

---

## Cronograma de una jornada productiva

| Hora | Actividad | Uso de IA | Tipo de sesión |
|------|-----------|-----------|----------------|
| 09:00 | Revisar PRs del equipo | Pedir resumen de diffs grandes; verificar lógica compleja con el agente | Ligera |
| 09:30 | Standup: asignan 2 tickets | Usar el agente para explorar el contexto de cada ticket (archivos relevantes, tests existentes) | Ligera |
| 10:00 | Ticket 1: fix bug en API | Workflow de debugging: reproducir, aislar, fix, test. Sesión enfocada con el agente | **Profunda** |
| 11:00 | `/clear` + Ticket 2: nueva feature | Workflow spec-first: entender requisitos, Plan Mode, diseñar approach, empezar TDD | **Profunda** |
| 12:30 | Almuerzo | Descanso total — cerrar la herramienta de IA | Descanso |
| 13:30 | Code review de PR de compañero | IA como segundo reviewer; verificar edge cases y regresiones | Ligera |
| 14:00 | Continuar ticket 2 | Sesión nueva, reinyectar contexto del spec. Implementar y testear | **Profunda** |
| 16:00 | Tests de integración fallan | Debugging con logs + agente. Investigación rápida, no sesión profunda | Ligera |
| 16:30 | PR + documentación | IA genera descripción de PR, actualiza docs, prepara changelog | Ligera |
| 17:00 | Planificar mañana | Anotar estado de tickets y contexto en CLAUDE.md para mañana | Ligera |

---

## Anatomía de los tipos de sesión

### Sesión profunda (40-90 minutos)

Es cuando resuelves un problema complejo de principio a fin. Requiere máxima atención porque supervisas activamente las decisiones del agente.

**Características**:
- Contexto sostenido durante toda la sesión
- Revisas cada diff antes de aceptar
- Ejecutas tests después de cada cambio
- Tomas decisiones de diseño activamente

**Cuándo usar**: fix bugs complejos, implementar features nuevas, refactoring importante.

**Límite**: máximo 2-3 sesiones profundas al día. Después de la tercera, tu capacidad de supervisión baja significativamente.

### Sesión ligera (10-30 minutos)

Tareas cortas y acotadas donde el riesgo de error es bajo. No requieren supervisión exhaustiva.

**Características**:
- Tarea bien definida y acotada
- Output fácil de verificar (un resumen, un draft de PR, una exploración)
- No modifica código crítico
- Puedes aceptar el resultado tras una revisión rápida

**Cuándo usar**: PR reviews, exploración de código, generación de documentación, planificación.

---

## Los 5 patrones de éxito del día a día

### 1. `/clear` entre tickets — nunca mezclar contextos

```text
-- Terminas ticket 1 (bug fix en el módulo de pagos) --
/clear
-- Empiezas ticket 2 (feature en el módulo de usuarios) --
```

**Por qué es crítico**: el contexto del ticket 1 (archivos de pagos, lógica financiera) contamina las decisiones del ticket 2 (archivos de usuarios, lógica de permisos). El agente puede hacer referencia a archivos irrelevantes o aplicar patrones del módulo equivocado.

### 2. Plan Mode para features de más de 1 hora

Si una tarea va a llevar más de una hora, invierte 10-15 minutos en planificar antes de escribir código:

```text
I need to implement [feature]. Before writing any code:
1. Read the relevant files and understand the current architecture
2. Propose a plan with: files to create/modify, approach, risks
3. Wait for my approval before implementing
```

**Beneficio**: evitas reescrituras completas cuando el agente va por un camino equivocado.

### 3. Tests antes de implementar

Para cualquier feature o fix, siempre en este orden:
1. Escribe (o pide) los tests que definen el comportamiento esperado
2. Verifica que los tests fallan (están testeando algo que aún no existe)
3. Implementa hasta que los tests pasen
4. Refactoriza si es necesario manteniendo los tests verdes

### 4. Revisar diffs antes de commit

Nunca hagas `git add .` y `git commit` sin antes revisar qué cambió:

```bash
git diff --stat    # ¿cuántos archivos y líneas cambiaron?
git diff           # ¿el contenido de los cambios tiene sentido?
```

**Señales de alarma**:
- Más archivos cambiados de los esperados
- Archivos fuera del scope de tu tarea
- Imports nuevos de dependencias que no acordaste
- Eliminación de código que no pediste

### 5. Máximo 2-3 sesiones profundas al día

La **fatiga de supervisión de IA** es real. Después de supervisar activamente código generado durante varias horas, tu capacidad de detectar errores baja notablemente.

**Señales de que estás fatigado**: aceptas diffs sin leerlos, no ejecutas tests, sientes que el agente "sabe lo que hace". Cuando las detectes: para, haz una pausa, cambia a una tarea ligera.

---

## Gestión del contexto a lo largo del día

| Momento | Acción de contexto |
|---------|-------------------|
| Inicio del día | Leer CLAUDE.md y notas del día anterior para recordar estado |
| Antes de cada ticket | `/clear` para empezar con contexto limpio |
| Tras 20+ minutos en una sesión | Evaluar si necesitas `/compact` para reducir contexto |
| Antes del almuerzo | Anotar estado actual del trabajo en un comentario o nota |
| Fin del día | Actualizar CLAUDE.md con decisiones tomadas y estado de tickets |

---

## La jornada perfecta no existe

El cronograma de arriba es un ideal. En la realidad:

- Los incidentes interrumpen tu planificación
- Las reuniones no planificadas rompen sesiones profundas
- Algunos bugs se resisten más de lo esperado
- A veces llegas al final del día sin haber terminado ningún ticket

**Lo que sí puedes controlar**:
- Que cada sesión empiece con contexto limpio
- Que nunca hagas commit sin revisar el diff
- Que los tests existan antes de considerar algo "terminado"
- Que reconozcas cuándo estás fatigado y actúes en consecuencia

---

## Conexión con otros módulos

- Los workflows de cada tipo de tarea provienen de las teorías 01-04 de este mismo módulo
- La gestión de contexto aplica lo aprendido en el [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md) sobre degradación por contexto saturado
- Las técnicas de prompting para sesiones ligeras (PR reviews, exploración) vienen del [Módulo A1](../../modulo-A1-prompting-efectivo/README.md)
- El workflow de debugging para las sesiones profundas viene del [Módulo A4](../../modulo-A4-debugging-sistematico/README.md)
