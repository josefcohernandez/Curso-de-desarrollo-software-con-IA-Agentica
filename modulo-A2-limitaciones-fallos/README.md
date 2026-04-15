# Módulo A2: Limitaciones, Fallos y Pensamiento Crítico

## Descripción General

Los agentes de código no son infalibles. Alucinan APIs, introducen regresiones silenciosas, sobre-ingenierizan soluciones y pierden el hilo en sesiones largas. Conocer estos modos de fallo — y saber detectarlos antes de que lleguen a producción — es **la habilidad más importante** para trabajar profesionalmente con IA.

En este módulo desarrollarás un ojo clínico para los 8 tipos de fallo más comunes de agentes de código, aprenderás estrategias de prevención para cada uno y practicarás la detección con ejercicios realistas que simulan errores reales.

**Tiempo estimado: 2 horas**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Clasificar** los 8 tipos de fallo de agentes de código por frecuencia, peligrosidad y método de detección
2. **Detectar** alucinaciones de código (imports falsos, APIs inventadas, parámetros inexistentes) antes de aceptar cambios
3. **Prevenir** regresiones silenciosas mediante tests, análisis de dependencias y revisión de diffs
4. **Identificar** señales de over-engineering y simplificar código innecesariamente complejo generado por IA
5. **Reconocer** los síntomas de degradación por contexto saturado y aplicar técnicas de mitigación (`/clear`, `/compact`, subagentes)
6. **Decidir** cuándo delegar una tarea a IA y cuándo es más seguro o eficiente hacerla manualmente
7. **Aplicar** un flujo de verificación sistemático que combine compilación, tests y revisión de diff

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-taxonomia-fallos.md](teoria/01-taxonomia-fallos.md) | Taxonomía de fallos de agentes de código: 8 tipos clasificados por frecuencia, peligrosidad y detección | 15 min |
| 2 | [02-alucinaciones-codigo.md](teoria/02-alucinaciones-codigo.md) | Alucinaciones de código: qué son, ejemplos reales, cómo detectarlas y prevenirlas | 20 min |
| 3 | [03-regresiones-silenciosas.md](teoria/03-regresiones-silenciosas.md) | Regresiones silenciosas: el peligro más grave, por qué ocurren y estrategias de prevención | 15 min |
| 4 | [04-over-engineering.md](teoria/04-over-engineering.md) | Over-engineering: cuando el agente hace demasiado, señales de alarma y cómo prevenirlo | 15 min |
| 5 | [05-degradacion-contexto.md](teoria/05-degradacion-contexto.md) | Degradación por contexto saturado: síntomas, cuándo ocurre y técnicas de mitigación | 10 min |
| 6 | [06-cuando-no-usar-ia.md](teoria/06-cuando-no-usar-ia.md) | Cuándo NO usar IA: 6 escenarios donde el humano es mejor, la regla generación vs decisión | 10 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-caza-alucinaciones.md](ejercicios/01-caza-alucinaciones.md) | Caza de alucinaciones: encontrar 5 errores ocultos en código generado por IA | 15 min |
| 2 | [02-deteccion-regresiones.md](ejercicios/02-deteccion-regresiones.md) | Detección de regresiones: encontrar la regresión oculta en un diff de "fix" | 10 min |
| 3 | [03-poda-over-engineering.md](ejercicios/03-poda-over-engineering.md) | Poda de over-engineering: simplificar código inflado manteniendo funcionalidad | 15 min |
| 4 | [04-simulacion-degradacion.md](ejercicios/04-simulacion-degradacion.md) | Simulación de degradación: sesión larga guiada para observar pérdida de coherencia | 10 min |
| 5 | [05-decision-ia-si-no.md](ejercicios/05-decision-ia-si-no.md) | Decisión IA sí/no: clasificar 10 tareas reales y justificar la decisión | 10 min |

---

## Prerequisitos

- Experiencia básica con al menos una herramienta de coding con IA (Claude Code, Cursor, Copilot, etc.)
- Familiaridad con conceptos de desarrollo: tests, control de versiones, imports, diffs
- **Recomendado**: haber completado el [Módulo A1: Prompting Efectivo](../modulo-A1-prompting-efectivo/README.md)

---

## Conceptos Clave

- **Taxonomía de fallos**: 8 categorías (alucinación API, alucinación lógica, regresión silenciosa, over-engineering, divergencia de estilo, degradación de contexto, sesgo de recencia, confianza excesiva)
- **Alucinación de código**: el agente genera código que referencia APIs, funciones o imports que no existen en la realidad
- **Regresión silenciosa**: el cambio más peligroso — arregla lo pedido pero rompe algo en otro lugar sin que nadie lo note
- **Over-engineering**: abstracciones prematuras, error handling innecesario y features no solicitadas que inflan el código
- **Degradación de contexto**: pérdida progresiva de coherencia en sesiones largas (>30-50 intercambios)
- **Regla generación vs decisión**: usa IA para generar, nunca para decidir sin supervisión en contextos de alto riesgo
- **Red de seguridad**: tests + compilación + diff review = tu flujo de verificación antes de aceptar cualquier cambio

---

## Flujo de Trabajo Recomendado

1. **Lee la teoría en orden**: empieza por la taxonomía general (01) y luego profundiza en cada tipo de fallo (02-05)
2. **Interioriza las señales de alarma**: cada teoría incluye indicadores prácticos que puedes aplicar inmediatamente
3. **Termina con la teoría 06**: saber cuándo NO usar IA es tan valioso como saber usarla bien
4. **Haz los ejercicios con código real**: cada ejercicio simula fallos reales — no leas la solución antes de intentarlo
5. **Aplica inmediatamente**: en tu próxima sesión de trabajo con IA, usa la taxonomía para evaluar cada respuesta

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo A1: Prompting Efectivo para Agentes de Código](../modulo-A1-prompting-efectivo/README.md) |
| **Siguiente** | [Módulo A3: Revisión de Código Generado por IA](../modulo-A3-revision-codigo-ia/README.md) |
