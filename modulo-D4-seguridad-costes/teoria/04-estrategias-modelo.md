# Estrategias de Selección de Modelo

## Introducción

"¿Qué modelo debo usar?" es la pregunta más frecuente al empezar con IA agéntica. La respuesta correcta nunca es "el más potente" ni "el más barato". Es: **depende de la tarea**. Este capítulo te da un framework de decisión de 5 preguntas para elegir el modelo correcto cada vez, y te enseña a configurar fallbacks y effort levels para maximizar la relación calidad/coste.

---

## No Existe "El Mejor Modelo"

### La paradoja del modelo único

Usar siempre Opus (el más potente) es como enviar un camión de 18 ruedas a hacer todas las entregas, incluidas las del supermercado a una manzana. Funciona, pero es ineficiente. Usar siempre Haiku (el más barato) es como intentar mover una casa con una bicicleta. También funciona... a veces.

### La realidad del desarrollo diario

El 60-70% de las tareas de desarrollo son rutinarias: implementar un endpoint siguiendo un patrón existente, escribir tests, refactorizar nombres, formatear código. Para estas tareas, Sonnet con effort medium es suficiente. Solo el 10-15% requiere razonamiento profundo (Opus). Y un 20-25% son consultas rápidas donde Haiku sobra.

---

## Framework de Decisión: 5 Preguntas

Antes de enviar un prompt, hazte estas 5 preguntas:

| # | Pregunta | Si la respuesta es SÍ | Modelo recomendado |
|---|----------|----------------------|-------------------|
| 1 | ¿La tarea requiere razonamiento profundo? (arquitectura, algoritmos complejos, decisiones con trade-offs) | Necesitas máxima capacidad | **Opus** con effort high/max |
| 2 | ¿Es una tarea repetitiva o bien definida? (CRUD, tests, refactor de nombres) | No necesitas razonamiento profundo | **Sonnet** con effort medium |
| 3 | ¿Es exploración o búsqueda de información? (¿qué hace esta función?, ¿dónde está X?) | Necesitas velocidad y bajo coste | **Haiku** con effort low |
| 4 | ¿Es crítica para producción? (pagos, auth, datos sensibles) | Necesitas calidad + review humano | **Opus** + revisión manual |
| 5 | ¿Es un lote de 100+ tareas? (migración, batch processing) | Necesitas coste mínimo por tarea | **Haiku** o Sonnet low, nunca Opus |

### Diagrama de decisión

```text
¿Requiere razonamiento profundo?
├── SÍ → ¿Es crítico para producción?
│        ├── SÍ → Opus + effort max + review humano
│        └── NO → Opus + effort high
└── NO → ¿Es exploración/búsqueda?
         ├── SÍ → Haiku + effort low
         └── NO → ¿Es batch (100+ tareas)?
                  ├── SÍ → Haiku o Sonnet + effort low
                  └── NO → Sonnet + effort medium
```

---

## Effort Levels y su Impacto Real

Los effort levels controlan cuánto "piensa" el modelo antes de responder. No todos los modelos soportan todos los niveles.

| Effort | Comportamiento | Velocidad | Coste | Cuándo usarlo |
|--------|---------------|-----------|-------|---------------|
| **low** | Respuesta inmediata, mínimo razonamiento | Muy rápido | Mínimo | Preguntas factuales, ediciones simples, búsqueda |
| **medium** | Balance velocidad/calidad | Rápido | Moderado | Desarrollo habitual, tests, refactors simples |
| **high** (default) | Razonamiento profundo | Moderado | Alto | Bugs complejos, features con lógica de negocio |
| **max** (solo Opus) | Razonamiento extendido, extended thinking | Lento | Muy alto | Diseño de arquitectura, decisiones críticas |

### Cómo configurar effort levels

```bash
# En Claude Code, por sesión
claude --model sonnet --effort medium "Implementa el endpoint GET /api/users"

# En Claude Code, por defecto en settings
# ~/.claude/settings.json
```

```json
{
  "model": "sonnet",
  "effort": "medium"
}
```

### Ejemplo de diferencia real

La misma pregunta con diferentes effort levels:

```text
Prompt: "¿Debería usar Redis o PostgreSQL para las sesiones de usuario?"

effort low: "Redis, porque es más rápido para datos de sesión."
(5 segundos, ~200 tokens output)

effort medium: "Redis es mejor para sesiones por velocidad y TTL nativo. 
PostgreSQL si necesitas persistencia y ya lo tienes configurado."
(10 segundos, ~500 tokens output)

effort high: "Depende de tus requisitos. Análisis comparativo: [tabla con 
8 criterios: velocidad, persistencia, escalabilidad, complejidad operativa, 
TTL, clustering, coste, consistencia]. Recomendación para tu caso: ..."
(30 segundos, ~1500 tokens output)

effort max: "Análisis de arquitectura completo con diagramas, trade-offs 
a largo plazo, plan de migración, métricas de decisión, y recomendación 
fundamentada con referencias."
(90 segundos, ~4000 tokens output)
```

---

## Fallback Model

### Qué es

Un fallback model es un modelo de respaldo que se usa automáticamente cuando el modelo principal no está disponible (por sobrecarga, mantenimiento o rate limits).

### Configuración

```bash
# En Claude Code
claude --model opus --fallback-model sonnet "Diseña la arquitectura..."

# Si Opus no está disponible, usa Sonnet automáticamente
```

### Estrategia de fallback recomendada

| Modelo principal | Fallback | Cuándo usar el fallback |
|-----------------|----------|------------------------|
| Opus | Sonnet | Opus no disponible o rate limited |
| Sonnet | Haiku | Sonnet sobrecargado (raro pero posible) |
| Haiku | N/A | Haiku casi siempre está disponible |

### Cuándo NO usar fallback

- Tareas de seguridad crítica: si necesitas Opus, espera a que esté disponible
- Diseño de arquitectura: la diferencia de calidad entre Opus y Sonnet es significativa aquí
- Cuando la consistencia es clave: cambiar de modelo en mitad de una tarea puede producir resultados inconsistentes

---

## Combinando Estrategias: Ejemplos Prácticos

### Sprint típico de desarrollo

```text
Lunes - Planificación:
  ✓ Diseño de feature → Opus effort high ($3)
  ✓ Desglose en tareas → Sonnet effort medium ($0.50)

Martes a Jueves - Implementación:
  ✓ Explorar codebase → Haiku effort low ($0.10/consulta)
  ✓ Implementar endpoints → Sonnet effort medium ($1.50/endpoint)
  ✓ Escribir tests → Sonnet effort medium ($0.80/módulo)
  ✓ Debug complejo → Opus effort high ($2)

Viernes - Review y deploy:
  ✓ Security review → Opus effort high ($3)
  ✓ Fix de vulnerabilidades → Sonnet effort medium ($0.50)
  ✓ Documentación → Haiku effort low ($0.20)

Total sprint: ~$15-25 por desarrollador
```

### Batch processing (migración de 200 archivos)

```text
❌ MAL: Opus para cada archivo → $200+
✓ BIEN: Haiku para archivos simples, Sonnet para los complejos → $20-30
```

---

## Resumen

- Usa el framework de 5 preguntas antes de cada tarea para elegir el modelo correcto
- El 60-70% de las tareas de desarrollo solo necesitan Sonnet con effort medium
- Opus es para razonamiento profundo y decisiones críticas; no para tareas rutinarias
- Haiku es para exploración rápida y batch processing; no para lógica de negocio compleja
- Configura fallback models para evitar bloqueos cuando el modelo principal no está disponible
- Los effort levels son tan importantes como la elección del modelo: un Sonnet con effort high puede superar a un Opus con effort low en muchas tareas
