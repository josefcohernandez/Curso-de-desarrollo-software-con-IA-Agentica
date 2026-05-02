# Módulo A3: Revisión de Código Generado por IA

## Descripción general

Aprender a leer código con ojos de detective, no de espectador. La revisión de código generado por IA requiere un enfoque diferente al code review tradicional: el código compila, los tests pasan, se lee bien... pero puede esconder regresiones silenciosas, abstracciones innecesarias y deuda técnica que nadie cuestiona porque "el agente lo generó y funciona".

En este módulo desarrollarás un método sistemático para revisar PRs generados por IA, aprenderás a identificar las red flags más comunes y practicarás con ejercicios que simulan los errores reales que cometen los agentes de código.

**Tiempo estimado: 2 horas**

---

## Objetivos de aprendizaje

Al completar este módulo serás capaz de:

1. **Explicar** por qué la revisión de código IA requiere un enfoque diferente al code review tradicional
2. **Aplicar** un checklist de revisión priorizado (crítico/alto/medio/bajo) a cualquier PR generado por IA
3. **Identificar** las 8 red flags más comunes del código generado por agentes
4. **Usar** el método de lectura en 3 pasadas para evaluar diffs de forma eficiente
5. **Detectar** formas de deuda técnica silenciosa introducida por IA y aplicar estrategias para combatirla

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-por-que-es-diferente.md](teoria/01-por-que-es-diferente.md) | Por qué la revisión de código IA difiere de la revisión de código humano | 10 min |
| 2 | [02-que-buscar.md](teoria/02-que-buscar.md) | Checklist de revisión priorizado con ejemplos concretos | 15 min |
| 3 | [03-red-flags-codigo-ia.md](teoria/03-red-flags-codigo-ia.md) | 8 señales de alerta específicas del código generado por agentes | 15 min |
| 4 | [04-patrones-diff-review.md](teoria/04-patrones-diff-review.md) | Método de lectura en 3 pasadas y herramientas de revisión | 10 min |
| 5 | [05-deuda-tecnica-silenciosa.md](teoria/05-deuda-tecnica-silenciosa.md) | Código que funciona hoy pero acumula problemas para mañana | 10 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-review-pr-ia.md](ejercicios/01-review-pr-ia.md) | Realizar code review exhaustivo de un PR con 8+ issues ocultos | 20 min |
| 2 | [02-encontrar-regresion.md](ejercicios/02-encontrar-regresion.md) | Analizar un diff de 200 líneas para encontrar una regresión escondida | 15 min |
| 3 | [03-poda-complejidad.md](ejercicios/03-poda-complejidad.md) | Simplificar código over-engineered reduciendo 30%+ de líneas | 15 min |
| 4 | [04-red-flags-tests.md](ejercicios/04-red-flags-tests.md) | Identificar tests superficiales y reescribirlos correctamente | 10 min |

---

## Prerrequisitos

- Haber completado el [Módulo A2: Limitaciones, Fallos y Pensamiento Crítico](../modulo-A2-limitaciones-fallos/README.md)
- Experiencia básica con code review (haber revisado o recibido revisiones de código)
- Familiaridad con `git diff` y pull requests

---

## Conceptos clave

- **Rubber stamp**: aceptar código sin revisarlo porque "se ve bien"
- **Errores coherentes**: código que compila y se lee bien pero hace lo incorrecto
- **Red flag**: señal de alerta que indica un problema potencial en código generado
- **Lectura en 3 pasadas**: método sistemático de scope → lógica → calidad
- **Deuda técnica silenciosa**: problemas acumulados que nadie cuestiona porque "el agente lo generó y funciona"
- **Test del diff**: si el diff toca archivos que no mencionaste, investiga por qué

---

## Flujo de trabajo recomendado

1. **Lee la teoría en orden**: cada archivo construye sobre el anterior, desde los fundamentos hasta las técnicas avanzadas (01 → 05)
2. **Haz una pausa y revisa un PR reciente** de tu propio trabajo con IA
3. **Completa los ejercicios en orden**: cada ejercicio simula escenarios reales de revisión
4. **Crea tu propio checklist personalizado** para tu stack

---

## Relación con el Curso de Claude Code

Este módulo complementa especialmente:

- [Módulo B1 - Estrategias de Desarrollo con IA](../modulo-B1-estrategias-desarrollo-ia/README.md): donde se cubren Gherkin/BDD, TDD y patrones avanzados de workflow como Writer/Reviewer — este módulo A3 se centra en el oficio de revisar el resultado, una habilidad transferible a cualquier herramienta
- [Módulo B2 - SDD Monográfico](../modulo-B2-sdd-monografico/README.md): donde se detalla la metodología Spec-Driven Development que antecede y guía el código que aquí se revisa

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo A2: Limitaciones, Fallos y Pensamiento Crítico](../modulo-A2-limitaciones-fallos/README.md) |
| **Siguiente** | [Módulo A4: Debugging Sistemático con IA](../modulo-A4-debugging-sistematico/README.md) |
