# Ejercicio 3: Análisis de logs

## Contexto

Eres el desarrollador de guardia. A las 03:15 de la madrugada recibes una alerta: "Tasa de errores HTTP 500 > 5% en los últimos 5 minutos". Cuando miras el dashboard, el servicio ya se ha recuperado. Tu trabajo: analizar los logs para entender qué pasó, por qué y cómo prevenirlo.

## El log del servidor

A continuación tienes un extracto del log del servidor API (Express + PostgreSQL) entre las 03:00 y las 03:30. El log completo tiene 500+ líneas; este es un extracto representativo con las líneas más relevantes.

```text
[2024-06-20 03:00:01] INFO  GET  /api/products - 200 - 23ms - req:r1001
[2024-06-20 03:00:05] INFO  POST /api/orders - 200 - 89ms - req:r1002
[2024-06-20 03:00:12] INFO  GET  /api/users/me - 200 - 15ms - req:r1003
[2024-06-20 03:00:30] INFO  GET  /api/products - 200 - 18ms - req:r1004
[2024-06-20 03:00:45] INFO  POST /api/orders - 200 - 95ms - req:r1005
[2024-06-20 03:01:00] INFO  CRON job started: sync-inventory
[2024-06-20 03:01:01] INFO  sync-inventory: fetching data from external API
[2024-06-20 03:01:03] INFO  sync-inventory: received 2,847 product updates
[2024-06-20 03:01:03] INFO  sync-inventory: starting batch update (batch_size=500)
[2024-06-20 03:01:04] INFO  GET  /api/products - 200 - 45ms - req:r1006
[2024-06-20 03:01:05] INFO  sync-inventory: batch 1/6 complete (500 products)
[2024-06-20 03:01:08] INFO  sync-inventory: batch 2/6 complete (500 products)
[2024-06-20 03:01:10] INFO  POST /api/orders - 200 - 156ms - req:r1007
[2024-06-20 03:01:11] INFO  sync-inventory: batch 3/6 complete (500 products)
[2024-06-20 03:01:12] WARN  Database connection pool: 8/10 connections in use
[2024-06-20 03:01:14] INFO  sync-inventory: batch 4/6 complete (500 products)
[2024-06-20 03:01:15] WARN  Database connection pool: 9/10 connections in use
[2024-06-20 03:01:16] INFO  GET  /api/products - 200 - 320ms - req:r1008
[2024-06-20 03:01:17] WARN  Database connection pool: 10/10 connections in use
[2024-06-20 03:01:17] WARN  Request queued waiting for DB connection - req:r1009
[2024-06-20 03:01:18] INFO  sync-inventory: batch 5/6 complete (500 products)
[2024-06-20 03:01:19] WARN  Request queued waiting for DB connection - req:r1010
[2024-06-20 03:01:20] WARN  Request queued waiting for DB connection - req:r1011
[2024-06-20 03:01:21] INFO  sync-inventory: batch 6/6 complete (347 products)
[2024-06-20 03:01:21] INFO  sync-inventory: all batches complete, running verification queries
[2024-06-20 03:01:22] WARN  Database connection pool: 10/10 connections in use
[2024-06-20 03:01:25] ERROR Request timeout waiting for DB connection (5000ms) - req:r1012 - GET /api/users/me
[2024-06-20 03:01:25] ERROR Request timeout waiting for DB connection (5000ms) - req:r1013 - POST /api/orders
[2024-06-20 03:01:26] INFO  GET  /api/users/me - 500 - 5002ms - req:r1012
[2024-06-20 03:01:26] INFO  POST /api/orders - 500 - 5001ms - req:r1013
[2024-06-20 03:01:27] ERROR Request timeout waiting for DB connection (5000ms) - req:r1014 - GET /api/products
[2024-06-20 03:01:27] INFO  GET  /api/products - 500 - 5003ms - req:r1014
[2024-06-20 03:01:28] WARN  sync-inventory: verification query 1/3 complete
[2024-06-20 03:01:30] ERROR Request timeout waiting for DB connection (5000ms) - req:r1015 - POST /api/orders
[2024-06-20 03:01:30] ERROR Request timeout waiting for DB connection (5000ms) - req:r1016 - GET /api/products
[2024-06-20 03:01:32] WARN  sync-inventory: verification query 2/3 complete
[2024-06-20 03:01:35] ERROR Request timeout waiting for DB connection (5000ms) - req:r1017 - GET /api/users/me
[2024-06-20 03:01:36] WARN  sync-inventory: verification query 3/3 complete
[2024-06-20 03:01:36] INFO  sync-inventory: verification complete, releasing connections
[2024-06-20 03:01:37] INFO  Database connection pool: 4/10 connections in use
[2024-06-20 03:01:38] INFO  GET  /api/products - 200 - 28ms - req:r1018
[2024-06-20 03:01:39] INFO  POST /api/orders - 200 - 92ms - req:r1019
[2024-06-20 03:01:40] INFO  GET  /api/users/me - 200 - 14ms - req:r1020
[2024-06-20 03:01:45] INFO  Database connection pool: 2/10 connections in use
[2024-06-20 03:02:00] INFO  All systems normal
```

## Tu tarea

### Parte 1: Análisis del log

Responde a estas preguntas:

1. **Primer indicio**: ¿cuál es la primera línea que indica un problema? ¿A qué hora?
2. **Causa raíz**: ¿qué proceso causó el agotamiento del pool de conexiones?
3. **Timeline del incidente**: ¿cuándo empezó, cuándo fue el pico y cuándo se recuperó?
4. **Impacto**: ¿cuántos requests fallaron? ¿Qué endpoints se vieron afectados?
5. **Patrón**: ¿por qué el batch update no causaba problemas hasta el batch 4/6?

### Parte 2: Diagnóstico

6. ¿Por qué el cron job consume tantas conexiones?
7. ¿Qué son las "verification queries" y por qué empeoran el problema?
8. ¿Por qué el sistema se recuperó solo a las 03:01:37?

### Parte 3: Propuesta de fix

9. Propón al menos 3 acciones para que esto no vuelva a ocurrir (ordénalas por prioridad)
10. Escribe la configuración o código que implementaría la acción de mayor prioridad

## Criterios de evaluación

| Nivel | Requisito |
|-------|-----------|
| **Aprobado** | Identificar la causa raíz y el timeline del incidente |
| **Notable** | Todo lo anterior + proponer 3 acciones preventivas justificadas |
| **Excelente** | Todo lo anterior + escribir código/configuración del fix principal y proponer un test de monitorización |

---

[← Ejercicio anterior](02-bug-sin-stack-trace.md) | [Siguiente ejercicio: Debugging cross-stack →](04-debugging-cross-stack.md)
