# Módulo A1: Prompting Efectivo para Agentes de Código

## Descripción general

El prompt es tu interfaz principal con un agente de código. No es una pregunta casual ni una búsqueda de Google — es una **especificación de trabajo** para un agente autónomo que va a leer archivos, tomar decisiones y modificar tu código. La calidad de tus resultados depende directamente de la calidad de tus prompts.

En este módulo aprenderás a escribir prompts que producen resultados predecibles y de alta calidad, independientemente de la herramienta que uses (Claude Code, Cursor, Copilot, Windsurf, Cline). Los patrones y técnicas son transferibles.

**Tiempo estimado: 2 horas**

---

## Objetivos de aprendizaje

Al completar este módulo serás capaz de:

1. **Distinguir** entre un prompt para chatbot y un prompt para agente de código, entendiendo por qué requieren enfoques diferentes
2. **Aplicar** la estructura de 5 componentes (contexto, archivos, restricciones, resultado, verificación) a cualquier tarea de desarrollo
3. **Seleccionar** el patrón de prompt adecuado según el tipo de tarea: fix bug, add feature, explore, refactor, code review o migrate
4. **Transformar** prompts vagos o ineficaces en prompts específicos y verificables usando las técnicas del cookbook
5. **Decidir** cuándo dar contexto explícito y cuándo dejar que el agente explore por sí mismo
6. **Iterar** de forma eficiente sobre resultados parciales, usando feedback específico y la regla de las 2 correcciones
7. **Crear** una colección personal de prompts reutilizables para tus tareas frecuentes
8. **Aplicar** las 5 operaciones de context engineering (select, compress, order, isolate, format) para gestionar el contexto a lo largo de sesiones de trabajo complejas

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-prompt-como-interfaz.md](teoria/01-prompt-como-interfaz.md) | El prompt como interfaz de desarrollo: agente vs chatbot, los 3 componentes, la regla de oro | 15 min |
| 2 | [02-anatomia-buen-prompt.md](teoria/02-anatomia-buen-prompt.md) | Anatomía de un buen prompt: estructura de 5 componentes, anti-patrones comunes | 15 min |
| 3 | [03-patrones-por-tarea.md](teoria/03-patrones-por-tarea.md) | Patrones de prompt por tipo de tarea: 6 templates reutilizables con ejemplos completos | 25 min |
| 4 | [04-cookbook-antes-despues.md](teoria/04-cookbook-antes-despues.md) | Prompt cookbook — antes y después: 5 casos de transformación con código real | 15 min |
| 5 | [05-contexto-vs-exploracion.md](teoria/05-contexto-vs-exploracion.md) | Cuándo dar contexto vs. dejar que el agente explore: tabla de decisión y regla 80/20 | 10 min |
| 6 | [06-iteracion-correccion.md](teoria/06-iteracion-correccion.md) | Iteración y corrección de rumbo: corrección progresiva, regla de las 2 correcciones | 10 min |
| 7 | [07-context-engineering.md](teoria/07-context-engineering.md) | Context engineering: las 5 operaciones, patrones de fallo de contexto y KV-cache | 20 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-comparativa-prompts.md](ejercicios/01-comparativa-prompts.md) | Escribir 3 versiones de un prompt (vaga, razonable, óptima) para un bug de autenticación y comparar resultados | 15 min |
| 2 | [02-prompt-por-patron.md](ejercicios/02-prompt-por-patron.md) | Dado un repositorio de ejemplo, escribir un prompt para cada uno de los 6 patrones | 15 min |
| 3 | [03-rescue-prompt-fallido.md](ejercicios/03-rescue-prompt-fallido.md) | Diagnosticar por qué un prompt produce resultados incorrectos y reescribirlo | 15 min |
| 4 | [04-cookbook-personal.md](ejercicios/04-cookbook-personal.md) | Crear una colección personal de 5 prompts reutilizables y guardarlos como skills o en el archivo de instrucciones del proyecto | 15 min |

---

## Prerrequisitos

- Experiencia básica con al menos una herramienta de desarrollo con IA (Claude Code, Cursor, Copilot, etc.)
- Familiaridad con conceptos de desarrollo: tests, control de versiones, estructura de proyectos
- **Recomendado**: haber completado los módulos M01-M06 del [Curso de Claude Code](../../Curso-desarrollo-software-con-Claude-Code/README.md)

---

## Conceptos clave

- **Prompt como especificación**: el prompt no es una pregunta, es un brief de trabajo para un agente autónomo
- **Los 3 componentes**: contexto, intención y verificación — todo prompt efectivo los incluye
- **La regla de oro**: si no le das forma de verificar, no le des forma de implementar
- **Patrones por tarea**: 6 templates reutilizables (fix bug, add feature, explore, refactor, code review, migrate)
- **Anti-patrones**: prompt vago, scope creep, delegación ciega, saturación de contexto
- **Regla 80/20**: el 80% de las tareas solo necesitan 2-3 líneas de contexto
- **Regla de las 2 correcciones**: si corriges el mismo aspecto más de 2 veces, el problema está en el prompt
- **Context engineering**: "the art of curating and maintaining the optimal set of tokens during LLM inference" — gestión dinámica del contexto durante toda la sesión
- **Las 5 operaciones**: select, compress, order, isolate, format — las operaciones fundamentales para gestionar contexto
- **Patrones de fallo de contexto**: context poisoning, context distraction, context confusion, context clash

---

## Flujo de trabajo recomendado

1. **Lee la teoría en orden**: cada archivo construye sobre el anterior, desde los fundamentos hasta las técnicas avanzadas
2. **Toma notas de los patrones**: los 6 templates de la teoría 03 son tu referencia principal para el trabajo diario
3. **Haz los ejercicios con tu herramienta**: no basta con leer — ejecuta los prompts y observa los resultados
4. **Compara**: el ejercicio 01 es especialmente revelador; ver la diferencia entre un prompt vago y uno óptimo cambia tu forma de trabajar
5. **Crea tu cookbook**: el ejercicio 04 te deja con una herramienta práctica que usarás todos los días

---

## Relación con el Curso de Claude Code

Este módulo complementa especialmente:

- [M01 - Introducción y Metodología](../../Curso-desarrollo-software-con-Claude-Code/modulo-01-introduccion/README.md): donde se presenta la metodología de desarrollo asistido y los primeros prompts
- [M03 - Contexto y Tokens](../../Curso-desarrollo-software-con-Claude-Code/modulo-03-contexto-y-tokens/README.md): donde se profundiza en cómo el agente gestiona el contexto
- [M04 - Memoria con CLAUDE.md](../../Curso-desarrollo-software-con-Claude-Code/modulo-04-memoria-claude-md/README.md): donde se enseña a crear archivos de memoria persistente que complementan tus prompts

---

## Navegación

| | |
|---|---|
| **Siguiente** | [Módulo A2: Limitaciones, Fallos y Pensamiento Crítico](../modulo-A2-limitaciones-fallos/README.md) |
