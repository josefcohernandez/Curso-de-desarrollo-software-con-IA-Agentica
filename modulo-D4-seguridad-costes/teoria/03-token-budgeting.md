# Token Budgeting y Gestión de Costes

## Introducción

Cada prompt que envías a un agente de IA cuesta dinero. No mucho por prompt individual, pero multiplicado por un equipo de 10 personas durante meses, la factura puede sorprender. La diferencia entre un equipo que gasta $500/mes y otro que gasta $5,000/mes para obtener resultados similares no es la cantidad de uso -- es la **estrategia**. Token budgeting es gestionar conscientemente ese gasto.

---

## Entender el Coste Real

### Tipos de tokens

| Tipo | Qué incluye | Coste relativo | Ejemplo |
|------|-------------|----------------|---------|
| **Input tokens** | Tu prompt, archivos leídos, contexto de herramientas, system prompt | Base (1x) | El contenido de CLAUDE.md + tu pregunta + los archivos que el agente lee |
| **Output tokens** | Respuesta del agente, código generado, pensamiento interno (extended thinking) | 3-5x más caro que input | El código que el agente escribe + su razonamiento |
| **Cached tokens** | Contenido que se repite entre llamadas (CLAUDE.md, system prompt, archivos ya leídos) | ~90% descuento vs input | Tu CLAUDE.md se envía en cada mensaje pero solo pagas ~10% después del primero |

### Cálculo de ejemplo

```text
Sesión típica de desarrollo (1 hora, modelo Sonnet):

  Mensajes enviados: 15
  Input tokens promedio por mensaje: 8,000 (contexto + prompt)
  Output tokens promedio por mensaje: 2,000 (respuesta)
  Cached tokens promedio: 5,000 (CLAUDE.md + archivos repetidos)

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
| 2 | **Subagentes con Haiku** | 40-60% | Configurar investigación y exploración con modelo económico |
| 3 | **`/compact` proactivo** | 20-30% | Compactar el contexto antes de que se llene, no después |
| 4 | **`/clear` entre tareas** | 30-50% | Limpiar contexto entre tareas no relacionadas; evitar pagar por contexto irrelevante |
| 5 | **`--max-budget-usd`** | Techo fijo | Prevenir ejecuciones desbocadas con un límite por sesión |
| 6 | **CLAUDE.md eficiente** | 10-20% | Instrucciones concisas y relevantes = menos tokens por llamada |

### Estrategia 1: Elegir modelo por tarea

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

### Estrategia 3: `/compact` proactivo

```text
Regla práctica:
- Después de 10-15 mensajes en la misma conversación → /compact
- Antes de cambiar de tema/tarea → /compact
- Cuando notas que el agente "olvida" cosas → ya es tarde, debiste compactar antes
```

### Estrategia 4: `/clear` entre tareas

```text
Flujo óptimo:
1. Tarea A: implementar feature → 8 mensajes → completada
2. /clear (contexto limpio)
3. Tarea B: fix bug en otro módulo → 5 mensajes → completada

Flujo ineficiente:
1. Tarea A: 8 mensajes
2. Tarea B sin /clear: los 8 mensajes anteriores siguen en contexto, 
   pagando tokens de input por contexto irrelevante
```

### Estrategia 5: Presupuesto máximo

```bash
# Limitar una sesión a $2 máximo
claude --max-budget-usd 2 "Refactoriza el módulo de pagos"

# Si el agente alcanza el límite, se detiene y te informa
```

### Estrategia 6: CLAUDE.md eficiente

```markdown
# MAL: CLAUDE.md verboso (800 tokens)
Este proyecto es una aplicación web construida con React en el frontend 
y Node.js con Express en el backend. Usamos PostgreSQL como base de datos 
principal y Redis para caché. El proyecto fue iniciado en 2023 por el 
equipo de desarrollo y desde entonces hemos ido añadiendo funcionalidades...

# BIEN: CLAUDE.md conciso (200 tokens)
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
# Ver consumo de la sesión actual en Claude Code
# El indicador de tokens aparece en la barra de estado

# Consultar historial de uso (si tu proveedor lo ofrece)
# Anthropic Console: console.anthropic.com → Usage
```

### Alertas de gasto

Configura alertas en la consola de tu proveedor:
- Alerta al 50% del budget mensual
- Alerta al 80% del budget mensual
- Hard limit al 100%

---

## Resumen

- Los output tokens cuestan 3-5x más que los input tokens; optimiza la verbosidad de las respuestas
- Los cached tokens son tu aliado: un CLAUDE.md bien escrito se cachea y cuesta ~10% después del primer mensaje
- Las 6 estrategias combinadas pueden reducir costes un 50-70% sin perder productividad
- Asigna budget por tipo de tarea antes de empezar; si lo excedes, reformula
- Monitorea el gasto a nivel de equipo: un desarrollador que gasta 10x más que los demás necesita coaching, no más budget
