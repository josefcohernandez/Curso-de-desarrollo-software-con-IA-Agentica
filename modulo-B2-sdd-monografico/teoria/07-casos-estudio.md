# 07 — Casos de Estudio End-to-End

Tres casos reales que ilustran SDD aplicado a diferentes contextos. Cada caso incluye el problema, el flujo SDD, las decisiones clave, el resultado y las lecciones aprendidas.

---

## Caso 1: API de gestión de inventario desde cero

### Contexto

Una startup de retail necesita una API REST para gestionar el inventario de sus tiendas físicas. El equipo tiene dos desarrolladores, un deadline de 2 semanas, y no han construido una API desde cero antes con agentes IA. El fundador describe el requisito en una reunión de 5 minutos: "necesitamos ver qué hay en stock, actualizar cantidades, y saber cuándo nos quedamos sin algo".

### El problema sin SDD

Esa descripción de 5 minutos tiene al menos 8 decisiones de diseño no tomadas: ¿hay múltiples almacenes? ¿se hace seguimiento por lote? ¿hay concepto de stock mínimo? ¿cómo se gestionan los ajustes de inventario (pérdidas, devoluciones)? ¿quién puede modificar el inventario? ¿hay auditoría de cambios? Sin responder estas preguntas, el agente va a tomar esas decisiones solo.

### La entrevista: lo que descubrió

La entrevista duró 12 minutos y generó 9 preguntas. Las más importantes:

**Agente**: "¿El inventario es por almacén o centralizado?"

**Fundador**: "Por almacén. Tenemos 3 tiendas y el stock de cada una es independiente."

Esta respuesta cambió completamente el modelo de datos: sin la entrevista, la implementación habría sido un inventario centralizado, que luego habría necesitado un refactoring completo.

**Agente**: "¿Qué ocurre cuando el stock de un producto llega a cero?"

**Fundador**: "Debemos recibir una alerta para reordenar. Cada producto tiene un stock mínimo configurado por el gestor."

Esto añadió un requisito completo (alertas de stock mínimo) que no estaba en la descripción original.

**Agente**: "¿Necesitáis auditoría de quién hizo qué cambio en el inventario?"

**Fundador**: "Sí, es obligatorio. Hemos tenido problemas de robo interno."

Esto añadió el requisito de audit log, que habría sido imposible de añadir a posteriori sin refactorizar la base de datos.

### La spec resultante (extracto)

```markdown
## Requisitos Funcionales

### Multi-almacén
- RF-01: El inventario se gestiona por almacén. Hay 3 almacenes (tiendas).
- RF-02: El stock de un producto es independiente por almacén.
- RF-03: Un usuario puede tener acceso a uno o varios almacenes (no todos).

### Alertas de stock mínimo
- RF-08: Cada producto tiene un nivel mínimo configurable por almacén.
- RF-09: Cuando el stock cae por debajo del mínimo, se genera una alerta.
- RF-10: Las alertas se consultan via API (GET /alerts) — no hay email en v1.

### Auditoría
- RF-11: Todo cambio de stock genera un registro inmutable con: quién, cuándo, 
         qué cambió (de N a M), y el motivo (venta, ajuste, devolución, pérdida).
- RF-12: El audit log es consultable por producto, por almacén y por usuario.

## Exclusiones
- Sin integración con sistemas de punto de venta (POS) en v1
- Sin órdenes de compra automáticas (las alertas son solo informativas)
- Sin inventario de multi-ubicación dentro del mismo almacén
```

### Resultado

La API se implementó en 4 días de trabajo real (distribuidos en 2 semanas con otras responsabilidades). La cobertura de tests fue del 89%. El audit log, que el fundador consideraba un requisito de segundo orden, acabó siendo la feature más usada en las primeras semanas.

### Lecciones

1. **La entrevista descubrió el requisito de multi-almacén**, que era la decisión de arquitectura más importante del proyecto. Sin la entrevista, la implementación habría sido incorrecta desde el inicio.
2. **El audit log es el tipo de requisito que siempre se olvida sin entrevista**: no está en la descripción inicial, pero es crítico y muy difícil de añadir después si el modelo de datos no lo contempla.
3. **Las exclusiones evitaron scope creep**: el agente propuso durante la implementación generar emails automáticos de alerta. Las exclusiones explícitas lo bloquearon.

---

## Caso 2: Feature de notificaciones en e-commerce existente

### Contexto

Un e-commerce con 200K líneas de código (Node.js + React) necesita un sistema de notificaciones in-app: "cuando el pedido cambia de estado, el usuario debe verlo en la app". El codebase tiene 4 años de antigüedad, arquitectura de capas, y buenos tests de integración. El equipo tiene miedo de introducir regresiones en el flujo de pagos.

### El reto: SDD con código existente

En este caso, la entrevista no solo cubre los requisitos de la nueva feature — también cubre cómo integrarse con el sistema existente sin romperlo.

Las preguntas más importantes de la entrevista:

**Agente**: "¿El sistema actual tiene algún mecanismo de eventos o pub/sub?"

**Desarrollador**: "Sí, usamos EventEmitter internamente para algunas cosas. El flujo de pedidos emite eventos cuando cambia de estado."

Esto definió el punto de integración: el sistema de notificaciones escucharía los eventos existentes en lugar de modificar el flujo de pedidos.

**Agente**: "¿Las notificaciones deben ser en tiempo real (WebSocket) o es suficiente con polling?"

**Desarrollador**: "Polling está bien. Los usuarios comprueban el estado periódicamente."

Esta respuesta evitó añadir una dependencia de WebSocket que habría complicado significativamente la implementación.

### La spec como "contrato de no agresión"

La sección más importante de la spec fue la de integraciones:

```markdown
## Integraciones con el sistema existente

### Punto de integración: EventEmitter de pedidos
- El sistema de notificaciones ESCUCHA eventos, no modifica el flujo de pedidos.
- Eventos que escucha: order.status_changed, order.shipped, order.delivered
- El sistema de pedidos NO SABE que existe el sistema de notificaciones.

### Archivos que NO se deben modificar
- src/orders/ — el flujo de pedidos es intocable
- src/payments/ — sin ninguna razón
- src/auth/ — sin ninguna razón
- Cualquier migración existente

### Archivos de entrada para el sistema de notificaciones
- src/notifications/ (nuevo directorio)
- Una nueva migración: 020_create_notifications.sql
- src/events/index.js — añadir listeners, no modificar listeners existentes
```

Esta sección fue la más discutida con el equipo antes de empezar — y fue la que evitó que el agente tocara el flujo de pagos.

### Resultado

La feature se implementó en 2 días. Cero regresiones en el flujo de pedidos (verificado con los 340 tests de integración existentes). La spec documentó el contrato de integración de forma tan clara que otro desarrollador del equipo pudo entender la feature sin haber participado en la implementación.

### Lecciones

1. **La spec como "contrato de no agresión"**: listar explícitamente los archivos que NO se pueden tocar es tan importante como listar qué se va a construir.
2. **Entrevistar sobre el sistema existente**: las preguntas sobre la arquitectura actual son tan importantes como las preguntas sobre la nueva feature.
3. **El punto de integración es la decisión de diseño más importante**: en este caso, escuchar eventos en lugar de modificar el flujo fue la decisión que hizo que la feature fuera segura de implementar.

---

## Caso 3: Refactoring de módulo de autenticación

### Contexto

Un módulo de autenticación de 3 años de antigüedad, escrito originalmente por un desarrollador que ya no está en el equipo. Mezcla lógica de negocio con acceso a base de datos en el mismo archivo. Tiene tests unitarios frágiles que mockean todo. El equipo quiere refactorizarlo para poder añadir OAuth2 en el futuro, pero tiene pánico de romper el login.

### SDD para refactoring: diferente al greenfield

El objetivo del refactoring no es añadir funcionalidad — es mejorar la estructura manteniendo el comportamiento observable exactamente igual. Esto cambia SDD de forma fundamental: la spec del estado deseado debe ser una extensión de la spec del estado actual, no una sustitución.

**Paso 1 — Reverse-spec del estado actual** (25 minutos):

```text
Lee src/auth/auth.js y documenta en SPEC-AUTH-ACTUAL.md:
- Qué hace: las operaciones de autenticación que soporta
- Inputs y outputs de cada función pública
- Side effects: qué escribe en base de datos, qué emite
- Comportamientos implícitos: validaciones, transformaciones de datos
- Problemas actuales: qué mezcla de responsabilidades existe
```

El resultado fue una spec de 2 páginas que documentó por primera vez el comportamiento real del módulo.

**Paso 2 — Tests del estado actual** (45 minutos):

```text
Escribe tests de integración para el comportamiento documentado en SPEC-AUTH-ACTUAL.md.
No cambies el código de producción.
Usa base de datos real (no mocks).
Cubre: login correcto, login incorrecto, token expirado, token malformado,
cambio de contraseña, reset de contraseña.
```

**Paso 3 — SPEC del estado deseado** (15 minutos):

```markdown
## Estado deseado: módulo de autenticación refactorizado

### Separación de responsabilidades
- auth/handlers.js: manejo de HTTP request/response (sin lógica de negocio)
- auth/service.js: lógica de negocio de autenticación
- auth/repository.js: acceso a base de datos

### API pública (sin cambios)
Las firmas de las funciones públicas deben ser IDÉNTICAS al estado actual.
El refactoring es interno — ningún caller debe cambiar.

### Mejoras
- Sin lógica de negocio en handlers
- Sin SQL directo en service (usar repository)
- Tests de unidad posibles sin base de datos (service testeable con mocks de repository)
```

**Paso 4 — Gap analysis**:

```text
Compara SPEC-AUTH-ACTUAL.md con la nueva arquitectura en SPEC-AUTH-DESEADO.md.
Genera un plan de migración en orden de menor riesgo:
- Qué se mueve primero (menos acoplado)
- Qué se mueve al final (más crítico)
- Dónde están los puntos de riesgo
```

### Resultado

El refactoring tomó 4 horas. Los 15 tests de integración del estado actual pasaron sin modificaciones durante todo el proceso — fueron la red de seguridad que hizo posible el refactoring con confianza. El módulo refactorizado tiene ahora tests de unidad del service (con mocks del repository) además de los tests de integración existentes.

### Lecciones

1. **La reverse-spec es el artefacto más valioso**: documentar el comportamiento actual antes de cambiarlo es lo que hace que el refactoring sea seguro.
2. **Tests de integración antes que tests de unidad**: en código legacy, los tests de integración con base de datos real verifican el comportamiento observable. Los tests unitarios con mocks verifican la implementación — que es justo lo que va a cambiar.
3. **La API pública como contrato de no regresión**: si nadie de fuera del módulo nota el refactoring, el refactoring fue exitoso.

---

## Patrones comunes en los tres casos

| Patrón | Caso 1 | Caso 2 | Caso 3 |
|--------|--------|--------|--------|
| La entrevista descubrió requisitos críticos | Multi-almacén, audit log | Punto de integración via eventos | Comportamiento implícito del módulo |
| Las exclusiones evitaron scope creep | No emails, no POS | No WebSocket, no modificar pedidos | No cambiar API pública |
| La verificación encontró gaps | Tests de alertas faltaban | Test de no-regresión en pagos | Cobertura de edge cases de tokens |
| El ROI de SDD vs. no SDD | 4 días vs. estimado de 2 semanas de retrabajo | Cero regresiones vs. alto riesgo | Refactoring con confianza vs. parálisis |
