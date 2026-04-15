# Módulo D3: Testing Avanzado y AI Pair Programming

## Descripción General

Las prácticas de testing tradicionales (unit tests, integration tests, coverage) son necesarias pero insuficientes cuando se desarrolla con asistencia de IA. Este módulo explora técnicas de testing de siguiente nivel -- property-based testing, mutation testing y visual regression -- que aprovechan las capacidades generativas de los agentes de código. Además, aprenderás a colaborar en tiempo real con un agente de IA usando cinco patrones de pair programming, y a gestionar tu productividad y flow state en sesiones prolongadas.

**Tiempo estimado: 2 horas**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Diseñar** property-based tests que validen invariantes en lugar de casos concretos, usando Hypothesis, fast-check o QuickCheck
2. **Aplicar** fuzzing asistido por IA para descubrir vulnerabilidades y edge cases en validación, parsing y APIs
3. **Implementar** mutation testing para evaluar la calidad real de tu suite de tests más allá del porcentaje de coverage
4. **Integrar** visual regression testing en tu pipeline de CI/CD usando Playwright, Chromatic o Percy
5. **Seleccionar** el modo de pair programming adecuado (Driver/Navigator, Ping-pong TDD, Mob, Investigación, Delegación) según el tipo de tarea
6. **Gestionar** tu flow state y fatiga de supervisión en sesiones largas con IA, aplicando la regla del 50/50 y el límite de 90 minutos
7. **Medir** tu productividad personal con métricas objetivas: ratio de iteraciones, coste por tarea y satisfacción subjetiva

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-testing-generativo.md](teoria/01-testing-generativo.md) | Testing generativo con IA: property-based testing, fuzzing, generación de tests desde specs | 15 min |
| 2 | [02-mutation-testing.md](teoria/02-mutation-testing.md) | Mutation testing y calidad de tests: mutantes, herramientas, mutation score vs coverage | 15 min |
| 3 | [03-visual-regression.md](teoria/03-visual-regression.md) | Visual regression testing: comparación de screenshots, herramientas y CI/CD | 15 min |
| 4 | [04-pair-programming-ia.md](teoria/04-pair-programming-ia.md) | AI pair programming: 5 modos de colaboración en tiempo real | 20 min |
| 5 | [05-flow-state-productividad.md](teoria/05-flow-state-productividad.md) | Flow state y productividad: latencia, fatiga de supervisión, métricas personales | 15 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-property-based-testing.md](ejercicios/01-property-based-testing.md) | Generar property-based tests para un módulo de validación y comparar con tests unitarios tradicionales | 20 min |
| 2 | [02-mutation-testing.md](ejercicios/02-mutation-testing.md) | Aplicar mutation testing a un módulo con "buena cobertura" y descubrir gaps ocultos | 20 min |
| 3 | [03-sesion-pair-programming.md](ejercicios/03-sesion-pair-programming.md) | Sesión de 60 minutos usando los 5 modos de pair programming, documentando transiciones | 20 min |

---

## Prerequisitos

- Experiencia escribiendo tests unitarios y de integración en al menos un lenguaje
- Familiaridad con el concepto de code coverage y herramientas de testing (Jest, pytest, JUnit o similares)
- Haber completado los módulos D1 y D2 del curso, o tener experiencia equivalente con agentes de código
- **Recomendado**: experiencia con TDD (Test-Driven Development)

---

## Conceptos Clave

- **Property-based testing**: definir propiedades invariantes en vez de ejemplos concretos; el framework genera los inputs
- **Fuzzing**: generación de inputs malformados, extremos y adversariales para encontrar edge cases
- **Mutation testing**: modificar el código fuente (mutantes) y verificar que los tests los detectan; mutation score como métrica de calidad
- **Visual regression**: comparar screenshots antes/después para detectar regresiones visuales no cubiertas por tests funcionales
- **Pair programming con IA**: 5 modos de colaboración (Driver/Navigator, Ping-pong TDD, Mob, Investigación, Delegación)
- **Flow state**: estado de concentración profunda que la latencia del agente puede interrumpir; requiere estrategias activas de gestión
- **Fatiga de supervisión**: agotamiento por revisar código generado por IA durante períodos largos; regla del 50/50, límite de 90 minutos

---

## Flujo de Trabajo Recomendado

1. **Lee la teoría en orden**: los tres primeros archivos cubren técnicas de testing progresivamente más avanzadas; los dos últimos se centran en la colaboración humano-IA
2. **Practica cada técnica de testing**: instala al menos una herramienta (Hypothesis, Stryker, Playwright) y ejecuta los ejemplos
3. **Haz el ejercicio 1 primero**: la comparativa entre property-based tests y unit tests es reveladora
4. **Reserva 60 minutos sin interrupciones** para el ejercicio 3: la sesión de pair programming requiere concentración continua
5. **Reflexiona sobre tu productividad**: después del ejercicio 3, revisa tus notas y calcula tus métricas personales

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo D2: Orquestación y Automatización](../modulo-D2-orquestacion-automatizacion/README.md) |
| **Siguiente** | [Módulo D4: Seguridad, Costes y Optimización](../modulo-D4-seguridad-costes/README.md) |
