# Token Budgeting y Gestión de Costes

## Introducción

Cada prompt que envías a un agente de IA cuesta dinero. No mucho por prompt individual, pero multiplicado por un equipo de 10 personas durante meses, la factura puede sorprender. La diferencia entre un equipo que gasta $500/mes y otro que gasta $5,000/mes para obtener resultados similares no es la cantidad de uso: es la **estrategia**. Token budgeting es gestionar conscientemente ese gasto.

---

## Entender el Coste Real

### Tipos de tokens

| Tipo | Qué incluye | Coste relativo | Ejemplo |
|------|-------------|----------------|---------|
| **Input tokens** | Tu prompt, archivos leídos, contexto de herramientas, system prompt | Base (1x) | El contenido de `AGENTS.md` / `CLAUDE.md` + tu pregunta + los archivos que el agente lee |
| **Output tokens** | Respuesta del agente, código generado, pensamiento interno (extended thinking) | 3-5x más caro que input | El código que el agente escribe + su razonamiento |
| **Cached tokens** | Contenido que se repite entre llamadas (`AGENTS.md`, `CLAUDE.md`, system prompt, archivos ya leídos) | ~90% descuento vs input | El archivo de instrucciones persistentes se envía en cada mensaje pero solo pagas una fracción después del primero |

### Cálculo de ejemplo

```text
Sesión típica de desarrollo (1 hora, modelo Sonnet):

  Mensajes enviados: 15
  Input tokens promedio por mensaje: 8,000 (contexto + prompt)
  Output tokens promedio por mensaje: 2,000 (respuesta)
  Cached tokens promedio: 5,000 (archivo de instrucciones + archivos repetidos)

  Coste input: 15 × 3,000 × $3/1M = $0.135 (solo tokens no cacheados)
  Coste cached: 15 × 5,000 × $0.30/1M = $0.0225
  Coste output: 15 × 2,000 × $15/1M = $0.45

  Total sesión: ~$0.60
  Total día (6 sesiones): ~$3.60
  Total mes (22 días): ~$79.20
  Total equipo (5 personas): ~$396/mes
```

Los precios varían por modelo y proveedor. Consulta siempre la página de pricing actualizada.

---

## 6 Estrategias de Optimización

| # | Estrategia | Ahorro estimado | Cómo implementarla |
|---|-----------|-----------------|---------------------|
| 1 | **Elegir modelo por tarea** | 50-80% | Haiku para exploración, Sonnet para desarrollo, Opus para arquitectura |
| 2 | **Subagentes con modelo económico** | 40-60% | Configurar investigación y exploración con modelos económicos (`mini`, `haiku`, etc.) |
| 3 | **Compactación proactiva del contexto** | 20-30% | Compactar o resumir el contexto antes de que se llene, no después |
| 4 | **Reset de contexto entre tareas** | 30-50% | Limpiar contexto entre tareas no relacionadas; evitar pagar por contexto irrelevante |
| 5 | **Límite de presupuesto por sesión** | Techo fijo | Prevenir ejecuciones desbocadas con un límite por sesión |
| 6 | **Archivo de instrucciones eficiente** | 10-20% | Instrucciones concisas y relevantes = menos tokens por llamada |

### Estrategia 1: Elegir modelo por tarea

> **Nota**: los comandos de ejemplo usan Claude Code porque el resto del módulo también lo usa como referencia. En Codex u otros agentes CLI el principio es el mismo, aunque cambien los flags concretos.

No uses Opus para todo. Cada modelo tiene su punto óptimo:

```bash
# Exploración rápida: ¿qué hace este módulo?
claude --model haiku "Explica qué hace src/payments/ en 3 frases"

# Desarrollo habitual: implementar un endpoint
claude --model sonnet "Implementa el endpoint GET /api/users/:id"

# Diseño de arquitectura: decisiones críticas
claude --model opus "Diseña la arquitectura de microservicios para el sistema de pagos"
```

### Estrategia 2: Subagentes económicos

Si tu flujo usa subagentes (agentes que el agente principal delega tareas), configúralos con modelos económicos:

```json
{
  "subagent_model": "haiku",
  "subagent_tasks": ["search", "explore", "read", "lint"],
  "main_model": "sonnet"
}
```

### Estrategia 3: compactación proactiva del contexto

```text
Regla práctica:
- Después de 10-15 mensajes en la misma conversación → compactar o resumir
- Antes de cambiar de tema/tarea → compactar o resumir
- Cuando notas que el agente "olvida" cosas → ya es tarde, debiste compactar antes
```

### Estrategia 4: reset de contexto entre tareas

```text
Flujo óptimo:
1. Tarea A: implementar feature → 8 mensajes → completada
2. Nueva sesión o comando de reset de contexto
3. Tarea B: fix bug en otro módulo → 5 mensajes → completada

Flujo ineficiente:
1. Tarea A: 8 mensajes
2. Tarea B sin reset de contexto: los 8 mensajes anteriores siguen en contexto, 
   pagando tokens de input por contexto irrelevante
```

### Estrategia 5: presupuesto máximo

```bash
# Limitar una sesión a $2 máximo
claude --max-budget-usd 2 "Refactoriza el módulo de pagos"

# Si el agente alcanza el límite, se detiene y te informa
```

### Estrategia 6: archivo de instrucciones eficiente

```markdown
# MAL: archivo de instrucciones verboso (800 tokens)
Este proyecto es una aplicación web construida con React en el frontend 
y Node.js con Express en el backend. Usamos PostgreSQL como base de datos 
principal y Redis para caché. El proyecto fue iniciado en 2023 por el 
equipo de desarrollo y desde entonces hemos ido añadiendo funcionalidades...

# BIEN: archivo de instrucciones conciso (200 tokens)
Stack: React + Express + PostgreSQL + Redis
Tests: Jest (unit), Playwright (e2e)
Convenciones: ESLint Airbnb, commits convencionales
Archivos clave: src/api/ (endpoints), src/models/ (DB), src/ui/ (React)
```

---

## Presupuesto por Tipo de Tarea

| Tipo de tarea | Modelo recomendado | Budget sugerido | Ejemplo |
|---------------|-------------------|-----------------|---------|
| Exploración rápida | Haiku | < $0.05 | "¿Qué hace esta función?" |
| Bug fix simple | Sonnet | $0.05 - $0.50 | Fix de un error de validación |
| Feature completa | Sonnet | $0.50 - $2.00 | Nuevo endpoint con tests |
| Refactor de módulo | Sonnet | $1.00 - $3.00 | Reorganizar módulo de pagos |
| Diseño de arquitectura | Opus | $2.00 - $10.00 | Diseño de sistema de microservicios |
| Batch procesamiento | Haiku/Sonnet | $0.10 - $0.30/archivo | Migrar 50 archivos de formato |

### Cómo usar esta tabla

Antes de empezar una tarea, decide:
1. ¿Qué tipo de tarea es?
2. ¿Qué modelo uso?
3. ¿Cuál es el budget máximo razonable?

Si alcanzas el budget sin completar la tarea, es señal de que necesitas reformular tu enfoque (mejor prompt, descomponer la tarea, o hacerla manualmente).

---

## Monitoreo de Costes

### Herramientas de seguimiento

```bash
# Ver consumo de la sesión actual en tu host o CLI, si lo expone
# En Claude Code, el indicador de tokens aparece en la barra de estado

# Consultar historial de uso (si tu proveedor lo ofrece)
# Anthropic Console: console.anthropic.com → Usage
```

### Alertas de gasto

Configura alertas en la consola de tu proveedor:
- Alerta al 50% del budget mensual
- Alerta al 80% del budget mensual
- Hard limit al 100%

---

## Prompt Caching Avanzado

Más allá del caching automático, existen mecanismos explícitos para maximizar el ahorro.

### Cache Control Explícito (API)

Cuando construyes aplicaciones con la API de Anthropic, puedes controlar qué partes del contexto se cachean explícitamente. El principio general existe también en otros proveedores, aunque la sintaxis concreta cambie.

```python
from anthropic import Anthropic

client = Anthropic()

response = client.messages.create(
    model="claude-sonnet-4-20250514",
    max_tokens=1024,
    system=[
        {
            "type": "text",
            "text": "Eres un asistente de desarrollo. Convenciones del proyecto: ...",
            "cache_control": {"type": "ephemeral"}  # Marca para cachear
        }
    ],
    messages=[{"role": "user", "content": "Implementa el endpoint GET /api/users"}]
)

# En la respuesta, verifica los cache hits:
# response.usage.cache_creation_input_tokens  → tokens cacheados por primera vez
# response.usage.cache_read_input_tokens      → tokens leídos de cache (90% descuento)
```

**Cómo funciona**: el campo `cache_control` con `type: "ephemeral"` marca un bloque como cacheable. En la primera llamada se paga el coste completo + un 25% extra de escritura. En las siguientes (dentro del TTL de 5 minutos), se paga solo el 10%.

**Reglas del cache**:
- El cache funciona por **prefijo**: el contenido cacheado debe estar al principio del contexto y ser idéntico byte a byte
- TTL de 5 minutos: si no hay otra llamada en 5 minutos, el cache expira
- Mínimo: 1,024 tokens para Sonnet/Opus, 2,048 para Haiku
- Puedes poner hasta 4 breakpoints de cache en un prompt

### Message Batches API

Para tareas que no requieren respuesta inmediata, la Batches API de Anthropic ofrece un **50% de descuento** sobre los precios estándar:

```python
# Crear un batch de tareas
batch = client.messages.batches.create(
    requests=[
        {
            "custom_id": "review-file-1",
            "params": {
                "model": "claude-sonnet-4-20250514",
                "max_tokens": 2048,
                "messages": [{"role": "user", "content": f"Revisa este código: {code_1}"}]
            }
        },
        {
            "custom_id": "review-file-2",
            "params": {
                "model": "claude-sonnet-4-20250514",
                "max_tokens": 2048,
                "messages": [{"role": "user", "content": f"Revisa este código: {code_2}"}]
            }
        }
        # ... hasta 100,000 requests por batch
    ]
)

# El batch se procesa en hasta 24 horas
# Consultar estado:
status = client.messages.batches.retrieve(batch.id)
```

**Cuándo usar Batches API**:

| Caso de uso | ¿Batches API? | Por qué |
|-------------|---------------|---------|
| Code review de 50 PRs acumulados | Si | No necesitas respuesta inmediata |
| Migración masiva de ficheros | Si | Fan-out donde cada tarea es independiente |
| Generación de documentación del proyecto completo | Si | Batch de tareas de documentación |
| Debugging interactivo | No | Necesitas respuesta inmediata para iterar |
| Desarrollo en tiempo real | No | La latencia de hasta 24h es inaceptable |
| Evaluaciones de agentes (evals) | Si | Batch de evaluaciones con LLM-as-judge |

### Estrategia Combinada de Caching

La combinación óptima para minimizar costes en producción:

```text
1. Contexto estable (CLAUDE.md, system prompt) → cache_control ephemeral
   - Se paga 1x completo + 25% escritura la primera vez
   - Se paga 10% en las llamadas siguientes (dentro de 5 min)

2. Tareas batch sin urgencia → Message Batches API
   - 50% descuento sobre precio estándar
   - Compatible con prompt caching (se acumulan los descuentos)

3. Tareas interactivas → modelo por tarea (estrategia 1)
   - Haiku para exploración, Sonnet para desarrollo, Opus para arquitectura

4. Prefijo estable → KV-cache hit rate alto
   - Mantén la información que cambia al FINAL del contexto
   - La información estable va al INICIO (se cachea el prefijo)
```

**Ahorro estimado con la estrategia combinada**:

| Componente | Ahorro | Mecanismo |
|-----------|--------|-----------|
| Prompt caching explícito | 70-90% en input repetido | cache_control + prefijo estable |
| Batches API | 50% | Procesamiento asíncrono |
| Modelo por tarea | 50-80% | Haiku vs Opus según necesidad |
| Combinado (mejor caso) | 85-95% | Todo junto para tareas batch con contexto estable |

---

## Resumen

- Los output tokens cuestan 3-5x más que los input tokens; optimiza la verbosidad de las respuestas
- Los cached tokens son tu aliado: un CLAUDE.md bien escrito se cachea y cuesta ~10% después del primer mensaje
- El `cache_control` explícito permite controlar qué se cachea en la API; el KV-cache prefijo premia mantener el inicio del contexto estable
- La Message Batches API ofrece 50% de descuento para tareas asíncronas — ideal para code review masivo, migraciones y evals
- Las 6 estrategias base + caching avanzado + batches pueden reducir costes un 85-95% en el mejor caso
- Asigna budget por tipo de tarea antes de empezar; si lo excedes, reformula
- Monitorea el gasto a nivel de equipo: un desarrollador que gasta 10x más que los demás necesita coaching, no más budget
