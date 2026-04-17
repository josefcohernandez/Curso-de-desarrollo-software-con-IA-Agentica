# Módulo E2: Orquestación Multi-Agente y Automatización a Escala

## Descripción General

Cuando un solo agente no es suficiente, necesitas orquestar múltiples agentes trabajando en paralelo, en pipeline o en jerarquía. Y cuando necesitas que funcionen sin supervisión humana — en CI/CD, batch processing o ejecución nocturna — los prompts deben ser robustos, deterministas y tolerantes a fallos.

Este módulo cubre los patrones de coordinación multi-agente, la creación de prompts para ejecución desatendida, el procesamiento en lote a escala, el testing de prompts como artefactos de producción y la gestión de fallos en automatización.

**Tiempo estimado: 2.75 horas**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Seleccionar** el patrón de orquestación adecuado (fan-out, pipeline, jerárquico, writer/reviewer, investigación paralela) según la tarea
2. **Diseñar** prompts robustos para CI/CD que funcionen sin intervención humana, con output estructurado y restricciones explícitas
3. **Implementar** procesamiento batch a escala con fan-out, control de concurrencia y gestión de fallos
4. **Crear** test suites para prompts de producción con assertions, golden tests y testing adversarial
5. **Definir** estrategias de recuperación ante fallos en ejecución desatendida: logging, checkpoints y alertas
6. **Seleccionar** el framework de orquestación adecuado (LangGraph, CrewAI, OpenAI Agents SDK, Google ADK, Semantic Kernel, AutoGen) según el ecosistema y la complejidad del flujo
7. **Implementar** un servidor MCP con FastMCP que exponga tools y resources a cualquier cliente compatible (Claude Code, Cursor, Copilot)

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-patrones-orquestacion.md](teoria/01-patrones-orquestacion.md) | 5 patrones de coordinación multi-agente, comunicación y anti-patrones | 15 min |
| 2 | [02-prompts-para-cicd.md](teoria/02-prompts-para-cicd.md) | Prompts robustos para CI/CD: determinismo, fail-safe, verificación y output estructurado | 15 min |
| 3 | [03-batch-processing.md](teoria/03-batch-processing.md) | Procesamiento en lote a escala: fan-out, control de fallos y monitorización | 10 min |
| 4 | [04-testing-de-prompts.md](teoria/04-testing-de-prompts.md) | Testing de prompts como artefactos de producción: 4 técnicas y framework de evaluación | 10 min |
| 5 | [05-gestion-fallos-desatendidos.md](teoria/05-gestion-fallos-desatendidos.md) | Gestión de fallos en ejecución desatendida: tipos, recuperación y alertas | 10 min |
| 6 | [06-frameworks-orquestacion.md](teoria/06-frameworks-orquestacion.md) | Frameworks de producción: LangGraph, CrewAI, OpenAI Agents SDK, Google ADK, Semantic Kernel, AutoGen | 15 min |
| 7 | [07-mcp-protocolo.md](teoria/07-mcp-protocolo.md) | MCP: arquitectura host/client/server, las 3 primitivas (tools, resources, prompts), FastMCP, seguridad y portabilidad | 20 min |
| 8 | [08-evaluacion-agentes.md](teoria/08-evaluacion-agentes.md) | Evaluación de agentes (evals): métricas, LLM-as-judge, framework de 3 niveles, SWE-bench y diseño de evals propias | 20 min |
| 9 | [09-memoria-agentes.md](teoria/09-memoria-agentes.md) | Memoria de agentes: 4 tipos, mecanismos en coding agents, frameworks (Mem0, Letta, LangGraph Checkpointing), patrones y memoria en orquestación multi-agente | 15 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-pipeline-multi-agente.md](ejercicios/01-pipeline-multi-agente.md) | Diseñar un pipeline Writer/Reviewer para documentación API | 15 min |
| 2 | [02-prompt-para-cicd.md](ejercicios/02-prompt-para-cicd.md) | Crear un prompt robusto para CI/CD con output JSON, testeado con 5 PRs | 15 min |
| 3 | [03-migracion-batch.md](ejercicios/03-migracion-batch.md) | Implementar migración fan-out de 10 ficheros con logging y resumen | 15 min |
| 4 | [04-test-suite-prompts.md](ejercicios/04-test-suite-prompts.md) | Crear test suite para un prompt de producción: assertions, golden tests, adversarial | 15 min |

---

## Prerequisitos

- Experiencia con al menos un agente de código en proyectos reales
- Familiaridad con CI/CD (GitHub Actions, GitLab CI o similar)
- Conocimiento básico de scripting bash
- **Recomendado**: haber completado el [Módulo E1: Arquitectura de Software Orientada a IA](../modulo-E1-arquitectura-para-ia/README.md)

---

## Conceptos Clave

- **Patrones de orquestación**: fan-out, pipeline, jerárquico, writer/reviewer, investigación paralela
- **Prompts para CI/CD**: determinismo, fail-safe, verificación incluida, restricciones explícitas, output estructurado
- **Fan-out escalable**: procesamiento paralelo con control de concurrencia y presupuesto por tarea
- **Testing de prompts**: assertions sobre output, golden tests, adversarial testing, regression testing
- **Gestión de fallos**: logging, checkpoints, alertas, post-processing
- **Comunicación entre agentes**: ficheros compartidos, tasks/mensajes, git branches/worktrees
- **Frameworks de orquestación**: LangGraph (grafos de estado), CrewAI (roles declarativos), OpenAI Agents SDK (handoffs + guardrails), Google ADK (jerárquico nativo), Semantic Kernel (planners enterprise), AutoGen (conversación multi-agente)
- **Criterio de selección**: ecosistema, complejidad del flujo, necesidad de ciclos y estado persistente
- **MCP (Model Context Protocol)**: protocolo abierto para integración de herramientas; primitivas tools/resources/prompts; servidor con FastMCP; portabilidad entre clientes; seguridad (tool poisoning, supply chain, SSRF)
- **Evaluación de agentes (evals)**: task completion rate, tool use accuracy, context retention; LLM-as-judge; framework de 3 niveles (unit, integration, end-to-end); SWE-bench como benchmark estándar
- **Memoria de agentes**: in-context, externa persistente, episódica, semántica; archivo de instrucciones del repositorio (`AGENTS.md`, `CLAUDE.md`, etc.) como memoria explícita; auto-memory como implementación específica de herramienta; frameworks Mem0, Letta, LangGraph Checkpointing; patrones write-through, summarize-and-store, retrieve-and-augment, forget-and-prune; memoria jerárquica en multi-agente

---

## Flujo de Trabajo Recomendado

1. **Lee la teoría en orden**: empieza por patrones de orquestación (01), luego prompts para CI/CD (02), batch processing (03), testing de prompts (04), gestión de fallos (05), frameworks de producción (06), MCP (07), evaluación de agentes (08) y memoria de agentes (09)
2. **Relaciona con tu experiencia**: piensa en tareas de tu trabajo diario donde cada patrón aplicaría
3. **Haz los ejercicios progresivamente**: pipeline simple (01), prompt robusto (02), batch real (03) y testing (04)
4. **Mide resultados**: en cada ejercicio, observa cuántas tareas se completan sin intervención humana

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo E1: Arquitectura de Software Orientada a IA](../modulo-E1-arquitectura-para-ia/README.md) |
| **Siguiente** | [Módulo E3: Testing Avanzado y AI Pair Programming](../modulo-E3-testing-pair-programming/README.md) |
