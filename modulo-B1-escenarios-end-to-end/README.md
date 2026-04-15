# Módulo B1: El Día a Día — Escenarios End-to-End

## Descripción General

Los módulos del Bloque A te enseñaron las habilidades individuales: prompting, pensamiento crítico, revisión de código y debugging. Pero el trabajo real no son habilidades aisladas — es **combinarlas en flujos completos** que resuelven problemas reales de principio a fin.

En este módulo vas a recorrer cinco escenarios que representan las situaciones más comunes en el día a día de un desarrollador profesional. Cada escenario incluye un workflow paso a paso con prompts concretos, tiempos estimados y lecciones aprendidas. No son recetas rígidas — son mapas mentales que adaptarás a tu contexto.

**Tiempo estimado: 2 horas 30 minutos**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Ejecutar** un onboarding completo a un codebase desconocido en 90 minutos, produciendo un mapa de arquitectura y un primer PR
2. **Resolver** un incidente de producción de forma estructurada en menos de 30 minutos, combinando triage, diagnóstico, fix y validación
3. **Arrancar** un proyecto greenfield desde una idea hasta un MVP funcional con especificación, arquitectura y tests
4. **Abordar** código legacy de forma segura: primero entender, luego proteger con tests, después modificar
5. **Organizar** una jornada de trabajo productiva con IA, alternando entre sesiones profundas y tareas ligeras sin perder contexto
6. **Decidir** qué workflow aplicar según el tipo de tarea, combinando las técnicas de los módulos A1-A4

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-onboarding-codebase.md](teoria/01-onboarding-codebase.md) | Onboarding a un codebase desconocido: workflow de 4 pasos para mapear un proyecto en 90 minutos | 20 min |
| 2 | [02-incidente-produccion.md](teoria/02-incidente-produccion.md) | Incidente en producción: workflow de triage, diagnóstico, fix y post-mortem bajo presión | 20 min |
| 3 | [03-proyecto-greenfield.md](teoria/03-proyecto-greenfield.md) | Proyecto greenfield: de la idea al MVP con especificación, arquitectura y TDD | 20 min |
| 4 | [04-codigo-legacy.md](teoria/04-codigo-legacy.md) | Trabajar con código legacy: tests primero, feature después, refactor opcional | 20 min |
| 5 | [05-dia-tipico.md](teoria/05-dia-tipico.md) | El día típico de un desarrollador con IA: cronograma, patrones de éxito y gestión de fatiga | 15 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-onboarding-simulado.md](ejercicios/01-onboarding-simulado.md) | Onboarding simulado a un repositorio open source real en 90 minutos | 90 min |
| 2 | [02-incidente-simulado.md](ejercicios/02-incidente-simulado.md) | Resolución de un incidente simulado con bug inyectado y logs del error | 30 min |
| 3 | [03-greenfield.md](ejercicios/03-greenfield.md) | Construir un CLI de monitorización de cambios desde cero en 2 horas | 120 min |
| 4 | [04-legacy-rescue.md](ejercicios/04-legacy-rescue.md) | Rescatar un módulo legacy: añadir tests, implementar feature, refactorizar | 60 min |

---

## Prerequisitos

- Haber completado los módulos A1-A4 de este curso (Prompting, Limitaciones, Code Review, Debugging)
- Experiencia práctica con al menos una herramienta de coding con IA (Claude Code, Cursor, Copilot, etc.)
- Familiaridad con git, tests automatizados y al menos un lenguaje backend (Node.js, Python, Go, etc.)
- **Recomendado**: haber completado los módulos M01-M10 del [Curso de Claude Code](https://github.com/josefcohernandez/claude-code-course)

---

## Conceptos Clave

- **Workflow end-to-end**: secuencia completa de pasos desde que identificas un problema hasta que lo resuelves, documentas y verificas
- **Onboarding con agente**: exploración estructurada en 4 fases (overview, deep dive, convenciones, primera contribución) que produce un mapa mental del proyecto
- **Triage de incidentes**: proceso de 5 pasos donde la velocidad importa y el agente investiga en paralelo mientras tú decides
- **Spec-first en greenfield**: la especificación es el artefacto más importante — sin spec, el agente toma decisiones de arquitectura que luego cuestan cambiar
- **Tests-first en legacy**: nunca modificar código legacy sin antes tener una red de seguridad de tests que proteja el comportamiento existente
- **Fatiga de supervisión**: límite cognitivo real al supervisar código generado por IA; máximo 2-3 sesiones profundas al día
- **Higiene de contexto**: usar `/clear` entre tickets no relacionados para evitar degradación por contexto saturado

---

## Flujo de Trabajo Recomendado

1. **Lee la teoría en orden**: cada escenario construye sobre los anteriores, desde situaciones simples (onboarding) hasta complejas (legacy)
2. **Identifica tu escenario más frecuente**: no todos los escenarios tienen el mismo peso en tu día a día — prioriza el que más se parezca a tu trabajo actual
3. **Haz al menos 2 ejercicios**: el ejercicio 01 (onboarding) y el 03 (greenfield) o 04 (legacy) son los más reveladores
4. **Adapta los workflows**: los tiempos y prompts son orientativos — ajústalos a tu stack, equipo y herramienta
5. **Practica la jornada completa**: el archivo 05 (día típico) es tu referencia para organizar el trabajo diario con IA

---

## Relación con el Curso de Claude Code

Este módulo complementa especialmente:

- [M09 - Agentes, Skills y Teams](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-09-agentes-skills-teams/README.md): donde se profundiza en la orquestación de agentes para tareas complejas
- [M12 - Metodologías de Desarrollo con IA](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-12-metodologias-desarrollo-ia/README.md): donde se formalizan los procesos SDD, Gherkin y TDD aplicados aquí

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo A4: Debugging Sistemático con IA](../modulo-A4-debugging-sistematico/README.md) |
| **Siguiente** | [Módulo B2: Trabajar con Diferentes Stacks](../modulo-B2-diferentes-stacks/README.md) |
