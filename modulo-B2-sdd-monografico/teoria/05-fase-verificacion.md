# 05 — Fase 4: Verificación

## Por qué la verificación es una fase separada

La verificación es la última fase de SDD, y la que más frecuentemente se omite. El error de omitirla es creer que si los tests pasan, la implementación cumple la spec.

Los tests demuestran que el código hace lo que los tests verifican. Si los tests no cubren un requisito, ese requisito no está verificado aunque los tests pasen. La verificación cruzada sistemática es la única forma de detectar brechas entre lo especificado y lo implementado.

La verificación también requiere una sesión limpia — un agente sin el contexto de la implementación ve el código con ojos más críticos que el agente que lo escribió.

---

## Verificación cruzada: el proceso

La verificación cruzada toma cada requisito de la spec y lo compara contra la implementación, clasificándolo en tres estados:

| Estado | Significado | Ejemplo |
|--------|-------------|---------|
| **CUMPLIDO** | El requisito está implementado y verificado | RF-01: CRUD de tareas — existe y los tests pasan |
| **PARCIAL** | El requisito está implementado pero de forma incompleta | RF-09: filtrado — funciona por estado pero no por categoría |
| **NO IMPLEMENTADO** | El requisito no está implementado | RNF-03: rate limiting — no existe |

El resultado se documenta en VERIFICACION.md, siguiendo la plantilla del módulo.

---

## El patrón Writer/Reviewer

El patrón Writer/Reviewer aplica el mismo principio que el code review: el que escribe el código no es el mejor revisor de ese código.

**Writer** (la sesión de implementación): escribió el código, conoce el razonamiento detrás de cada decisión, tiene sesgo hacia "esto cumple la spec porque yo sé qué quería decir".

**Reviewer** (la sesión de verificación): nueva sesión sin contexto de implementación, ve el código como lo vería otro desarrollador, sin suposiciones sobre la intención.

Prompt para el Reviewer:

```text
Eres un revisor de código. Tu tarea es verificar que esta implementación
cumple la especificación en SPEC.md.

Para cada requisito (RF-XX, RNF-XX):
1. Busca la evidencia de implementación (archivo, función, línea)
2. Clasifica como CUMPLIDO, PARCIAL o NO IMPLEMENTADO
3. Para PARCIAL: describe qué falta
4. Para NO IMPLEMENTADO: confirma que no existe ningún código que lo cubra

Genera el resultado como VERIFICACION.md siguiendo la plantilla en
plantillas/plantilla-verificacion.md.

No asumas que algo está implementado por ser "razonable" —
solo lo que puedas verificar con evidencia concreta.
```

---

## Qué buscar en la verificación

Más allá de la lista de requisitos, la verificación busca:

### Requisitos no implementados

Requisitos que están en la spec pero no tienen ningún código que los cubra. Ocurre frecuentemente con:
- Requisitos no funcionales (rendimiento, rate limiting, logging)
- Edge cases documentados en la fase de entrevista pero olvidados durante la implementación
- Funcionalidad de la última fase cuando el tiempo se agotó

### Implementaciones que se desvían de la spec

El código hace algo diferente a lo especificado, aunque funcionalmente parece correcto. Por ejemplo, la spec dice que borrar una categoría no borra las tareas asociadas (las deja sin categoría), pero la implementación usa `ON DELETE CASCADE` y borra las tareas.

### Tests faltantes

Hay requisitos implementados pero sin tests. La verificación incluye revisar si el plan de testing de la spec se ejecutó completo.

### Problemas de seguridad no previstos

La implementación introduce vectores de ataque no considerados en la spec. El Reviewer con ojos frescos puede detectar: datos de otros usuarios accesibles, validaciones omitidas, tokens expuestos en logs.

---

## VERIFICACION.md: el artefacto de salida

Un ejemplo de VERIFICACION.md parcial:

```markdown
# VERIFICACION.md — API de Gestión de Tareas v1

**Fecha**: 2025-01-15
**Revisado por**: sesión Reviewer (sin contexto de implementación)
**Implementación**: commit a3f9d2b

---

## Requisitos Funcionales

| ID | Requisito | Estado | Evidencia | Notas |
|----|-----------|--------|-----------|-------|
| RF-01 | CRUD completo de tareas | CUMPLIDO | src/routes/tasks.js (L1-45), tests/tasks.test.js | Todos los tests pasan |
| RF-02 | Campos de tarea | CUMPLIDO | src/models/task.js, migración 001_create_tasks.sql | Tipos correctos |
| RF-05 | Usuario solo ve sus tareas | CUMPLIDO | src/middleware/auth.js (L23), tests/auth.test.js (L67-89) | Verificado con usuario B intentando acceder a datos de usuario A |
| RF-08 | Borrar categoría no borra tareas | PARCIAL | src/routes/categories.js (L78) | Usa SET NULL ✓, pero no hay test que verifique este comportamiento |
| RF-11 | Paginación (20 por defecto, máx 100) | NO IMPLEMENTADO | No encontrado en src/routes/tasks.js | El endpoint devuelve todas las tareas sin paginación |

## Requisitos No Funcionales

| ID | Requisito | Estado | Evidencia | Notas |
|----|-----------|--------|-----------|-------|
| RNF-01 | Respuesta < 200ms p95 | PARCIAL | No hay tests de carga | La implementación no tiene caché ni índices optimizados |
| RNF-02 | JWT 24h | CUMPLIDO | src/middleware/auth.js (L8): expiresIn: '24h' | |
| RNF-03 | Rate limiting 100 req/min | NO IMPLEMENTADO | No encontrado | No hay middleware de rate limiting |

---

## Resumen

| Estado | Cantidad | Porcentaje |
|--------|----------|------------|
| CUMPLIDO | 8 | 57% |
| PARCIAL | 3 | 21% |
| NO IMPLEMENTADO | 3 | 21% |
| **Total** | **14** | |

---

## Plan de Corrección

### Prioridad Alta (bloquean el lanzamiento)
1. **RF-11 — Paginación**: implementar paginación con offset/limit. Estimado: 2h
2. **RNF-03 — Rate limiting**: añadir middleware express-rate-limit. Estimado: 1h

### Prioridad Media (mejoras importantes)
3. **RF-08 — Test de borrado de categoría**: añadir test de integración. Estimado: 30min
4. **RNF-01 — Tests de rendimiento**: añadir índices en user_id y due_date. Estimado: 1h

### Prioridad Baja (mejoras para v2)
5. Métricas de respuesta para verificar RNF-01 en producción
```

---

## Gestionar implementaciones parciales

No alcanzar el 100% en la primera iteración es normal en proyectos reales. La verificación convierte ese estado parcial en algo manejable: tienes una lista priorizada de qué falta, con estimaciones de esfuerzo, en lugar de una sensación difusa de "algo falta".

El proceso después de la verificación:

1. Revisar el plan de corrección con el equipo o stakeholder
2. Decidir qué va al sprint actual y qué va al backlog
3. Para cada ítem del sprint actual: crear una spec mini (sin entrevista formal, pero con requisito claro y criterio de éxito)
4. Implementar, re-verificar

La verificación no es el fin de SDD — es el punto de partida del siguiente ciclo.

---

## El ciclo continuo

SDD no termina con la verificación. Una vez lanzado el sistema, cada modificación significativa pasa por el mismo ciclo:

```text
Entrevista → Spec (actualización de SPEC.md) → Implementación → Verificación
```

Para cambios pequeños (bug fix, ajuste de validación), el ciclo es informal pero sigue el mismo espíritu: ¿qué debería hacer exactamente este cambio? ¿cómo verifico que funciona? ¿hay edge cases?

La diferencia entre un desarrollador que usa IA y un desarrollador que domina el desarrollo con IA agéntica está exactamente aquí: el primero pide al agente que "arregle el bug"; el segundo especifica el comportamiento correcto, implementa, y verifica que la implementación lo cumple.

---

> **Prompt de verificación para Cursor/Copilot**: el prompt del Reviewer funciona igual en cualquier herramienta. La clave es asegurar que la sesión de verificación no tiene acceso al historial de la sesión de implementación — empieza una nueva pestaña/conversación.
