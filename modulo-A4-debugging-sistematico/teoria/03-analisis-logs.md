# Análisis de logs con IA

## Por qué los agentes son excelentes para logs

Los archivos de log son una de las fuentes de información más valiosas para debugging — y una de las más tediosas de analizar manualmente. Un log de producción puede tener miles de líneas con mezcla de niveles (INFO, WARN, ERROR), timestamps, IDs de request y mensajes crípticos.

Los agentes de código son especialmente buenos para esto porque pueden:

- **Buscar patrones** entre miles de líneas en segundos
- **Correlacionar timestamps** para reconstruir secuencias de eventos
- **Filtrar ruido** (separar la señal de los mensajes informativos)
- **Detectar anomalías** (gaps temporales, ráfagas de actividad, mensajes repetitivos)

## El patrón de análisis de logs

### Prompt template general

```text
Analiza este log de [nombre del sistema/servicio]. Busca:
1. El primer error o warning relevante
2. Qué pasó inmediatamente antes (posible causa)
3. Patrones repetitivos que sugieran un problema sistémico
4. Timestamps anómalos (gaps largos entre operaciones o ráfagas de actividad)

[pegar log o indicar ruta al archivo]
```

### Ejemplo con log de un servidor Express

```text
Analiza este log del servidor de API. El equipo reporta que 
"los pedidos tardan mucho" desde las 14:00 de hoy.

Busca:
1. Qué cambió a las 14:00
2. Si hay un patrón de degradación gradual o un evento puntual
3. Cuáles son los endpoints más afectados

Log:
[2024-03-15 13:58:01] INFO  POST /api/orders - 200 - 45ms
[2024-03-15 13:58:15] INFO  GET  /api/products - 200 - 12ms
[2024-03-15 13:59:42] INFO  POST /api/orders - 200 - 52ms
[2024-03-15 14:00:03] WARN  Database connection pool: 8/10 connections in use
[2024-03-15 14:00:15] INFO  POST /api/orders - 200 - 340ms
[2024-03-15 14:00:28] WARN  Database connection pool: 10/10 connections in use
[2024-03-15 14:00:45] INFO  POST /api/orders - 200 - 1250ms
[2024-03-15 14:01:02] ERROR Database connection timeout after 5000ms
[2024-03-15 14:01:03] INFO  POST /api/orders - 503 - 5012ms
```

Un buen agente identificará:

1. El primer warning a las 14:00:03 (pool de conexiones al 80%)
2. Escalada rápida: 80% → 100% → timeout en menos de 1 minuto
3. Posible causa: un query lento o una conexión que no se libera
4. Impacto: los endpoints de pedidos pasaron de 45ms a 5000ms+

## Estrategias para logs grandes

### Problema: logs de más de 1000 líneas

Pasar un log de 5000 líneas directamente al agente consume mucho contexto y puede provocar respuestas imprecisas. Usa estas estrategias:

### Estrategia 1: Resumen primero, detalles después

```text
Paso 1: Dame un resumen de alto nivel de este log:
- Rango temporal (primera y última línea)
- Distribución por nivel (cuántos INFO, WARN, ERROR)
- Los 3 errores más frecuentes
No analices cada línea todavía.

Paso 2 (después del resumen): Ahora céntrate en los errores 
de tipo [X] entre las 14:00 y las 14:30. Para cada uno,
muéstrame las 5 líneas anteriores.
```

### Estrategia 2: Filtrar antes de pasar al agente

```bash
# Filtrar solo errores y warnings
grep -E "ERROR|WARN" app.log > filtered.log

# Filtrar por rango temporal
awk '$0 >= "[2024-03-15 14:00" && $0 <= "[2024-03-15 15:00"' app.log > time_range.log

# Filtrar por request ID específico
grep "req-id-abc123" app.log > single_request.log
```

Después pasa el archivo filtrado al agente:

```text
Analiza este log filtrado (solo errores y warnings entre 14:00 y 15:00).
[pegar contenido filtrado]
```

### Estrategia 3: Subagentes para secciones en paralelo

Para logs muy grandes, divide el análisis en secciones y usa subagentes:

```text
Necesito analizar un log de 10,000 líneas. Divide el análisis:
- Subagente 1: analiza las primeras 2500 líneas, busca el inicio del problema
- Subagente 2: analiza las líneas 2500-5000, busca la escalada
- Subagente 3: analiza las líneas 5000-7500, busca el pico del incidente
- Subagente 4: analiza las últimas 2500 líneas, busca la recuperación

Para cada sección reporta: errores encontrados, patrones, timestamps clave.
```

## Patrones comunes en análisis de logs

| Patrón en el log | Posible problema | Qué preguntar al agente |
|-------------------|-----------------|-------------------------|
| Tiempos de respuesta crecientes gradualmente | Memory leak o pool exhaustion | "¿Hay un patrón de degradación progresiva?" |
| Ráfaga de errores seguida de silencio | Crash y restart del servicio | "¿Hay un gap después de los errores?" |
| El mismo error repetido cada N segundos | Retry loop sin backoff | "¿Hay un patrón repetitivo con intervalo fijo?" |
| Errores de conexión intermitentes | Red inestable o servicio flapping | "¿Los errores de conexión tienen un patrón temporal?" |
| Warnings que preceden a errores | Degradación antes del fallo | "¿Qué warnings aparecen antes de cada error?" |

## Prompt avanzado: correlación entre logs

Cuando tienes logs de múltiples servicios:

```text
Tengo logs de 3 servicios que participan en el flujo de crear un pedido:
- frontend.log: log del servidor Next.js
- api.log: log del servidor Express
- worker.log: log del worker de procesamiento

El usuario reporta que el pedido se crea pero no se procesa.
Correlaciona los logs por request-id para reconstruir el flujo completo.
Identifica en qué punto se pierde el pedido.
```

---

[← Anterior: Workflow de debugging](02-workflow-debugging.md) | [Siguiente: Debugging cross-stack →](04-debugging-cross-stack.md)
