# Módulo D2: Orquestación Multi-Agente y Automatización a Escala

## Descripción General

Cuando un solo agente no es suficiente, necesitas orquestar múltiples agentes trabajando en paralelo, en pipeline o en jerarquía. Y cuando necesitas que funcionen sin supervisión humana — en CI/CD, batch processing o ejecución nocturna — los prompts deben ser robustos, deterministas y tolerantes a fallos.

Este módulo cubre los patrones de coordinación multi-agente, la creación de prompts para ejecución desatendida, el procesamiento en lote a escala, el testing de prompts como artefactos de producción y la gestión de fallos en automatización.

**Tiempo estimado: 2 horas**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Seleccionar** el patrón de orquestación adecuado (fan-out, pipeline, jerárquico, writer/reviewer, investigación paralela) según la tarea
2. **Diseñar** prompts robustos para CI/CD que funcionen sin intervención humana, con output estructurado y restricciones explícitas
3. **Implementar** procesamiento batch a escala con fan-out, control de concurrencia y gestión de fallos
4. **Crear** test suites para prompts de producción con assertions, golden tests y testing adversarial
5. **Definir** estrategias de recuperación ante fallos en ejecución desatendida: logging, checkpoints y alertas

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
- **Recomendado**: haber completado el [Módulo D1: Arquitectura de Software Orientada a IA](../modulo-D1-arquitectura-para-ia/README.md)

---

## Conceptos Clave

- **Patrones de orquestación**: fan-out, pipeline, jerárquico, writer/reviewer, investigación paralela
- **Prompts para CI/CD**: determinismo, fail-safe, verificación incluida, restricciones explícitas, output estructurado
- **Fan-out escalable**: procesamiento paralelo con control de concurrencia y presupuesto por tarea
- **Testing de prompts**: assertions sobre output, golden tests, adversarial testing, regression testing
- **Gestión de fallos**: logging, checkpoints, alertas, post-processing
- **Comunicación entre agentes**: ficheros compartidos, tasks/mensajes, git branches/worktrees

---

## Flujo de Trabajo Recomendado

1. **Lee la teoría en orden**: empieza por patrones de orquestación (01), luego prompts para CI/CD (02), batch processing (03), testing de prompts (04) y gestión de fallos (05)
2. **Relaciona con tu experiencia**: piensa en tareas de tu trabajo diario donde cada patrón aplicaría
3. **Haz los ejercicios progresivamente**: pipeline simple (01), prompt robusto (02), batch real (03) y testing (04)
4. **Mide resultados**: en cada ejercicio, observa cuántas tareas se completan sin intervención humana

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo D1: Arquitectura de Software Orientada a IA](../modulo-D1-arquitectura-para-ia/README.md) |
| **Siguiente** | [Módulo D3: Testing Avanzado y AI Pair Programming](../modulo-D3-testing-pair-programming/README.md) |
