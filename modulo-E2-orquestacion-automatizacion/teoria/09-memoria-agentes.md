# Memoria de Agentes: Retención de Conocimiento Entre Sesiones

## Por Qué la Memoria es un Problema Central

Un agente de código sin memoria empieza cada sesión desde cero. No sabe que ya migraste la autenticación a JWT la semana pasada. No recuerda que los tests del módulo de pagos son frágiles y hay que ejecutarlos con un flag especial. No sabe que tu equipo prefiere `async/await` sobre callbacks, o que hay una convención de nombramiento en las migraciones de base de datos.

El resultado práctico: el agente repite errores, genera código inconsistente con decisiones previas y consume tiempo tuyo repitiendo contexto que ya le habías dado.

Con memoria bien gestionada, el agente acumula conocimiento del proyecto, aprende tus preferencias y respeta las decisiones de arquitectura tomadas. La diferencia no es de velocidad — es de calidad de colaboración. Es la diferencia entre trabajar con alguien que lleva dos años en el equipo y alguien nuevo cada mañana.

---

## Los 4 Tipos de Memoria de Agentes

Los sistemas de memoria de agentes se clasifican en cuatro tipos según su persistencia y naturaleza. Entender la distinción es clave para elegir el mecanismo correcto.

### Memoria In-Context

La conversación actual. Todo lo que está en la context window activa: el historial del chat, los ficheros que el agente ha leído, las herramientas que ha ejecutado, las instrucciones del sistema.

Es la "memoria de trabajo" del agente. Tiene capacidad limitada (la context window del modelo, típicamente 128K-200K tokens en modelos actuales), y desaparece al cerrar la sesión. No persiste en ningún sitio salvo que alguien la guarde explícitamente.

**Implicación para coding agents**: cuando la sesión se alarga y el contexto se llena, el agente empieza a "olvidar" lo que estaba al principio de la conversación — el fichero que leyó hace 20 intercambios ya no está en su ventana activa. Ver el fichero `05-degradacion-contexto.md` del módulo A2 para estrategias de mitigación.

### Memoria Externa Persistente

Ficheros, bases de datos y configuraciones que el agente lee al inicio de la sesión o cuando los necesita. Persiste entre sesiones porque vive fuera del modelo.

En coding agents, el ejemplo más claro es el archivo de instrucciones persistentes del repositorio: `AGENTS.md` en Codex, `CLAUDE.md` en Claude Code, `.cursorrules` en Cursor o equivalentes. También entran aquí los memory files que algunas herramientas escriben automáticamente.

Esta memoria no es automática — alguien tiene que escribirla y mantenerla. Su ventaja es la predictibilidad: es texto plano que puedes leer, auditar y controlar.

### Memoria Episódica

Recuerdos de interacciones pasadas específicas. "La semana pasada intentamos refactorizar el módulo de notificaciones y encontramos que el servicio externo de email tiene un rate limit no documentado de 10 req/s". O: "La última vez que ejecutamos la migración de base de datos en staging, el timeout era de 30 segundos, no el valor por defecto".

Es la memoria de lo que pasó, con su contexto y consecuencias. Algunos frameworks de agentes (Mem0, Letta) la implementan automáticamente extrayendo y almacenando episodios relevantes de las conversaciones.

En la práctica de coding agents en 2026, la memoria episódica automática es más común en agentes conversacionales de soporte que en coding agents. La alternativa manual es llevar un diario de decisiones en el proyecto (`DECISIONS.md` o similar).

### Memoria Semántica

Conocimiento factual extraído y organizado sobre el dominio. No "lo que pasó" sino "lo que es". "El proyecto usa PostgreSQL 16 con Prisma como ORM". "El servicio de pagos llama a Stripe v3 API". "El módulo de autenticación usa JWT con refresh tokens de 7 días".

Es el mapa del territorio, no el historial de exploraciones. Se construye leyendo el código, la documentación y los commits, y se usa para dar respuestas precisas sobre el estado actual del proyecto.

### Tabla Comparativa

| Tipo | Persistencia | Ejemplo en coding | Implementación típica |
|------|-------------|-------------------|-----------------------|
| **In-context** | Dura lo que dura la sesión | El historial de chat actual | Automática por el modelo |
| **Externa persistente** | Entre sesiones indefinidamente | `AGENTS.md`, `CLAUDE.md`, `.cursorrules`, memory files | Ficheros que el agente lee al inicio |
| **Episódica** | Hasta que se poda explícitamente | "El deploy del viernes falló por X" | Frameworks como Mem0, Letta, o manual |
| **Semántica** | Hasta que el código cambia | "El stack es Next.js + Prisma + AWS" | Archivo de instrucciones, vector stores, RAG |

---

## Mecanismos de Memoria en Coding Agents Actuales

### `AGENTS.md`, `CLAUDE.md` y `.cursorrules`

La forma más robusta y predecible de memoria en coding agents. Es memoria que el desarrollador escribe intencionadamente y el agente lee al inicio de cada sesión.

Un archivo de instrucciones bien mantenido (`AGENTS.md`, `CLAUDE.md` o equivalente) elimina la necesidad de repetir contexto en cada prompt: arquitectura del proyecto, convenciones de código, comandos de build y test, restricciones ("no modificar `legacy/` sin avisar"), decisiones de diseño y sus motivos.

```markdown
# Proyecto: api-pagos

## Stack
- Node.js 20, TypeScript 5.x, Prisma 5 (PostgreSQL 16)
- Tests: Vitest + Supertest para integración
- Deploy: AWS ECS via GitHub Actions

## Convenciones
- Commits en español, formato: `tipo: descripción` (feat, fix, chore, docs)
- Migraciones de Prisma: nombre descriptivo en snake_case, ej: `add_payment_metadata`
- Nunca hardcodear credenciales. Usar variables de entorno con prefijo `APP_`

## Restricciones
- No modificar src/legacy/ sin comentarlo en el PR
- El módulo de webhooks tiene tests frágiles: ejecutar siempre con `--reporter=verbose`

## Decisiones de arquitectura
- JWT sobre sessions: decidido en sprint 3, ver PR #47 para el contexto completo
- Rate limiting en el gateway, no en los servicios individuales
```

Es memoria semántica explícita, controlada por el desarrollador. Su debilidad: requiere disciplina para mantenerla actualizada conforme el proyecto evoluciona.

En Codex el patrón suele resolverse con `AGENTS.md`. En Cursor, con `.cursorrules`. En GitHub Copilot, con ficheros de instrucciones como `.github/copilot-instructions.md`.

### Auto-memory como implementación específica de herramienta

Claude Code escribe automáticamente ficheros de memoria en `~/.claude/projects/<project-id>/memory/`. A diferencia del archivo de instrucciones del repositorio, esta memoria la gestiona el agente, no el desarrollador.

Los tipos de entradas que Claude Code guarda automáticamente:

```text
~/.claude/projects/<id>/memory/
├── user.md          # Preferencias del usuario detectadas en conversaciones
├── feedback.md      # Feedback explícito dado por el usuario ("no hagas X así")
├── project.md       # Conocimiento del proyecto aprendido durante sesiones
└── reference.md     # Referencias a ficheros y decisiones importantes
```

Esta memoria se incluye automáticamente en el contexto de nuevas sesiones del mismo proyecto. No requiere acción del desarrollador, pero es menos predecible: el agente decide qué guardar.

Puedes leer y editar estos ficheros directamente si necesitas corregir o eliminar información que el agente guardó incorrectamente.

### Git como Memoria del Proyecto

El historial de git es una forma de memoria semántica y episódica del proyecto que suele ignorarse.

```bash
# El agente puede usar git para reconstruir contexto
git log --oneline -20           # Qué ha cambiado recientemente
git log --grep="payment" --oneline   # Cambios relacionados con pagos
git blame src/api/webhooks.ts   # Quién cambió cada línea y cuándo
git log -p --follow -- legacy/  # Historial completo de un módulo
```

Un commit message bien escrito es una entrada de memoria episódica. "fix: corregir race condition en procesamiento de webhooks cuando llegan dos eventos en <50ms — ver issue #234 para contexto" es más valiosa que "fix: webhook bug".

Instruir al agente para que consulte el historial de git antes de modificar un módulo es una práctica de bajo coste y alto retorno.

### Tabla de Mecanismos

| Mecanismo | Tipo de memoria | Quién la gestiona | Fiabilidad |
|-----------|----------------|-------------------|------------|
| `AGENTS.md` / `CLAUDE.md` / `.cursorrules` | Semántica + episódica | Desarrollador | Alta — texto explícito, auditable |
| Auto-memory de Claude Code | Semántica + preferencias | Agente | Media — puede incluir información incorrecta |
| Historial git | Episódica + semántica | El equipo (via commits) | Alta si los commits son descriptivos |
| Session history del IDE | In-context | IDE | Baja — suele perderse al cerrar |

---

## Frameworks de Memoria para Agentes Custom

Cuando construyes un agente propio (no usas un coding agent preexistente), necesitas una solución de memoria explícita. Estos son los principales frameworks disponibles en 2026:

### Mem0

"Memory layer for AI agents". Gestiona memoria automáticamente combinando vectores (para búsqueda semántica) con un grafo de conocimiento (para relaciones entre entidades). Extrae hechos relevantes de las conversaciones y los almacena estructurados.

Integración nativa con LangGraph, CrewAI y OpenAI Agents SDK. Disponible como open source o como servicio cloud (`mem0.ai`).

```python
from mem0 import Memory

m = Memory()

# Añadir memoria desde una conversación
m.add("El proyecto usa PostgreSQL 16 con Prisma", user_id="agent-codereview")
m.add("El desarrollador prefiere async/await sobre callbacks", user_id="agent-codereview")

# Recuperar memorias relevantes para una tarea
results = m.search("cómo conectar a la base de datos", user_id="agent-codereview")
for r in results:
    print(r["memory"])
# Output:
# El proyecto usa PostgreSQL 16 con Prisma
```

Mejor caso de uso: agentes conversacionales o asistentes que necesitan personalización por usuario. Para coding agents en producción, la combinación archivo de instrucciones + auto-memory suele ser suficiente.

### Letta (antes MemGPT)

Framework completo de agentes con gestión de memoria inspirada en el modelo de memoria de los sistemas operativos. El agente gestiona activamente su propia memoria: decide qué mover a "core memory" (siempre en contexto), qué guardar en "archival memory" (almacenamiento externo que puede paginar) y qué desechar.

La analogía con SO es precisa: igual que un sistema operativo gestiona RAM y disco, Letta gestiona la context window y el almacenamiento externo.

Mejor caso de uso: agentes que mantienen conversaciones muy largas o que necesitan razonar sobre grandes bases de conocimiento que no caben en la context window.

### LangGraph Checkpointing

No es memoria semántica sino persistencia de estado del grafo. Permite reanudar un flujo de LangGraph exactamente donde se quedó — útil para pipelines largos que pueden interrumpirse.

```python
from langgraph.checkpoint.sqlite import SqliteSaver

# El checkpointer persiste el estado del grafo entre ejecuciones
checkpointer = SqliteSaver.from_conn_string("checkpoints.db")
graph = workflow.compile(checkpointer=checkpointer)

# El thread_id identifica una ejecución concreta
config = {"configurable": {"thread_id": "migration-run-2026-01-15"}}
result = graph.invoke(initial_state, config=config)

# Si la ejecución se interrumpe, se puede reanudar desde el último checkpoint
result = graph.invoke(None, config=config)  # None = reanudar desde checkpoint
```

Mejor caso de uso: pipelines de automatización largos (migraciones, análisis de código, procesamiento batch) que pueden fallar a mitad y necesitan reanudar sin repetir el trabajo ya hecho.

### Zep

Memoria conversacional con búsqueda semántica, optimizado para chatbots y agentes conversacionales. Almacena el historial de conversaciones, lo resume automáticamente y permite búsqueda semántica sobre él.

Mejor caso de uso: agentes de soporte, asistentes de onboarding, cualquier escenario donde el historial conversacional es el activo principal de la memoria.

### Tabla Comparativa de Frameworks

| Framework | Tipo de memoria | Mejor caso de uso | Madurez |
|-----------|----------------|-------------------|---------|
| **Mem0** | Semántica + grafo de conocimiento | Personalización por usuario, agentes conversacionales | Estable, cloud + open source |
| **Letta** | Gestionada por el agente (core + archival) | Conversaciones largas, bases de conocimiento grandes | Activo, proyecto académico consolidado |
| **LangGraph Checkpointing** | Estado de flujo (no semántica) | Pipelines que se reanudan tras interrupción | GA desde LangGraph v1.0 |
| **Zep** | Conversacional con búsqueda semántica | Chatbots, asistentes con historial rico | Estable, producto SaaS maduro |

---

## Patrones de Implementación de Memoria

### Write-Through

El agente escribe en memoria en tiempo real durante la conversación. Cada hecho relevante que detecta lo persiste inmediatamente.

**Ventaja**: la memoria siempre está actualizada.
**Desventaja**: introduce ruido. No todo lo que ocurre en una sesión merece persistirse. Un agente que persiste demasiado contamina la memoria con detalles transitorios.

Cuándo usar: cuando la latencia de la escritura es irrelevante y prefieres no perder ningún dato relevante.

### Summarize-and-Store

Al final de la sesión, el agente o un proceso separado resume lo importante y persiste el resumen.

```python
# Ejemplo: hook post-sesión que resume y guarda
def end_of_session_hook(conversation_history: list[dict]) -> None:
    summary_prompt = (
        "De esta conversación de desarrollo, extrae:\n"
        "1. Decisiones de arquitectura tomadas\n"
        "2. Problemas encontrados y sus soluciones\n"
        "3. Preferencias del desarrollador expresadas\n"
        "4. Convenciones del proyecto descubiertas\n\n"
        f"Conversación:\n{format_history(conversation_history)}"
    )
    summary = model.invoke(summary_prompt)
    append_to_memory_file(summary.content)
```

**Ventaja**: menos ruido, la memoria es más limpia y densa en información.
**Desventaja**: puede perder detalles que parecían insignificantes en el momento pero eran relevantes.

Cuándo usar: cuando la calidad de la memoria importa más que capturarlo todo.

### Retrieve-and-Augment

Al inicio de cada sesión, el agente recupera memorias relevantes para la tarea actual y las añade al contexto del sistema. Es el patrón RAG (Retrieval-Augmented Generation) aplicado a la memoria.

```python
# Al inicio de una sesión, recuperar memorias relevantes
def build_system_prompt(task: str, user_id: str) -> str:
    relevant_memories = memory_store.search(task, user_id=user_id, limit=5)
    
    memory_section = "\n".join([f"- {m['memory']}" for m in relevant_memories])
    
    return f"""Eres un agente de código para el proyecto api-pagos.

Contexto relevante de sesiones anteriores:
{memory_section}

Tarea actual: {task}
"""
```

Es la forma más común de usar memoria en agentes custom. El agente no carga toda la memoria sino solo la relevante para la tarea concreta.

### Forget-and-Prune

Las memorias que se vuelven obsoletas se eliminan activamente. Un hecho sobre la versión 2.x del sistema no tiene valor cuando ya estás en la versión 4.x.

```python
# Ejemplo: política de caducidad por antigüedad o invalidación explícita
def prune_memory(memory_store, max_age_days: int = 90) -> None:
    cutoff = datetime.now() - timedelta(days=max_age_days)
    memory_store.delete_older_than(cutoff)

# O invalidación semántica: cuando detectas que un hecho cambió
def update_memory(memory_store, old_fact: str, new_fact: str, user_id: str) -> None:
    memory_store.delete_similar(old_fact, user_id=user_id)
    memory_store.add(new_fact, user_id=user_id)
```

Sin este patrón, la memoria crece indefinidamente y la recuperación degrada en calidad: memorias antiguas e incorrectas compiten con memorias actuales y correctas.

---

## Memoria en Orquestación Multi-Agente

Cuando tienes múltiples agentes trabajando en paralelo o en pipeline (patrones del fichero `01-patrones-orquestacion.md`), surge la pregunta: ¿comparten memoria o tienen cada uno la suya?

### Memoria Compartida

Todos los agentes leen y escriben el mismo store de memoria.

```text
[Agente Writer] ──escribe──▶ [Memory Store] ◄──lee── [Agente Reviewer]
                                    ▲
                             [Agente Lead] ──lee/escribe──┘
```

**Ventaja**: simple de implementar. El conocimiento descubierto por un agente es inmediatamente disponible para los demás.

**Desventaja**: posibles conflictos de escritura. Si el agente Writer y el Reviewer escriben simultáneamente sobre el mismo hecho, pueden producirse inconsistencias. Requiere control de concurrencia.

### Memoria por Agente con Sincronización

Cada agente mantiene su propia memoria local. Un mecanismo de sincronización periódica o basado en eventos propaga el conocimiento relevante entre agentes.

```text
[Agente A] ──[memoria-A]──┐
[Agente B] ──[memoria-B]──┼──▶ [Sync] ──▶ [Memoria compartida]
[Agente C] ──[memoria-C]──┘
```

**Ventaja**: cada agente trabaja con su memoria sin bloqueos. La sincronización ocurre en momentos controlados.
**Desventaja**: complejidad de implementación. El conocimiento descubierto por un agente no está disponible para los demás hasta el próximo ciclo de sincronización.

### Memoria Jerárquica

El agente lead mantiene la memoria global del proyecto. Los agentes teammates mantienen memoria local de su subtarea. Al completar, reportan al lead lo que aprendieron.

```text
[Lead]  ──[memoria global]──  arquitectura, decisiones, estado del proyecto
   │
   ├── [Teammate 1] ──[memoria local]──  detalles del módulo de pagos
   ├── [Teammate 2] ──[memoria local]──  detalles del módulo de usuarios
   └── [Teammate 3] ──[memoria local]──  detalles de la capa de datos
```

Es el patrón más coherente con la orquestación jerárquica descrita en el fichero `01-patrones-orquestacion.md`. El lead agrega conocimiento global; los teammates se focalizan en su dominio.

---

## Riesgos de la Memoria de Agentes

La memoria mejora la calidad de la colaboración, pero introduce riesgos específicos que hay que gestionar activamente.

**Memory poisoning**: un actor malicioso o un fallo en la validación del input permite que información falsa o instrucciones adversariales se escriban en la memoria del agente. En la siguiente sesión, el agente actúa sobre esa información contaminada. Ver el fichero `../modulo-E4-seguridad-costes/teoria/06-seguridad-agentica.md` para el análisis completo de este vector de ataque.

**Información obsoleta**: el código evoluciona pero la memoria no. El agente tiene memorizado que "el autenticador usa bcrypt con salt de 10 rounds" cuando ya migraste a Argon2 hace dos sprints. Resultado: genera código con una dependencia incorrecta o con parámetros de seguridad desactualizados. La política de prune-and-forget es la mitigación.

**Memorias contradictorias**: dos sesiones diferentes generaron memorias que se contradicen. "El módulo de pagos usa idempotency keys" (correcto hoy) convive con "el módulo de pagos no soporta reintentos" (correcto hace seis meses). El agente no sabe cuál prevalece. Solución: timestamping de memorias y política explícita de "la más reciente gana" para hechos que se contradicen.

**Overfitting a preferencias pasadas**: el agente ha aprendido que prefieres cierto patrón de código y lo aplica incluso cuando no es apropiado. La memoria de preferencias puede volverse una restricción implícita invisible.

---

## Recomendación Práctica para Coding Agents

Si usas un coding agent existente (Claude Code, Cursor, Copilot, Windsurf), no necesitas un framework de memoria. La combinación más efectiva es:

| Componente | Para qué | Cómo mantenerlo |
|------------|----------|-----------------|
| `AGENTS.md` / `CLAUDE.md` / `.cursorrules` | Memoria semántica del proyecto: stack, convenciones, restricciones, decisiones | Actualizar al inicio del proyecto y tras cada decisión arquitectural importante |
| Auto-memory del agente | Preferencias del desarrollador, feedback, patrones observados | Revisar periódicamente los ficheros de memoria de tu herramienta, corregir errores |
| Git history | Memoria episódica del proyecto | Escribir commit messages descriptivos, usar PR descriptions detalladas |

Los frameworks como Mem0 o Letta son relevantes cuando **construyes** un agente custom, no cuando usas uno existente. Si tu caso de uso es "quiero que mi coding agent me conozca mejor", un archivo de instrucciones persistentes y el auto-memory de la herramienta suelen resolverlo sin dependencias adicionales.

Si tu caso de uso es "estoy construyendo un agente que atiende a múltiples usuarios y necesita recordar las preferencias de cada uno", entonces sí necesitas un framework como Mem0.

---

## Resumen

| Tipo de memoria | Recomendación de implementación para coding agents |
|----------------|---------------------------------------------------|
| **In-context** | Gestionar el tamaño de la sesión activamente. Cerrar y abrir sesiones nuevas para tareas independientes. Ver módulo A2 para degradación de contexto |
| **Externa persistente** | Mantener actualizado el archivo de instrucciones persistentes del repositorio. Invertir 5 minutos después de cada decisión arquitectural importante |
| **Episódica** | Usar auto-memory del agente para preferencias y feedback. Complementar con `DECISIONS.md` para decisiones relevantes del proyecto |
| **Semántica** | El archivo de instrucciones del repositorio es suficiente para la mayoría de proyectos. Para proyectos grandes, considerar un RAG sobre la documentación interna |

La memoria de agentes no es un problema tecnológico: es un problema de disciplina de equipo. Las herramientas existen. La pregunta es quién mantiene qué y con qué frecuencia. Un archivo de instrucciones desactualizado es peor que no tener ninguno: el agente trabaja con un mapa incorrecto del territorio.
