# Módulo B2: SDD — Spec-Driven Development (Monográfico)

## Descripción general

SDD es la metodología que separa a los desarrolladores que usan IA de los que **dominan** el desarrollo con IA. Mientras otros acumulan horas de retrabajo porque el agente "interpretó mal lo que querían", quienes practican SDD tienen especificaciones que eliminan la ambigüedad antes de escribir la primera línea de código.

Este módulo monográfico profundiza en las cuatro fases de SDD — entrevista, especificación, implementación y verificación — con nivel de detalle suficiente para aplicarlas en proyectos reales: desde APIs greenfield hasta features en codebases legacy con 200K líneas. Incluye tres casos de estudio end-to-end y ejercicios que simulan los retos reales del trabajo diario.

**Tiempo estimado: 3 horas 30 minutos**

---

## Objetivos de aprendizaje

Al completar este módulo serás capaz de:

1. **Explicar** por qué las especificaciones importan más con IA que sin ella, y cuantificar el ROI de la fase de entrevista
2. **Conducir** una entrevista de requisitos estructurada donde el agente descubre edge cases que tú no habías considerado
3. **Redactar** un SPEC.md completo y verificable siguiendo las cinco reglas de calidad de una spec efectiva
4. **Implementar** desde la spec usando sesiones limpias, implementación por fases y la combinación SDD + Gherkin + TDD
5. **Verificar** la implementación cruzándola contra la spec, generando un VERIFICACION.md con clasificación CUMPLIDO / PARCIAL / NO IMPLEMENTADO
6. **Adaptar** SDD a diferentes contextos: proyectos greenfield, código legacy, APIs y features de frontend
7. **Reconocer** cuándo SDD es la herramienta correcta y cuándo es excesiva para la tarea en mano

---

## Prerrequisitos

- Haber completado el [Módulo B1: Estrategias de Desarrollo con IA Agéntica](../modulo-B1-estrategias-desarrollo-ia/README.md), donde se introduce SDD en el contexto de las metodologías de desarrollo
- Haber completado los módulos A1-A4 de este curso (Prompting, Limitaciones, Code Review, Debugging)
- Experiencia práctica con al menos una herramienta de desarrollo con IA (Claude Code, Cursor, Copilot, etc.)
- Familiaridad con el concepto de TDD (Test-Driven Development) — no es necesario dominarlo

---

## Duración Estimada

**3 horas 30 minutos** desglosadas:

| Bloque | Contenido | Tiempo |
|--------|-----------|--------|
| Teoría | 7 ficheros de teoría | 90 min |
| Ejercicios | 4 ejercicios prácticos | 90 min |
| Plantillas | Uso y personalización | 30 min |

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-filosofia-sdd.md](teoria/01-filosofia-sdd.md) | Filosofía y fundamentos: por qué las specs importan más con IA, ROI, cuándo usar SDD | 10 min |
| 2 | [02-fase-entrevista.md](teoria/02-fase-entrevista.md) | Fase 1 — Entrevista de requisitos: técnicas, categorías de preguntas, ejemplo real | 15 min |
| 3 | [03-fase-especificacion.md](teoria/03-fase-especificacion.md) | Fase 2 — Escribir SPEC.md: estructura completa, reglas de calidad, ejemplo anotado | 15 min |
| 4 | [04-fase-implementacion.md](teoria/04-fase-implementacion.md) | Fase 3 — De la spec al código: sesión limpia, implementación por fases, SDD + TDD + Gherkin | 15 min |
| 5 | [05-fase-verificacion.md](teoria/05-fase-verificacion.md) | Fase 4 — Verificación: Writer/Reviewer, VERIFICACION.md, gestión de gaps | 10 min |
| 6 | [06-sdd-en-contexto.md](teoria/06-sdd-en-contexto.md) | SDD aplicado: greenfield, legacy, APIs, frontend, microservicios — y cuándo no usarlo | 15 min |
| 7 | [07-casos-estudio.md](teoria/07-casos-estudio.md) | 3 casos de estudio end-to-end: API, feature en legacy y refactoring de autenticación | 10 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-spec-desde-cero.md](ejercicios/01-spec-desde-cero.md) | Crear spec completa via entrevista: app de notas con categorías y sincronización | 25 min |
| 2 | [02-spec-para-feature-existente.md](ejercicios/02-spec-para-feature-existente.md) | Spec para feature en proyecto existente: sistema de etiquetas en API de tareas | 25 min |
| 3 | [03-sdd-ciclo-completo.md](ejercicios/03-sdd-ciclo-completo.md) | Ciclo SDD completo: API de tareas con usuarios, categorías y prioridades | 30 min |
| 4 | [04-verificacion-cruzada.md](ejercicios/04-verificacion-cruzada.md) | Verificación cruzada: encontrar gaps entre SPEC.md y una implementación con errores intencionales | 10 min |

### Plantillas

| # | Archivo | Descripción |
|---|---------|-------------|
| 1 | [plantilla-spec.md](plantillas/plantilla-spec.md) | Plantilla completa de SPEC.md con instrucciones, ejemplos y checklist de calidad |
| 2 | [plantilla-verificacion.md](plantillas/plantilla-verificacion.md) | Plantilla de VERIFICACION.md con tabla de clasificación y plan de corrección |

---

## Conceptos clave

- **SDD (Spec-Driven Development)**: metodología de desarrollo donde la especificación formal precede siempre a la implementación, especialmente relevante cuando el implementador es un agente de IA
- **Spec como single source of truth**: el SPEC.md es la referencia definitiva del comportamiento esperado del sistema — todo lo que no esté en la spec no existe
- **Entrevista de requisitos**: sesión estructurada donde el agente hace preguntas sistemáticas para descubrir edge cases, requisitos no funcionales y restricciones que el desarrollador no había considerado
- **Implementación por fases**: dividir la spec en fases ordenadas por dependencias e implementar una a la vez, con tests al final de cada fase
- **Sesión limpia**: nueva sesión del agente sin contexto de la fase de especificación — evita el sesgo de confirmación donde el agente que escribió la spec tiende a "ver" que la cumple
- **Writer/Reviewer**: patrón de verificación donde una sesión diferente (sin contexto de implementación) revisa el código contra la spec
- **VERIFICACION.md**: artefacto formal que documenta el estado de cada requisito: CUMPLIDO, PARCIAL o NO IMPLEMENTADO, con evidencia y plan de corrección
- **Triple stack**: combinación SDD + Gherkin + TDD — la metodología más robusta para desarrollo profesional con IA agéntica

---

## Flujo de trabajo recomendado

1. **Lee los 7 ficheros de teoría en orden**: la filosofía (01) contextualiza todo lo demás; las fases (02-05) son el núcleo; el contexto (06) y los casos de estudio (07) te preparan para los ejercicios
2. **Descarga las plantillas**: ten `plantilla-spec.md` y `plantilla-verificacion.md` abiertas mientras haces los ejercicios
3. **Haz el ejercicio 01**: crear una spec desde cero es la habilidad más fundamental — aquí practican la entrevista y la redacción de spec
4. **Haz el ejercicio 03** (ciclo completo): es el ejercicio más representativo del trabajo real — las 4 fases de SDD de principio a fin
5. **Haz el ejercicio 04** (verificación cruzada): aprenderás a detectar gaps que normalmente pasan desapercibidos
6. **Adapta las plantillas** a tu contexto: añade secciones específicas de tu dominio (autenticación, internacionalización, compliance, etc.)

---

## Relación con el Curso de Claude Code

Este módulo expande y profundiza significativamente el contenido de:

- [Módulo B1 - Estrategias de Desarrollo con IA](../modulo-B1-estrategias-desarrollo-ia/README.md): donde se introducen los patrones Gherkin/BDD, TDD y Writer/Reviewer en el contexto del desarrollo con IA agéntica. Este módulo B2 profundiza específicamente en SDD con más detalle en cada fase, casos de estudio adicionales y aplicación a contextos variados
- [M09 - Agentes, Skills y Teams](../../Curso-desarrollo-software-con-Claude-Code/modulo-09-agentes-skills-teams/README.md): el patrón Writer/Reviewer de la fase de verificación utiliza el concepto de múltiples agentes con roles distintos

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo B1: Estrategias de Desarrollo con IA Agéntica](../modulo-B1-estrategias-desarrollo-ia/README.md) |
| **Siguiente** | [Módulo C1: El Día a Día — Escenarios End-to-End](../modulo-C1-escenarios-end-to-end/README.md) |
