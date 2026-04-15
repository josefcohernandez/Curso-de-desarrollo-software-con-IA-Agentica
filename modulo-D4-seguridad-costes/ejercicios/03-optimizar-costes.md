# Ejercicio 3: Optimizar Costes de IA - Análisis y Plan de Reducción

## Objetivo

Dado un historial de uso simulado de IA de una semana, calcular el coste real, identificar desperdicios y proponer un plan de optimización que reduzca el coste un 30% sin sacrificar productividad.

---

## Historial de Uso Simulado

Un equipo de 3 desarrolladores ha usado Claude Code durante una semana. Aquí está el registro de consumo:

### Desarrollador A (Senior)

| Día | Modelo | Tareas | Prompts | Input tokens | Output tokens | Coste estimado |
|-----|--------|--------|---------|-------------|--------------|----------------|
| Lunes | Opus | Diseño de arquitectura | 8 | 120,000 | 45,000 | $1.04 |
| Lunes | Opus | Implementar CRUD users | 12 | 180,000 | 60,000 | $1.44 |
| Martes | Opus | Fix bug pagination | 15 | 225,000 | 50,000 | $1.42 |
| Martes | Opus | Escribir tests | 10 | 150,000 | 70,000 | $1.50 |
| Miércoles | Opus | Refactor naming | 6 | 90,000 | 20,000 | $0.57 |
| Miércoles | Opus | Documentación API | 8 | 120,000 | 40,000 | $0.96 |
| Jueves | Opus | Security review | 5 | 75,000 | 30,000 | $0.68 |
| Jueves | Opus | Explorar codebase | 20 | 300,000 | 25,000 | $1.28 |
| Viernes | Opus | Deploy fix | 4 | 60,000 | 15,000 | $0.41 |

**Total Dev A: ~$9.30/semana (todo Opus)**

### Desarrollador B (Mid-level)

| Día | Modelo | Tareas | Prompts | Input tokens | Output tokens | Coste estimado |
|-----|--------|--------|---------|-------------|--------------|----------------|
| Lunes | Sonnet | Feature login social | 25 | 200,000 | 100,000 | $2.10 |
| Martes | Sonnet | Feature login social (cont.) | 30 | 240,000 | 120,000 | $2.52 |
| Miércoles | Sonnet | Tests login social | 15 | 120,000 | 60,000 | $1.26 |
| Jueves | Sonnet | Fix bugs varios | 20 | 160,000 | 40,000 | $1.08 |
| Viernes | Sonnet | Refactor + docs | 10 | 80,000 | 30,000 | $0.69 |

**Total Dev B: ~$7.65/semana (todo Sonnet)**

### Desarrollador C (Junior)

| Día | Modelo | Tareas | Prompts | Input tokens | Output tokens | Coste estimado |
|-----|--------|--------|---------|-------------|--------------|----------------|
| Lunes | Sonnet | Aprender el proyecto | 40 | 320,000 | 80,000 | $2.16 |
| Martes | Sonnet | CRUD básico products | 35 | 280,000 | 140,000 | $2.94 |
| Miércoles | Sonnet | CRUD básico orders | 30 | 240,000 | 120,000 | $2.52 |
| Jueves | Sonnet | Fix bugs en su código | 25 | 200,000 | 50,000 | $1.35 |
| Viernes | Sonnet | Más exploración | 30 | 240,000 | 60,000 | $1.62 |

**Total Dev C: ~$10.59/semana (todo Sonnet, muchos prompts)**

### Resumen del equipo

| Desarrollador | Coste semanal | Prompts/semana | Modelo | Tareas completadas |
|--------------|--------------|----------------|--------|-------------------|
| Dev A (Senior) | $9.30 | 88 | Opus | 9 |
| Dev B (Mid) | $7.65 | 100 | Sonnet | 5 |
| Dev C (Junior) | $10.59 | 160 | Sonnet | 5 |
| **Total** | **$27.54** | **348** | | **19** |

**Coste mensual proyectado: ~$110/mes**

---

## Parte 1: Análisis de Desperdicios (10 min)

Analiza el historial e identifica ineficiencias. Usa el agente si lo necesitas:

```text
Analiza este historial de uso de IA y encuentra los desperdicios:
[pegar la tabla de arriba]

Busca:
1. Tareas donde se usó un modelo más potente de lo necesario
2. Desarrolladores con ratio prompts/tarea anormalmente alto
3. Sesiones donde no se usó /compact o /clear (input tokens altos)
4. Patrones de uso ineficiente
```

### Preguntas guía

1. Dev A usa Opus para todo. ¿Qué tareas podrían haberse hecho con Sonnet o Haiku?
2. Dev B necesitó 55 prompts para una sola feature (login social, lunes+martes). ¿Es normal?
3. Dev C tiene 160 prompts en la semana. ¿Cuál es su ratio prompts/tarea? ¿Qué indica?
4. ¿Alguien parece no usar `/clear` entre tareas? ¿Cómo lo sabes?

---

## Parte 2: Calcular el Coste Optimizado (10 min)

Propón un plan de optimización y calcula el nuevo coste:

### Tabla de reasignación de modelos

Completa esta tabla con el modelo que debería haberse usado:

| Tarea original | Modelo usado | Modelo óptimo | Ahorro |
|---------------|-------------|---------------|--------|
| Diseño arquitectura (Dev A) | Opus | Opus | $0 (correcto) |
| CRUD users (Dev A) | Opus | Sonnet | ? |
| Fix bug pagination (Dev A) | Opus | ? | ? |
| Explorar codebase (Dev A) | Opus | ? | ? |
| Aprender proyecto (Dev C) | Sonnet | ? | ? |

### Otras optimizaciones

| Optimización | Desarrollador | Ahorro estimado |
|-------------|--------------|-----------------|
| /clear entre tareas | | |
| /compact proactivo | | |
| Mejores prompts (menos iteraciones) | | |
| Batching de tareas | | |

---

## Parte 3: Plan de Reducción del 30% (10 min)

Escribe un plan concreto que reduzca el coste semanal de $27.54 a menos de $19.28 (30% menos):

```text
Dado el análisis anterior, diseña un plan de optimización para este equipo.
El plan debe incluir:
1. Política de selección de modelo por tipo de tarea
2. Reglas de /compact y /clear
3. Coaching específico para cada desarrollador
4. Budget máximo por persona/semana
5. Coste semanal objetivo tras la optimización

Restricción: la productividad (19 tareas/semana) no puede bajar.
```

---

## Entregable

1. **Análisis de desperdicios**: lista de al menos 5 ineficiencias identificadas
2. **Tabla de reasignación**: modelo óptimo para cada tarea con ahorro calculado
3. **Plan de optimización**: política de modelo, reglas de higiene, coaching por persona
4. **Coste proyectado**: demostrar que el plan logra la reducción del 30%

---

## Criterios de Evaluación

- Se identifican al menos 5 desperdicios concretos (no genéricos)
- Los cálculos de ahorro son correctos (basados en precios reales de los modelos)
- El plan es accionable (no "usar menos IA", sino acciones específicas por persona)
- Se mantiene la productividad (19 tareas/semana) con el coste reducido
- Se incluye coaching específico para Dev C (ratio 32 prompts/tarea es excesivo)
