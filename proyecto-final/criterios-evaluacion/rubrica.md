# Rúbrica de Evaluación del Proyecto Final

## Estructura de Evaluación

El proyecto se evalúa en 5 criterios. Cada criterio tiene 3 niveles: Excelente, Bueno y Necesita Mejora. La puntuación total es ponderada según los pesos indicados.

---

## Criterio 1: Calidad de los Prompts (25%)

Evalúa si los prompts son específicos, acotados, con verificación, y si siguen los patrones del [Módulo A1](../../modulo-A1-prompting-efectivo/README.md).

| Nivel | Descripción | Ejemplos |
|-------|-------------|----------|
| **Excelente** | Los prompts incluyen contexto, restricciones, resultado esperado y forma de verificar. Se usan los patrones correctos para cada tipo de tarea. Se aplica `/clear` entre tareas no relacionadas. | "El test en test/tasks.test.js:45 falla con 'TypeError'. El código relevante está en src/services/task.js. Investiga y corrige. Verifica que los 12 tests pasan." |
| **Bueno** | Los prompts son específicos y producen resultados aceptables, pero les falta algún componente (generalmente la verificación o las restricciones). | "Fix the bug in the task service that causes a TypeError when updating a task" |
| **Necesita Mejora** | Los prompts son vagos, sin restricciones ni verificación. Se acumula scope creep. Muchas iteraciones para cada tarea. | "Fix the tests", "Add authentication", "Make it work" |

---

## Criterio 2: Pensamiento Crítico (25%)

Evalúa si el developer detecta y corrige fallos del agente, revisa el código antes de aceptarlo, y aplica las técnicas del [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md) y [Módulo A3](../../modulo-A3-revision-codigo-ia/README.md).

| Nivel | Descripción | Ejemplos |
|-------|-------------|----------|
| **Excelente** | Se detectan y documentan al menos 3 errores del agente. Se revisa cada output antes de aceptar. La code review del Día 2 tiene hallazgos reales y clasificados. Se cuestionan decisiones del agente. | "El agente generó un middleware de auth que no verifica expiración del token. Lo detecté en review y pedí corrección específica." |
| **Bueno** | Se detectan algunos errores. Se revisa el código, pero de forma superficial. La code review tiene hallazgos pero sin priorización clara. | Se detecta un bug del agente pero no se documentan los motivos ni el proceso de detección. |
| **Necesita Mejora** | Se acepta todo lo que genera el agente sin cuestionarlo. No se detectan errores obvios. La code review es mecánica o está ausente. | El código tiene bugs visibles que no se mencionan en la retrospectiva. |

---

## Criterio 3: Calidad del Código (20%)

Evalúa el resultado final: funcionalidad, tests, ausencia de over-engineering, consistencia.

| Nivel | Descripción | Ejemplos |
|-------|-------------|----------|
| **Excelente** | Los tests cubren happy path y edge cases. El código es consistente en estilo. No hay abstracciones innecesarias. Los endpoints siguen las convenciones REST. El manejo de errores es apropiado (ni excesivo ni ausente). | CRUD completo con tests de validación, error handling, y edge cases como "tarea no encontrada" o "input inválido". |
| **Bueno** | Los tests cubren el happy path. El código funciona correctamente. Hay alguna inconsistencia de estilo o abstracción innecesaria, pero nada grave. | CRUD funciona, tests del happy path pasan, pero faltan tests de edge cases como "ID no existente". |
| **Necesita Mejora** | Los tests son mínimos o no significativos. El código tiene bugs no detectados. Over-engineering visible (ej: factory pattern para 2 entidades). Inconsistencias de estilo. | Tests que solo verifican "no lanza error". Código con capas innecesarias. |

---

## Criterio 4: Proceso y Metodología (20%)

Evalúa si se siguieron los workflows de cada módulo: TDD, Plan Mode, `/clear`, subagentes, debugging sistemático.

| Nivel | Descripción | Ejemplos |
|-------|-------------|----------|
| **Excelente** | Se usa TDD (tests primero) en la implementación. Se usa Plan Mode para diseño. Se aplica `/clear` entre contextos. El debugging del Día 2 sigue un proceso sistemático. Se documenta el proceso, no solo el resultado. | "Fase 1.4: Escribí primero los tests para el CRUD, luego pedí implementación. El agente falló en el test de DELETE idempotente, lo que reveló un bug en mi spec." |
| **Bueno** | Se intenta seguir el proceso pero no siempre de forma rigurosa. Se usa TDD parcialmente. Se documenta el proceso de forma básica. | Tests y código se escriben al mismo tiempo (no estrictamente TDD), pero se verifica al final. |
| **Necesita Mejora** | No hay proceso visible. El código se genera y se acepta sin workflow. No se usa Plan Mode ni `/clear`. El debugging es ad-hoc. | Se genera todo de una vez con un solo prompt largo, sin iteración ni verificación intermedia. |

---

## Criterio 5: Documentación (10%)

Evalúa la calidad de SPEC.md, ARCHITECTURE.md, CLAUDE.md, la auditoría de seguridad y RETROSPECTIVE.md.

| Nivel | Descripción | Ejemplos |
|-------|-------------|----------|
| **Excelente** | Todos los documentos están completos y son útiles. La retrospectiva incluye métricas cuantitativas y reflexiones cualitativas. El CLAUDE.md es utilizable por otro developer. La auditoría tiene hallazgos reales. | RETROSPECTIVE.md con tiempos por fase, prompts que funcionaron vs que fallaron, y lecciones aplicables a futuros proyectos. |
| **Bueno** | Los documentos existen y cubren lo básico. La retrospectiva tiene métricas pero poca reflexión. El CLAUDE.md es genérico. | Documentos completos pero sin profundidad. CLAUDE.md copiado del template sin adaptar. |
| **Necesita Mejora** | Faltan documentos o están incompletos. La retrospectiva es superficial o ausente. No hay auditoría de seguridad. | Falta SPEC.md o ARCHITECTURE.md. RETROSPECTIVE.md de 5 líneas sin métricas. |

---

## Tabla Resumen

| Criterio | Peso | Excelente | Bueno | Necesita Mejora |
|----------|------|-----------|-------|-----------------|
| Calidad de los prompts | 25% | 25 | 15 | 5 |
| Pensamiento crítico | 25% | 25 | 15 | 5 |
| Calidad del código | 20% | 20 | 12 | 4 |
| Proceso y metodología | 20% | 20 | 12 | 4 |
| Documentación | 10% | 10 | 6 | 2 |
| **Total** | **100%** | **100** | **60** | **20** |

---

## Autoevaluación

Usa esta tabla para evaluar tu propio proyecto antes de darlo por terminado:

```markdown
## Mi Autoevaluación

| Criterio | Mi nivel | Justificación breve |
|----------|----------|---------------------|
| Calidad de los prompts | [ ] Excelente [ ] Bueno [ ] Necesita Mejora | |
| Pensamiento crítico | [ ] Excelente [ ] Bueno [ ] Necesita Mejora | |
| Calidad del código | [ ] Excelente [ ] Bueno [ ] Necesita Mejora | |
| Proceso y metodología | [ ] Excelente [ ] Bueno [ ] Necesita Mejora | |
| Documentación | [ ] Excelente [ ] Bueno [ ] Necesita Mejora | |

Puntuación estimada: ___/100
Área a mejorar: ___
```

---

## Navegación

| | |
|---|---|
| **Enunciado** | [Enunciado del proyecto](../enunciado/README.md) |
| **Proyecto** | [README del proyecto](../README.md) |
