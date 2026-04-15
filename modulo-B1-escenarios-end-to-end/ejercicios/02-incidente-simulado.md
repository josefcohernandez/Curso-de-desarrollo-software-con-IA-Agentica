# Ejercicio 2: Incidente Simulado

## Objetivo

Resolver un incidente de producción simulado en 30 minutos siguiendo el workflow de 5 pasos de la teoría 02. Debes encontrar la causa raíz, implementar el fix y escribir un post-mortem.

**Tiempo máximo: 30 minutos**

---

## Contexto del proyecto

**TaskFlow**: una API REST en Node.js/Express para gestión de tareas. Endpoints principales: `POST /api/tasks` (crear), `GET /api/tasks` (listar), `GET /api/tasks/due-today` (tareas del día), `PUT /api/tasks/:id`, `DELETE /api/tasks/:id`. Usa `dayjs` para fechas y PostgreSQL.

## El incidente

A las 07:15, el dashboard de "tareas para hoy" aparece vacío para todos los usuarios. El endpoint responde 200 pero con `{ "tasks": [], "count": 0 }`. Otros endpoints funcionan correctamente.

### Logs

```text
[2024-03-15 07:15:02] INFO  GET /api/tasks/due-today - 200 OK (12ms)
[2024-03-15 07:15:02] INFO  Response: { "tasks": [], "count": 0 }
[2024-03-15 07:16:12] INFO  GET /api/tasks?status=pending - 200 OK (15ms)
[2024-03-15 07:16:12] INFO  Response: { "tasks": [...], "count": 47 }
[2024-03-15 07:17:00] WARN  TaskService.getDueToday: comparing 2024-03-15T00:00:00.000Z with stored dates in format 2024-03-15T07:00:00+02:00
```

### Información adicional

- Ultimo deploy: ayer a las 18:30. Changelog: "chore: update dependencies (dayjs 1.11.10 -> 1.11.13)"
- Las tareas se almacenan con timezone Europe/Madrid (UTC+2)

### El bug

La actualización de `dayjs` cambió el comportamiento de `.startOf('day')`: antes preservaba la timezone original, ahora devuelve UTC. El código del servicio:

```javascript
// src/services/task-service.js (línea 42)
async getDueToday() {
  const today = dayjs().startOf('day');
  const tomorrow = dayjs().startOf('day').add(1, 'day');
  return db.query(
    'SELECT * FROM tasks WHERE due_date >= $1 AND due_date < $2',
    [today.toISOString(), tomorrow.toISOString()]
  );
}
```

Con la version antigua, `today` = `2024-03-15T00:00:00+02:00`. Con la nueva, `today` = `2024-03-15T00:00:00.000Z` (medianoche UTC = 02:00 en Madrid). La comparacion falla.

---

## Tu misión

1. **Triage (5 min)**: describe el incidente al agente con los logs. Pide que investigue commits recientes
2. **Diagnóstico (10 min)**: confirma que el cambio de `dayjs` causa el bug de timezone
3. **Fix (10 min)**: elige una opción e implementa:
   - Forzar timezone explícita en `.startOf('day')` (requiere plugin `dayjs/plugin/timezone`)
   - Rollback de `dayjs` a 1.11.10 (inmediato pero no resuelve el fondo)
   - Almacenar fechas en UTC (correcto a largo plazo, requiere migración)
4. **Validación (5 min)**: ejecuta tests que verifiquen que el endpoint devuelve tareas correctamente

---

## Entregables

1. **El fix**: código corregido con tests y commit descriptivo
2. **Post-mortem**: documento con resumen, timeline, causa raíz, impacto, resolución y acciones preventivas

---

## Criterios de Evaluación

| Criterio | Peso | Excelente | Insuficiente |
|----------|------|-----------|--------------|
| Velocidad de diagnóstico | 25% | Causa identificada en <10 min | No identifica la causa en 15 min |
| Calidad del fix | 30% | Fix preciso con tests, sin regresiones | Fix incorrecto o incompleto |
| Decisión fix vs. rollback | 15% | Justifica con criterios claros | No toma decisión o no justifica |
| Post-mortem | 20% | Completo con acciones preventivas | Incompleto o ausente |
| Gestión del tiempo | 10% | Completado en 30 min | No completado |

---

## Reflexión post-ejercicio

1. ¿La pista del `WARN` en los logs te llevó rápido a la causa?
2. ¿Qué opción de fix elegiste y por qué?
3. ¿Qué acción preventiva implementarías para detectar este tipo de bugs antes del deploy?
