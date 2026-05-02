# Módulo A4: Debugging Sistemático con IA

## Descripción general

La IA no reemplaza al detective, le da superpoderes. Debugging es donde la asistencia de IA aporta más valor: los agentes pueden analizar logs masivos, correlacionar timestamps, explorar hipótesis en paralelo y generar post-mortems completos. Pero el humano sigue siendo quien dirige la investigación.

En este módulo aprenderás un workflow de debugging de 4 pasos optimizado para agentes de código, técnicas de análisis de logs asistido, estrategias de debugging cross-stack con subagentes y la generación de post-mortems profesionales.

**Tiempo estimado: 2 horas**

---

## Objetivos de aprendizaje

Al completar este módulo serás capaz de:

1. **Aplicar** el modelo mental de "IA como asistente de investigación" en sesiones de debugging
2. **Seguir** el workflow de 4 pasos (Reproducir → Aislar → Diagnosticar → Fix + Validar) con prompts efectivos
3. **Usar** agentes de código para analizar logs grandes identificando patrones y anomalías
4. **Coordinar** debugging cross-stack usando subagentes para investigación paralela
5. **Generar** post-mortems completos asistidos por IA con acciones correctivas y preventivas

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-debugging-amplificado.md](teoria/01-debugging-amplificado.md) | Por qué debugging es donde la IA aporta más valor y el modelo mental correcto | 10 min |
| 2 | [02-workflow-debugging.md](teoria/02-workflow-debugging.md) | Los 4 pasos detallados con prompt templates para cada fase | 20 min |
| 3 | [03-analisis-logs.md](teoria/03-analisis-logs.md) | Patrones de análisis, prompt templates y estrategias para logs grandes | 10 min |
| 4 | [04-debugging-cross-stack.md](teoria/04-debugging-cross-stack.md) | Estrategia para bugs que cruzan capas y uso de subagentes | 10 min |
| 5 | [05-post-mortems.md](teoria/05-post-mortems.md) | Generación de post-mortems y tests de regresión con IA | 10 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-bug-con-stack-trace.md](ejercicios/01-bug-con-stack-trace.md) | Diagnosticar y corregir un bug siguiendo el workflow de 4 pasos | 15 min |
| 2 | [02-bug-sin-stack-trace.md](ejercicios/02-bug-sin-stack-trace.md) | Investigar un problema sin error visible — "algo no funciona bien" | 15 min |
| 3 | [03-analisis-logs.md](ejercicios/03-analisis-logs.md) | Encontrar un incidente en 500+ líneas de log de servidor | 10 min |
| 4 | [04-debugging-cross-stack.md](ejercicios/04-debugging-cross-stack.md) | Bug en proyecto fullstack (React + Express + PostgreSQL) | 15 min |
| 5 | [05-post-mortem.md](ejercicios/05-post-mortem.md) | Generar un post-mortem completo del ejercicio anterior | 5 min |

---

## Prerrequisitos

- Haber completado el [Módulo A3: Revisión de Código Generado por IA](../modulo-A3-revision-codigo-ia/README.md)
- Experiencia con debugging (haber depurado bugs en proyectos reales)
- Familiaridad con stack traces, logs de servidor y herramientas de debugging básicas

---

## Conceptos clave

- **IA como asistente de investigación**: la IA explora hipótesis en paralelo, pero el humano dirige la investigación
- **Workflow de 4 pasos**: Reproducir → Aislar → Diagnosticar → Fix + Validar
- **Análisis de logs**: los agentes pueden correlacionar timestamps, filtrar ruido y detectar patrones
- **Debugging cross-stack**: trazar un request de principio a fin para identificar la capa que falla
- **Subagentes**: agentes paralelos que investigan diferentes capas simultáneamente
- **Post-mortem**: documentación estructurada de incidentes con acciones correctivas y preventivas

---

## Flujo de trabajo recomendado

1. **Lee la teoría en orden**: cada archivo construye sobre el anterior, desde los fundamentos hasta las técnicas avanzadas (01 → 05)
2. **Practica el workflow de 4 pasos** con un bug real de tu proyecto
3. **Completa los ejercicios en orden**: los últimos 2 dependen de los anteriores
4. **Guarda los prompt templates** para uso futuro

---

## Relación con el Curso de Claude Code

Este módulo complementa especialmente:

- [Módulo B1 - Estrategias de Desarrollo con IA](../modulo-B1-estrategias-desarrollo-ia/README.md): donde se cubren Gherkin/BDD, TDD y patrones avanzados de workflow como Writer/Reviewer y Fan-out
- [Módulo B2 - SDD Monográfico](../modulo-B2-sdd-monografico/README.md): donde se detalla Spec-Driven Development — las especificaciones generadas en SDD son la referencia para identificar regresiones durante el debugging
- [M09 - Agentes, Skills y Teams](../../Curso-desarrollo-software-con-Claude-Code/modulo-09-agentes-skills-teams/README.md): donde se enseñan las capacidades técnicas de subagentes — este módulo enseña cuándo y cómo aplicarlas en situaciones reales de debugging

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo A3: Revisión de Código Generado por IA](../modulo-A3-revision-codigo-ia/README.md) |
| **Siguiente** | [Módulo B1: Estrategias de Desarrollo con IA Agéntica](../modulo-B1-estrategias-desarrollo-ia/README.md) |
