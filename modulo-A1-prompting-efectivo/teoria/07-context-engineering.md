# Context Engineering: La Evolución del Prompting

## Introducción

El prompt engineering te enseña a formular una buena instrucción puntual. El context engineering va más allá: es **"the art of curating and maintaining the optimal set of tokens during LLM inference"** (Anthropic Engineering Blog, 2025).

La distinción es importante. Un chatbot recibe un prompt, procesa, responde, y olvida. Un agente de código trabaja en sesiones largas, con contexto que crece y cambia: lee archivos, ejecuta comandos, recibe resultados, toma decisiones. En ese entorno, la pregunta no es solo "cómo escribo una buena instrucción" sino "qué información está activa en el contexto en cada momento, y cómo la gestiono durante toda la sesión".

| Dimensión | Prompt Engineering | Context Engineering |
|-----------|-------------------|---------------------|
| Alcance | La instrucción inicial | Todo el contexto durante la sesión |
| Momento | Antes de empezar | Continuo, mientras el agente trabaja |
| Pregunta central | ¿Cómo formulo la tarea? | ¿Qué tokens deben estar activos ahora? |
| Relevancia | Tareas puntuales | Sistemas en producción, sesiones largas |
| Relación | Subconjunto | Disciplina que subsume al prompt engineering |

Context engineering no reemplaza lo aprendido en los ficheros anteriores de este módulo — lo expande. Los 5 componentes del prompt (fichero 02) siguen siendo válidos; context engineering los enmarca en una gestión dinámica.

---

## Las 5 Operaciones de Context Engineering

Anthropic identifica cinco operaciones para gestionar el contexto de forma óptima. Dominarlas es la diferencia entre un usuario que "habla con la IA" y un desarrollador que orquesta sesiones de trabajo eficientes.

### 1. Select — Elegir qué incluir

No toda la información disponible debe estar en el contexto. Incluir información irrelevante no es neutral: consume tokens y puede diluir la atención del modelo.

```text
# Sin seleccion: pegar todo el directorio
"Revisa todos los archivos en src/ y corrige el bug"

# Con seleccion: identificar los relevantes
"El bug está en la capa de autenticación.
Archivos relevantes:
- src/auth/jwt.ts (generación del token)
- src/middleware/auth.ts (verificación)
- test/auth.test.ts (el test que falla)
Ignora el resto del directorio por ahora."
```

La selección es tu responsabilidad inicial. Con práctica, aprendes qué información tiene peso real para cada tipo de tarea.

### 2. Compress — Reducir sin perder lo esencial

La compresión reduce el volumen de información manteniendo el valor informativo. No es simplificar — es **destilar**.

```text
# Sin comprimir: pegar 500 lineas de logs
[pegar el log completo de la CI]

# Comprimido: las lineas relevantes
El error ocurre en el step "deploy-staging":
  Error: ECONNREFUSED 10.0.0.45:5432
  Context: pg client connecting to DATABASE_URL
Las lineas anteriores del build pasan sin errores.
```

En Claude Code, `/compact` aplica compresión automática al historial de la conversación cuando se acerca al límite de contexto.

### 3. Order — El orden importa

Los modelos de lenguaje muestran **efecto de recencia**: la información al final del contexto tiene más influencia que la del principio. Esto tiene implicaciones prácticas directas.

```text
# Estructura que aprovecha el efecto de recencia:

[Contexto general del proyecto — al principio, menos peso]
Usamos TypeScript estricto, Prisma, y tests con Vitest.

[Información específica de la tarea — en el medio]
Necesito un endpoint POST /api/invitations.

[Restricciones y criterios de verificacion — al final, mas peso]
IMPORTANTE: usa el mismo patron de validacion que src/api/users.ts.
Ejecuta `npx vitest run test/invitations.test.ts` antes de cerrar.
```

La regla práctica: pon las restricciones y el criterio de verificación al final del prompt, no al principio.

### 4. Isolate — Separar contextos que interfieren

Cuando mezclas contextos de tareas diferentes en la misma conversación, el agente tiene que reconciliar información que no tiene relación. El resultado es degradación de calidad.

```text
# Problema: contextos mezclados
[30 mensajes sobre un bug de autenticación]
"Ahora necesito implementar el flujo de pagos con Stripe"
# El agente arrastra el contexto del bug de auth, que no tiene nada que ver

# Solucion: aislar contextos
/clear
"Contexto fresco. Necesito implementar el flujo de pagos con Stripe..."
```

El aislamiento también aplica a nivel de arquitectura: los subagentes ejecutan tareas independientes en contextos propios, sin contaminar la sesión principal.

### 5. Format — La estructura del contexto afecta la calidad

La misma información presentada en formatos diferentes produce respuestas de diferente calidad. El markdown con headers, listas y tablas es significativamente más efectivo que texto plano corrido.

```text
# Sin estructura: texto plano
"Necesito que el endpoint valide que el usuario este autenticado
y que sea admin y que el body tenga name y email y que el email
sea valido y que name tenga entre 2 y 100 caracteres"

# Con estructura: markdown
## Endpoint POST /api/admin/users

### Autenticacion
- Requerir token JWT válido
- El usuario debe tener rol `admin`

### Validacion del body (zod)
- `name`: string, 2-100 caracteres
- `email`: string, formato email válido

### En caso de error
- 401 si no autenticado
- 403 si no es admin
- 400 con errores de validación detallados
```

El frontmatter YAML también es efectivo para metadata de contexto en archivos `CLAUDE.md`.

---

## Relación con los 5 Componentes del Prompt

Las operaciones de context engineering no son conceptos nuevos — son una capa de abstracción sobre las prácticas de prompting ya vistas en el [fichero 02](02-anatomia-buen-prompt.md). Esta tabla muestra la correspondencia directa:

| Componente del prompt | Operación de context engineering | Por qué se relacionan |
|-----------------------|----------------------------------|----------------------|
| **Archivos relevantes** | Select | Elegir qué leer es exactamente la operación de selección |
| **Contexto del cambio** | Compress + Format | El contexto debe ser denso en información y bien estructurado |
| **Restricciones** | Order + Format | Deben ir al final (recencia) y en formato de lista |
| **Resultado esperado** | Format | Definirlo con estructura markdown aumenta la precisión |
| **Forma de verificar** | Order | Al final del prompt, donde tiene más peso |

La diferencia es que el prompt engineering aplica estas ideas en el momento de escribir el prompt inicial. Context engineering las aplica de forma continua, a lo largo de toda la sesión.

---

## Los 4 Patrones de Fallo de Contexto

En sesiones de trabajo reales, el contexto puede degradarse de cuatro formas distintas. Reconocerlos es el primer paso para prevenirlos.

### Context Poisoning (Envenenamiento)

Información incorrecta o desactualizada en el contexto que contamina todas las respuestas posteriores.

```text
# Escenario real:
El README dice "usamos Drizzle como ORM" (actualizado hace 6 meses)
pero el proyecto migró a Prisma hace 3 meses y nadie actualizó el README.

# Consecuencia:
El agente genera migraciones con la sintaxis de Drizzle
sobre una base de código que usa Prisma.
```

**Prevención**: mantener `CLAUDE.md` como fuente de verdad actualizada, no confiar en documentación desactualizada.

### Context Distraction (Distracción)

Información irrelevante que consume tokens y diluye la atención del modelo.

```text
# Escenario real:
Pegas 800 líneas de logs de la CI para preguntar por un error
que aparece en la línea 743.

# Consecuencia:
El agente analiza los 800 líneas en lugar de ir directo al error.
La respuesta es genérica y tarda más.

# Alternativa:
Pega solo las 20 líneas del error y las 10 líneas de contexto anterior.
```

**Prevención**: aplicar Compress activamente. Resumir, filtrar, destilar antes de incluir en el contexto.

### Context Confusion (Confusión)

Información contradictoria dentro del mismo contexto.

```text
# Escenario real:
CLAUDE.md dice:        "Stack: Node.js + Express + PostgreSQL"
Un comentario en código: "TODO: migrar a Fastify"
Un fichero de config:    version: fastify@4.28.0

# Consecuencia:
El agente no sabe qué framework usar.
Las respuestas mezclan patrones de Express y Fastify.
```

**Prevención**: auditar el contexto permanente (`CLAUDE.md`, documentación interna) para eliminar contradicciones. Cuando hay ambigüedad, explicitarla en el prompt: "Usamos Express, ignora referencias a Fastify — es una migración pendiente".

### Context Clash (Colisión)

Contextos de tareas diferentes mezclados en la misma sesión.

```text
# Escenario real:
[Sesión de 45 min depurando un bug de autenticación]
"Oye, aprovechando, crea también el módulo de facturación"

# Consecuencia:
El módulo de facturación sale con referencias al bug de auth,
manejo de errores mezclado con los patrones del debug,
y coherencia interna degradada.
```

**Prevención**: usar `/clear` al cambiar de tarea. Es el equivalente de abrir una ventana de terminal nueva para un trabajo diferente.

### Relación con la taxonomía del módulo A2

Estos 4 patrones son la causa directa de dos de los 8 fallos descritos en [A2 — Taxonomía de Fallos](../modulo-A2-limitaciones-fallos/teoria/01-taxonomia-fallos.md):

- **Context Poisoning y Context Confusion** causan **Alucinación de Lógica** (el agente tiene una premisa falsa y trabaja desde ella)
- **Context Distraction y Context Clash** causan **Degradación por Contexto** (la sesión pierde coherencia, como se describe en [A2 — Degradación por Contexto](../modulo-A2-limitaciones-fallos/teoria/05-degradacion-contexto.md))

---

## KV-Cache Hit Rate: La Métrica de Salud del Contexto

### Qué es el KV-cache

Cuando el modelo procesa tokens, almacena los resultados intermedios de cada token en un cache de clave-valor (KV-cache). Si la siguiente petición empieza con los mismos tokens que la anterior, el modelo puede reutilizar esos cálculos en lugar de recomputarlos.

El **KV-cache hit rate** es el porcentaje de tokens de entrada que el modelo puede servir desde cache en lugar de procesar de nuevo.

### Por qué importa

Una tasa alta de cache hits indica tres cosas positivas simultáneamente:

1. **El contexto es estable**: el prefijo de la conversación no cambia entre peticiones
2. **El coste baja**: los tokens cacheados cuestan menos que los procesados (hasta 90% menos en Claude)
3. **La latencia baja**: menos cómputo significa respuestas más rápidas

Una tasa baja de cache hits en producción es una señal de que el contexto se está reconstruyendo innecesariamente en cada petición.

### Cómo maximizar el KV-cache hit rate

```text
# Patron que maximiza cache hits:
[Contenido estatico — nunca cambia entre peticiones]
Eres un agente de desarrollo que trabaja en el proyecto X.
Stack: TypeScript, Fastify, Prisma, Vitest.
Convenciones: early returns, sin comentarios obvios, tests en test/.

[Contenido semi-estatico — cambia poco]
Archivos del contexto actual: src/payments/ y test/payments/

[Contenido dinamico — la peticion especifica, al final]
Implementa el endpoint POST /api/payments/refund
```

El contenido estático al principio (el prefijo) se cachea entre peticiones. El contenido dinámico al final no afecta el cache del prefijo.

Para una visión detallada de cómo los costes de tokens impactan en proyectos reales, ver [E4 — Token Budgeting](../modulo-E4-seguridad-costes/teoria/03-token-budgeting.md).

> **Referencia**: para profundizar en la gestión de la ventana de contexto desde la perspectiva de la herramienta, ver [M03 - Contexto y Tokens](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-03-contexto-y-tokens/README.md) del Curso de Claude Code.

---

## Context Engineering en la Práctica Diaria

Esta tabla cubre las 5 situaciones más frecuentes en un día de trabajo normal y qué operación aplicar:

| Situación | Operación | Acción concreta |
|-----------|-----------|-----------------|
| Empiezas a trabajar en un bug | Select | Identifica los 2-3 archivos relevantes antes de abrir la sesión. Menciónalos explícitamente |
| La sesión lleva más de 30 intercambios | Compress + Isolate | Ejecuta `/compact` si está disponible, o empieza una sesión nueva con `/clear` |
| El agente genera código inconsistente con el proyecto | Format + Select | Revisa tu `CLAUDE.md`. Si no existe o está desactualizado, créalo o actualízalo antes de continuar |
| Cambias de tarea a mitad de sesión | Isolate | `/clear` antes de la nueva tarea. No arrastres el contexto anterior |
| El agente ignora una restricción que pusiste al principio | Order | Mueve esa restricción al final del prompt en la siguiente iteración |

---

## Herramientas de Context Engineering en Coding Agents

Las operaciones abstractas se implementan con herramientas concretas. Esta tabla mapea cada operación a las herramientas disponibles:

| Operación | Herramienta | Cómo usarla |
|-----------|-------------|-------------|
| **Select** | `@file` mentions | Citar archivos específicos en lugar de "mira en src/" |
| **Select** | Plan Mode | El agente selecciona qué leer antes de actuar — más preciso que tu selección manual en tareas complejas |
| **Compress** | `/compact` | Comprime el historial de la conversación manteniendo el resumen de decisiones tomadas |
| **Order** | Estructura del prompt | Restricciones y verificación al final. Contexto general al principio |
| **Isolate** | `/clear` | Nueva conversación para nueva tarea. No arrastres contexto entre tareas no relacionadas |
| **Isolate** | Subagentes | Cada subagente tiene su propio contexto; las tareas paralelas no se contaminan entre sí |
| **Isolate** | Worktrees | Cada worktree mantiene su estado de código aislado — útil para features en paralelo |
| **Format** | `CLAUDE.md` | El contexto permanente del proyecto; estructura con headers YAML y listas |
| **Format** | Markdown en prompts | Headers, listas, bloques de código en lugar de prosa corrida |

> En **Cursor**, el equivalente de Select es usar `@codebase` con preguntas específicas en lugar de abrir el chat sin contexto. El equivalente de Isolate es abrir una nueva ventana de chat.
>
> En **Copilot**, Select se implementa seleccionando el rango de código antes de invocar el chat. Compress ocurre cuando cierras y reabres el panel de chat.

---

## Resumen

| Operación | Cuándo aplicarla | Herramienta en Claude Code |
|-----------|-----------------|---------------------------|
| **Select** | Al iniciar cualquier tarea: identifica qué archivos son relevantes | `@file`, Plan Mode |
| **Compress** | Cuando la sesión es larga o los inputs son voluminosos | `/compact`, resumir manualmente antes de pegar |
| **Order** | Al escribir el prompt: restricciones y verificación van al final | Estructura consciente del prompt |
| **Isolate** | Al cambiar de tarea, al detectar context clash o conversación larga | `/clear`, subagentes, worktrees |
| **Format** | Siempre: usar markdown en prompts y `CLAUDE.md` con estructura clara | `CLAUDE.md`, markdown en prompts |

---

## Siguiente

El context engineering te da las herramientas para gestionar el contexto de forma proactiva. El módulo A2 aborda el lado defensivo: cómo detectar y responder cuando el contexto ya ha fallado y el agente produce resultados incorrectos. Continúa con [Módulo A2 — Limitaciones, Fallos y Pensamiento Crítico](../../modulo-A2-limitaciones-fallos/README.md).
