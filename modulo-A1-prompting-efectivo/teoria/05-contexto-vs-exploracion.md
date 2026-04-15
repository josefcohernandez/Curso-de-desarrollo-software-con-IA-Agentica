# Cuándo Dar Contexto vs. Dejar que el Agente Explore

## Introducción

Una de las decisiones más frecuentes al escribir un prompt es: "cuánto contexto le doy?". Dar demasiado contexto desperdicia tokens y puede incluir información contradictoria. Dar poco contexto obliga al agente a explorar, lo cual consume tiempo pero a veces produce mejores resultados. La respuesta no es "siempre más" ni "siempre menos" — depende de la situación.

---

## Tabla de Decisión

| Situación | Estrategia | Por qué |
|-----------|------------|---------|
| Sabes exactamente qué archivos tocar | **Dar contexto explícito** | Ahorra tokens y tiempo de exploración |
| No sabes dónde está el código relevante | **Dejar que explore** | El agente es mejor que tú buscando en codebases grandes |
| Bug con stack trace claro | **Dar el stack trace + archivos** | El agente va directo al problema |
| Bug sin pista clara | **Pedir investigación primero** | "Investiga qué causa X, reporta antes de actuar" |
| Refactor de área que conoces bien | **Dar plan detallado** | Tu conocimiento del dominio es valioso |
| Refactor de código legacy desconocido | **Pedir Plan Mode** | Deja que el agente entienda antes de cambiar |
| Feature nueva en proyecto que conoces | **Dar restricciones y patrones** | Orientar hacia la arquitectura existente |
| Explorar codebase nuevo | **Preguntar sin restricciones** | No limites la investigación inicial |
| Proyecto con CLAUDE.md bien configurado | **Contexto mínimo en el prompt** | El agente ya tiene el contexto permanente |
| Proyecto sin CLAUDE.md | **Más contexto en cada prompt** | Compensa la falta de memoria persistente |

---

## La Regla del 80/20

> En el **80%** de los casos, dar 2-3 líneas de contexto es suficiente. Solo en el **20%** restante (bugs complejos, refactors grandes, migraciones) necesitas prompts largos o Plan Mode.

### Ejemplo del 80%: contexto mínimo suficiente

```text
El test test/auth.test.js:45 falla con TypeError: user.role is undefined.
Investiga y corrige.
```

Tres líneas. El agente tiene: qué falla (test específico), dónde (archivo y línea), y qué error (TypeError exacto). Puede ir directo al código, entender el contexto leyendo el archivo, y corregir.

### Ejemplo del 20%: contexto extenso necesario

```text
Necesito migrar el sistema de notificaciones de polling a WebSockets.

Contexto del sistema actual:
- El cliente hace polling cada 5 segundos a GET /api/notifications
- Las notificaciones se almacenan en PostgreSQL (tabla notifications)
- Hay 3 tipos: order_update, message, system_alert
- Cada tipo tiene diferente prioridad y lógica de agrupación

Archivos relevantes:
- src/api/routes/notifications.ts (endpoint actual)
- src/services/notification-service.ts (lógica de negocio)
- src/models/notification.ts (modelo Prisma)
- frontend/src/hooks/useNotifications.ts (hook de React)

Restricciones:
- Usar socket.io (ya está como dependencia)
- Mantener el endpoint REST como fallback
- No perder notificaciones durante la migración
- Respetar los mismos permisos: cada usuario solo ve sus notificaciones

Plan Mode: investiga primero y propón un plan paso a paso.
```

Esta tarea requiere contexto extenso porque involucra múltiples archivos, un cambio de arquitectura, y tiene restricciones no obvias.

---

## Contexto Explícito vs. Contexto Persistente

Hay dos formas de dar contexto al agente:

| Tipo | Mecanismo | Cuándo usarlo |
|------|-----------|---------------|
| **Contexto en el prompt** | Incluirlo en cada mensaje | Información específica de la tarea actual |
| **Contexto en CLAUDE.md** | Archivo permanente del proyecto | Arquitectura, convenciones, comandos frecuentes |

El ideal es tener un `CLAUDE.md` bien configurado con el contexto permanente del proyecto, y usar el prompt solo para la información específica de la tarea actual.

```text
# Con un buen CLAUDE.md, el prompt se simplifica:

# CLAUDE.md ya contiene:
# - Estructura del proyecto
# - Comandos de test (npm test)
# - Convenciones de código
# - Stack tecnológico

# Entonces el prompt solo necesita:
Añade paginación con cursor al endpoint GET /api/products.
Sigue el mismo patrón que GET /api/orders (ya tiene paginación).
```

> **Referencia**: para aprender a crear archivos CLAUDE.md efectivos, ver [M04 - Memoria con CLAUDE.md](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-04-memoria-claude-md/README.md) del Curso de Claude Code.

---

## Señales de que Estás Dando Demasiado Contexto

- Pegas archivos enteros en el prompt (el agente puede leerlos solo)
- Explicas cómo funciona JavaScript/Python/etc. (el agente lo sabe)
- Repites información que ya está en CLAUDE.md
- Tu prompt tiene más de 20 líneas para una tarea simple
- Incluyes contexto "por si acaso" que no es relevante para la tarea

## Señales de que Estás Dando Poco Contexto

- El agente pregunta cosas básicas del proyecto antes de empezar
- Los resultados usan convenciones diferentes a las de tu proyecto
- El agente explora archivos que tú le podrías haber indicado directamente
- Los primeros intentos fallan por falta de información sobre restricciones

---

## Resumen

| Concepto | Descripción |
|----------|-------------|
| Regla 80/20 | La mayoría de tareas solo necesitan 2-3 líneas de contexto |
| Dar más contexto | Cuando sabes dónde está el problema y hay restricciones no obvias |
| Dejar explorar | Cuando no sabes dónde está el código o necesitas un overview |
| CLAUDE.md | Reduce la necesidad de contexto repetitivo en cada prompt |

---

## Siguiente

Continúa con [06 - Iteración y Corrección de Rumbo](06-iteracion-correccion.md), donde aprenderás qué hacer cuando el resultado del agente no es exactamente lo que esperabas.
