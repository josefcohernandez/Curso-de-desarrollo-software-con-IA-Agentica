# Módulo B1: Estrategias de Desarrollo con IA Agéntica

## Descripción general

Las herramientas de desarrollo con IA son extraordinariamente potentes — pero la herramienta no es el factor limitante. El factor limitante es el proceso. Este módulo enseña las metodologías y estrategias que convierten la potencia del agente en resultados consistentes y predecibles.

Cubrirás el mapa completo de estrategias (SDD, TDD, Gherkin/BDD, PRD, Design Docs, ADR, Visual-Driven, Fan-out), profundizarás en las dos más transformadoras (Gherkin y TDD), y aprenderás los patrones de workflow avanzados que multiplican la efectividad de cualquier agente de código.

**Tiempo estimado: 2.5 horas**

---

## Objetivos de aprendizaje

Al completar este módulo serás capaz de:

1. **Explicar** la diferencia entre "usar IA" y "desarrollar con IA" y por qué la metodología es el factor diferenciador
2. **Elegir** la estrategia de desarrollo más adecuada dado el tipo de proyecto, la complejidad y los stakeholders involucrados
3. **Escribir** features Gherkin correctas desde requisitos vagos y usarlas para generar tests ejecutables con el agente
4. **Ejecutar** el ciclo TDD completo (Red-Green-Refactor) con un agente de código como implementador
5. **Aplicar** el patrón Writer/Reviewer para detectar problemas que una sesión única de implementación no vería
6. **Organizar** trabajo a escala mediante el patrón Fan-out para migraciones y transformaciones masivas

---

## Prerrequisitos

- Haber completado el [Bloque A del curso](../modulo-A1-prompting-efectivo/README.md) (módulos A1-A4) o tener experiencia equivalente
- Familiaridad con al menos un framework de tests (Jest, pytest, Go testing o similar)
- Haber usado un agente de código para implementar al menos una feature real

---

## Duración estimada

**2.5 horas** distribuidas en:
- Teoría (6 ficheros): ~70 minutos de lectura activa
- Ejercicios (4 ficheros): ~80 minutos de práctica

---

## Contenido: teoría

| # | Archivo | Tema | Tiempo |
|---|---------|------|--------|
| 1 | [01-por-que-metodologias.md](teoria/01-por-que-metodologias.md) | Por qué la metodología importa con IA: el loop de la desesperación, la paradoja de velocidad | 10 min |
| 2 | [02-panorama-estrategias.md](teoria/02-panorama-estrategias.md) | Mapa completo de estrategias: SDD, TDD, Gherkin, PRD, Design Docs, ADR, Visual-Driven, Fan-out, Spike | 15 min |
| 3 | [03-gherkin-bdd.md](teoria/03-gherkin-bdd.md) | Gherkin y BDD: sintaxis completa, ejemplos para API/UI/CLI, 7 buenas prácticas, flujo con agente | 15 min |
| 4 | [04-tdd-con-ia.md](teoria/04-tdd-con-ia.md) | TDD con agentes: 4 patrones (Test-First, desde Gherkin, Inverso, cobertura), prompt plantilla | 15 min |
| 5 | [05-patrones-avanzados.md](teoria/05-patrones-avanzados.md) | Writer/Reviewer (5 variantes), Visual-Driven, Fan-out, Git Worktrees | 10 min |
| 6 | [06-otras-estrategias.md](teoria/06-otras-estrategias.md) | PRD, Design Docs, ADR, Spike-then-spec, desarrollo iterativo, matriz consolidada | 5 min |

---

## Ejercicios prácticos

| # | Archivo | Tema | Tiempo |
|---|---------|------|--------|
| 1 | [01-elegir-estrategia.md](ejercicios/01-elegir-estrategia.md) | 5 escenarios reales: elige y justifica la estrategia correcta para cada uno | 15 min |
| 2 | [02-gherkin-desde-requisitos.md](ejercicios/02-gherkin-desde-requisitos.md) | Transforma 3 requisitos vagos en features Gherkin, genera tests e implementa | 20 min |
| 3 | [03-tdd-completo.md](ejercicios/03-tdd-completo.md) | Ciclo Red-Green-Refactor completo: módulo de autenticación con 4 requisitos en cadena | 20 min |
| 4 | [04-writer-reviewer.md](ejercicios/04-writer-reviewer.md) | Implementa un rate limiter (Writer), critica el resultado (Reviewer) y compara | 25 min |

---

## Plantillas disponibles

| Plantilla | Descripción | Cuándo usar |
|-----------|-------------|-------------|
| [plantilla-user-story-gherkin.md](plantillas/plantilla-user-story-gherkin.md) | Estructura completa para escribir features Gherkin con ejemplo de autenticación | Antes de cualquier implementación con criterios de aceptación |
| [plantilla-plan-implementacion.md](plantillas/plantilla-plan-implementacion.md) | Plan por fases con sesiones de agente, verificaciones y estimación de costos | Proyectos de más de 1 día de trabajo |
| [plantilla-decision-estrategia.md](plantillas/plantilla-decision-estrategia.md) | Matriz de puntuación para elegir la estrategia más adecuada | Cuando no está claro qué metodología aplicar |

---

## Conceptos clave

- **Loop de la desesperación**: ciclo de prompts vagos → resultados malos → más prompts → peores resultados. La salida es un proceso, no un prompt mejor.
- **Criterios de aceptación objetivos**: la transformación de "parece bien" en "pasa los tests definidos". Gherkin y TDD son mecanismos para esto.
- **SDD (Spec-Driven Development)**: diseñar antes de codificar. El agente implementa contra una especificación, no contra una descripción vaga.
- **TDD con IA**: el agente ejecuta el CÓMO, tú defines el QUÉ. El ciclo Red-Green-Refactor sigue siendo tuyo de dirigir.
- **Gherkin/BDD**: lenguaje estructurado que los agentes procesan excepcionalmente bien. Given/When/Then mapea directamente a Arrange/Act/Assert.
- **Writer/Reviewer**: sesiones separadas eliminan el sesgo de confirmación del agente. El Reviewer llega al código sin saber por qué se tomaron las decisiones.
- **Fan-out**: distribuir trabajo repetitivo en batches para migraciones y transformaciones masivas con calidad uniforme.
- **TDD Inverso**: para código legacy, añadir cobertura antes de modificar. Primero tests que documentan el comportamiento actual, luego refactor seguro.
- **Spike-then-spec**: exploración de bajo compromiso antes de especificar cuando el dominio es incierto.
- **Ventana deslizante**: concepto de rate limiting donde las peticiones "caducan" pasado un tiempo, no se reinician en bloque.

---

## Flujo de trabajo recomendado

1. **Lee el capítulo 01** para entender el cambio de modelo mental — no omitas este paso aunque tengas experiencia
2. **Lee el capítulo 02** de un tirón para tener el mapa completo de estrategias
3. **Haz el ejercicio 01** inmediatamente después del capítulo 02 — pone a prueba el juicio de selección
4. **Lee el capítulo 03 (Gherkin)** y haz el ejercicio 02 en la misma sesión — son inseparables
5. **Lee el capítulo 04 (TDD)** y haz el ejercicio 03 en la misma sesión
6. **Lee el capítulo 05** y haz el ejercicio 04 (Writer/Reviewer)
7. **Lee el capítulo 06** como referencia — no necesita ejercicio propio

Las plantillas están diseñadas para uso inmediato en proyectos reales. Tenlas a mano desde el primer día.

---

## Relación con el Curso de Claude Code

Este módulo está especialmente relacionado con:

- [Módulo B2 - SDD Monográfico](../modulo-B2-sdd-monografico/README.md): profundización en Spec-Driven Development con las cuatro fases detalladas y casos de estudio end-to-end. Este módulo B1 introduce los conceptos; B2 los desarrolla en profundidad.
- [Módulo E3 - Testing Avanzado y AI Pair Programming](../modulo-E3-testing-pair-programming/README.md): técnicas avanzadas de testing (property-based, mutation, visual regression) que amplían el TDD introducido aquí.
- [M09 - Agentes, Skills y Teams](../../Curso-desarrollo-software-con-Claude-Code/modulo-09-agentes-skills-teams/README.md): los patrones Fan-out y Writer/Reviewer aprovechan las capacidades de subagentes descritas en M09.
- [M06 - Planificación con Opus](../../Curso-desarrollo-software-con-Claude-Code/modulo-06-planificacion-opus/README.md): la fase de planificación en SDD y Design Docs beneficia del enfoque de M06.

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo A4: Debugging Sistemático con IA](../modulo-A4-debugging-sistematico/README.md) |
| **Siguiente** | [Módulo B2: SDD Monográfico](../modulo-B2-sdd-monografico/README.md) |
