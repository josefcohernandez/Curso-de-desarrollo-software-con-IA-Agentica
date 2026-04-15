# Módulo D1: Arquitectura de Software Orientada a IA

## Descripción General

La arquitectura de tu software determina directamente cuánto valor puedes extraer de un agente de código. Un proyecto con buena "AI-readability" permite que el agente entienda, navegue y modifique el código con precisión. Un proyecto con mala AI-readability produce alucinaciones, cambios incorrectos y frustración.

Este módulo te enseña a diseñar y evaluar la arquitectura de tus proyectos para maximizar la efectividad de cualquier agente de código (Claude Code, Cursor, Copilot, Windsurf, Cline). Aprenderás los patrones que facilitan la colaboración con IA y los anti-patrones que la sabotean.

**Tiempo estimado: 2 horas**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Evaluar** la AI-readability de un proyecto existente usando los 5 pilares y asignar una puntuación objetiva
2. **Aplicar** los 5 patrones de arquitectura que facilitan la asistencia de agentes de código
3. **Identificar** los 4 anti-patrones arquitectónicos que confunden a los agentes y proponer soluciones concretas
4. **Diseñar** documentación (CLAUDE.md, ADRs) que funcione como contrato bidireccional con el agente
5. **Refactorizar** módulos existentes para mejorar su AI-readability sin cambiar funcionalidad

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-ai-readability.md](teoria/01-ai-readability.md) | AI-readability como atributo de calidad: los 5 pilares y métricas proxy | 20 min |
| 2 | [02-patrones-modulares.md](teoria/02-patrones-modulares.md) | 5 patrones de arquitectura que facilitan la asistencia de IA | 20 min |
| 3 | [03-anti-patrones-arquitectonicos.md](teoria/03-anti-patrones-arquitectonicos.md) | 4 anti-patrones que confunden a los agentes y cómo resolverlos | 15 min |
| 4 | [04-documentacion-como-contrato.md](teoria/04-documentacion-como-contrato.md) | CLAUDE.md como contrato bidireccional y ADRs asistidos por IA | 15 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-evaluar-ai-readability.md](ejercicios/01-evaluar-ai-readability.md) | Evaluar la AI-readability de un proyecto usando los 5 pilares con puntuación 1-5 | 20 min |
| 2 | [02-refactorizar-para-ia.md](ejercicios/02-refactorizar-para-ia.md) | Refactorizar un módulo monolítico: añadir README, types y tests como spec | 20 min |
| 3 | [03-documentar-para-ia.md](ejercicios/03-documentar-para-ia.md) | Crear un CLAUDE.md completo y verificar que el agente produce mejor código | 20 min |

---

## Prerequisitos

- Experiencia con al menos un proyecto de software real (aplicación web, API, librería)
- Familiaridad con conceptos de arquitectura: módulos, interfaces, dependency injection
- Conocimiento básico de testing (unitarios, integración)
- **Recomendado**: haber completado el [Módulo C1: Adopción en Equipos y Métricas](../modulo-C1-adopcion-equipos/README.md)

---

## Conceptos Clave

- **AI-readability**: la facilidad con la que un agente de código puede entender, navegar y modificar tu código
- **Los 5 pilares**: modularidad explícita, interfaces como contrato, tests como especificación, convenciones explícitas, bajo acoplamiento
- **Métricas proxy**: preguntas concretas para evaluar si tu código es AI-readable
- **Módulos autónomos**: directorios con README propio que el agente puede entender sin explorar todo el proyecto
- **Types como documentación viva**: tipos que guían al agente mejor que cualquier comentario
- **Tests como spec**: tests descriptivos que documentan el comportamiento esperado
- **Documentación como contrato**: CLAUDE.md y `.claude/rules/` como acuerdos bidireccionales
- **ADRs con IA**: Architecture Decision Records generados y consumidos por agentes

---

## Flujo de Trabajo Recomendado

1. **Lee la teoría en orden**: empieza por entender qué es AI-readability (01), luego los patrones positivos (02), los anti-patrones a evitar (03) y finalmente la documentación como contrato (04)
2. **Evalúa tu proyecto actual**: usa los 5 pilares de la teoría 01 para puntuar un proyecto real tuyo
3. **Haz los ejercicios con código real**: los ejercicios están diseñados para aplicarse sobre proyectos existentes, no sobre ejemplos artificiales
4. **Mide el antes/después**: el ejercicio 03 te pide verificar que el agente produce mejor código tras documentar — esta medición es clave

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo C2: Ética y Herramientas](../modulo-C2-etica-herramientas/README.md) |
| **Siguiente** | [Módulo D2: Orquestación Multi-Agente y Automatización a Escala](../modulo-D2-orquestacion-automatizacion/README.md) |
