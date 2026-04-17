# Panorama de Herramientas: Cómo Compararlas Sin Casarte con el Marketing

## Introducción

El mercado de herramientas de desarrollo con IA cambia demasiado rápido como para memorizar rankings, precios o anuncios de producto. Lo útil no es aprender una lista de "ganadores", sino un **marco de evaluación** que te permita comparar cualquier herramienta con criterio.

Este capítulo debe leerse como una **foto orientativa de abril de 2026**. Las características exactas, el pricing, la disponibilidad GA/beta y la política de datos deben verificarse siempre en la documentación oficial.

---

## Primero: Separa Categorías

Muchas comparativas fallan porque mezclan herramientas que hacen cosas distintas. Antes de comparar, separa al menos estas categorías:

| Categoría | Qué optimiza | Ejemplos |
|-----------|--------------|----------|
| **Agente interactivo terminal-first** | Control fino del repositorio, shell, Git, automatización local | Claude Code, OpenAI Codex CLI |
| **Agente integrado en IDE** | Flujo continuo dentro del editor, diffs inline, autocompletado + edición | Cursor, Copilot, Windsurf, Cline |
| **Agente cloud asíncrono** | Delegar tareas completas y recibir resultados más tarde | Devin, coding agents en plataformas cloud, Jules |
| **Adaptador open source / BYOK** | Control del proveedor LLM, transparencia y configurabilidad | Cline y herramientas similares |

La primera pregunta no es "cuál es mejor", sino **qué tipo de interacción necesitas**.

---

## Los 6 Criterios que Sí Importan

### 1. Superficie de trabajo

¿Tu trabajo real ocurre en terminal, en IDE, en CI/CD o en tareas delegadas a la nube?

- Si vives en terminal y Git, un agente terminal-first suele darte más control.
- Si iteras mucho sobre UI, un agente integrado en IDE puede ser más cómodo.
- Si tu equipo quiere delegar tareas enteras y revisar PRs después, mira agentes cloud asíncronos.

### 2. Nivel de autonomía

No confundas autocompletado con autonomía real.

| Necesidad | Qué buscar |
|-----------|------------|
| Sugerencias rápidas mientras escribes | Buen autocompletado |
| Cambios multiarchivo con contexto | Agente interactivo con lectura/escritura y ejecución |
| Delegación de subtareas | Subagentes o handoffs |
| Ejecución no supervisada | Jobs cloud, tareas asíncronas, agentes de fondo |

### 3. Control y gobernanza

La pregunta profesional no es solo "qué puede hacer", sino **qué puedes impedir que haga**.

Evalúa:

- permisos finos
- confirmaciones humanas
- límites de presupuesto o consumo
- trazabilidad de acciones
- facilidad para revisar el diff antes de aceptar

### 4. Portabilidad y lock-in

Si una herramienta te obliga a modelar todo con primitivas propietarias, el coste de salida sube.

- `AGENTS.md` o `CLAUDE.md` son nombres distintos para una misma idea: **instrucciones persistentes del repositorio**
- `Agent Teams`, subagentes u orquestadores internos son nombres distintos para **delegación controlada**
- MCP es un intento de estandarizar la integración con herramientas externas

El principio correcto es: **prefiere conceptos y artefactos reutilizables por encima de features espectaculares pero totalmente propietarias**.

### 5. Privacidad y despliegue

No todas las herramientas encajan en todos los contextos:

| Contexto | Qué priorizar |
|----------|---------------|
| Open source o proyecto personal | UX, velocidad, coste |
| Startup con IP sensible | Retención de datos, controles de acceso, separación de secretos |
| Enterprise regulada | auditoría, residencia de datos, permisos, logging, políticas organizacionales |
| Gobierno / defensa | herramientas aprobadas, entornos aislados, revisión legal y de seguridad |

### 6. Coste total de uso

El coste no es solo la suscripción:

- coste de tokens o inferencia
- tiempo perdido por mala revisión o mala UX
- coste de supervisión humana
- coste de lock-in
- coste de formar al equipo

Una herramienta barata con mala trazabilidad puede salir más cara que una opción aparentemente premium.

---

## Lo Específico de Claude Code o Codex y Cómo Generalizarlo

Si vienes de Claude Code o de Codex, verás términos muy concretos. Conviene traducirlos al patrón general:

| Término específico | Concepto general |
|--------------------|------------------|
| `CLAUDE.md` / `AGENTS.md` | Instrucciones persistentes del repositorio |
| `Plan Mode` | Fase de exploración y planificación antes de editar |
| `/clear` | Reinicio de sesión o contexto |
| `/compact` | Compresión o resumen del contexto acumulado |
| Subagentes / Agent Teams | Delegación a agentes especializados |
| Hooks, headless mode, tareas programadas | Automatización no interactiva y ejecución en CI/CD |

Esta traducción importa porque evita confundir una **implementación concreta** con un **principio del oficio**.

---

## Qué No Debería Decidir una Compra

No tomaría una decisión de adopción basándome solo en:

- rankings de redes sociales
- installs de extensiones
- ARR o adquisiciones
- una feature recién anunciada
- una afirmación de "GA" sin revisar documentación
- benchmarks públicos sin reproducirlos en tu repositorio

Todo eso puede informar, pero no sustituye una evaluación en contexto real.

---

## Evaluación Mínima Antes de Adoptar una Herramienta

Antes de comprometerte con una herramienta para tu equipo, haz esta evaluación:

```markdown
## Evaluación de Herramienta de IA

### Herramienta: [nombre]
### Evaluador: [nombre], Fecha: [fecha]

### Prueba 1: Explorar código (10 min)
- ¿Encuentra los archivos correctos?
- ¿Resume bien la arquitectura?
- ¿Hace suposiciones falsas?

### Prueba 2: Fix bug (15-20 min)
- ¿Llega a la causa raíz?
- ¿Rompe algo fuera del scope?
- ¿Verifica con tests?

### Prueba 3: Add feature (30-45 min)
- ¿Respeta patrones existentes?
- ¿Propone plan antes de tocar demasiados archivos?
- ¿La review del diff es razonable?

### Prueba 4: Seguridad y control
- ¿Permite limitar acciones sensibles?
- ¿Queda trazabilidad?
- ¿Es fácil impedir cambios peligrosos?

### Veredicto
- Productividad: [1-5]
- Calidad de output: [1-5]
- Facilidad de revisión: [1-5]
- Riesgo operativo: [bajo/medio/alto]
- ¿La adoptaría para producción? [sí/no]
```

---

## Veredicto Práctico

La verdad incómoda es esta: **no hay herramienta perfecta**. Muchas veces la combinación correcta es:

- una herramienta para autocompletado y edición rápida
- otra para tareas complejas multiarchivo
- una capa de integración portable (por ejemplo, MCP) para conectar sistemas internos

La decisión madura no es elegir "la mejor herramienta del mercado", sino **la combinación con mejor equilibrio entre autonomía, control, revisión y coste para tu contexto**.

---

## Navegación

| | |
|---|---|
| **Anterior** | [04-responsible-disclosure.md](04-responsible-disclosure.md) |
| **Siguiente** | [06-futuro-desarrollo-ia.md](06-futuro-desarrollo-ia.md) |
| **Módulo** | [README del módulo](../README.md) |
