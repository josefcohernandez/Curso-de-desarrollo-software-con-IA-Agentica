# Benchmarking de Productividad

## Introducción

"Desde que usamos IA somos más productivos" es una afirmación que todo el mundo hace y nadie mide. ¿Cuánto más productivos? ¿En qué tareas? ¿A qué coste? Sin datos, es una opinión. Con un benchmark bien diseñado, es un hecho demostrable que justifica la inversión (o revela que necesitas ajustar tu enfoque).

---

## Qué Medir a Nivel Personal

### 4 métricas individuales

| Métrica | Cómo medirla | Qué indica | Target saludable |
|---------|-------------|------------|------------------|
| **Tareas completadas por sesión** | Contar tareas terminadas en cada sesión de trabajo con IA | Productividad bruta | 3-5 tareas/sesión de 2h |
| **Ratio prompts/resultado** | Número de prompts necesarios para completar una tarea | Calidad de tus prompts y de la colaboración | 2-4 prompts/tarea |
| **Coste por ticket/feature** | Tokens gastados (en $) por cada ticket o feature completada | Eficiencia económica | $0.50-$3 por feature |
| **Tiempo en modo supervisión** | Horas dedicadas a revisar output de IA vs trabajo autónomo | Sostenibilidad (burnout) | < 50% de la jornada |

### Cómo registrar las métricas

Formato simple en un archivo diario:

```markdown
## 2026-04-14

### Sesión 1 (09:00-11:00)
- Tareas: auth refactor (4 prompts, $1.20), nuevo endpoint users (3 prompts, $0.80)
- Ratio: 3.5 prompts/tarea
- Modo predominante: Ping-pong TDD
- Supervisión: 60% del tiempo
- Satisfacción: 4/5

### Sesión 2 (14:00-16:00)
- Tareas: fix pagination bug (6 prompts, $1.50), tests e2e (2 prompts, $0.40)
- Ratio: 4 prompts/tarea
- Modo predominante: Investigación + Mob
- Supervisión: 70% del tiempo
- Satisfacción: 3/5
- Nota: Bug de pagination fue difícil, debí usar Plan Mode
```

---

## Qué Medir a Nivel de Equipo

### 4 métricas de equipo

| Métrica | Cómo medirla | Qué indica | Frecuencia |
|---------|-------------|------------|------------|
| **Cycle time por tipo de tarea** | Tiempo desde que se empieza una tarea hasta que está en producción | Velocidad del equipo (comparar con/sin IA) | Semanal |
| **Bug escape rate** | Bugs que llegan a producción / total de cambios deployados | Calidad del código generado con IA | Mensual |
| **Developer satisfaction survey** | Encuesta anónima: productividad percibida, burnout, frustración | Sostenibilidad humana | Mensual |
| **Coste total IA vs horas ahorradas** | Factura de IA / horas estimadas ahorradas × coste/hora | ROI económico real | Mensual |

### Cycle time: antes y después

```text
Ejemplo de medición:

Tipo de tarea: "Nuevo endpoint CRUD completo con tests"
  Antes de IA: 4 horas promedio (diseño + código + tests + review)
  Con IA (mes 1): 3 horas promedio (curva de aprendizaje)
  Con IA (mes 3): 1.5 horas promedio (equipo optimizado)
  
  Mejora: 62.5% de reducción en cycle time
  Coste IA: $2 promedio por endpoint
  Coste hora desarrollador: $50
  Ahorro neto: $50 × 2.5h - $2 = $123 por endpoint
```

### Bug escape rate: la métrica que importa

Si adoptas IA y el bug escape rate sube, estás intercambiando velocidad por calidad. Eso no es productividad, es deuda técnica acelerada.

```text
Interpretación:
  Bug escape rate estable o descendente → la IA está ayudando bien
  Bug escape rate ascendente → necesitas más review, mejores prompts, 
    o mutation testing (ver módulo D3)
```

### Developer satisfaction: no la ignores

```text
Encuesta mensual (anónima, 5 preguntas, 2 minutos):

1. ¿La IA te hace más productivo? (1-5)
2. ¿Sientes fatiga de supervisión? (1-5, donde 5 = mucha fatiga)
3. ¿Confías en el código que genera la IA? (1-5)
4. ¿Has sentido que tus habilidades técnicas se deterioran? (1-5)
5. ¿Recomendarías el uso actual de IA a otro equipo? (1-5)

Alertas:
  Pregunta 2 promedio > 3.5 → reducir horas de supervisión
  Pregunta 4 promedio > 3.0 → programar trabajo manual obligatorio
  Pregunta 5 promedio < 3.0 → algo fundamental no funciona
```

---

## Cómo Hacer el Benchmark: Proceso de 5 Pasos

### Paso 1: Medir baseline (2 semanas)

Mide las métricas con tu flujo actual (con o sin IA). Este es tu punto de referencia.

```text
Qué registrar durante el baseline:
- Cycle time de cada tipo de tarea
- Número de bugs reportados en producción
- Encuesta de satisfacción inicial
- Coste actual de IA (si ya la usas)
```

### Paso 2: Implementar mejoras (1 semana de adopción)

Aplica las técnicas del curso:
- Mejores prompts (módulo A1)
- Model selection por tarea (este módulo)
- Modos de pair programming (módulo D3)
- Token budgeting (este módulo)

### Paso 3: Medir resultado (2 semanas)

Repite exactamente las mismas mediciones que en el baseline.

### Paso 4: Calcular delta y ROI

```text
Fórmula de ROI:

  Horas ahorradas = (cycle_time_antes - cycle_time_después) × tareas_completadas
  Valor ahorrado = horas_ahorradas × coste_hora_desarrollador
  Coste IA = factura_mensual_herramientas_IA
  ROI = (valor_ahorrado - coste_IA) / coste_IA × 100

Ejemplo:
  Horas ahorradas: 40h/mes (equipo de 5)
  Valor ahorrado: 40 × $50 = $2,000
  Coste IA: $400/mes
  ROI = ($2,000 - $400) / $400 × 100 = 400%
```

### Paso 5: Iterar trimestralmente

El benchmark no es un evento único. Cada trimestre:

1. Revisa las métricas
2. Identifica áreas de mejora
3. Implementa ajustes
4. Mide de nuevo
5. Compara con el trimestre anterior

---

## Errores Comunes en Benchmarking

| Error | Por qué es malo | Alternativa |
|-------|-----------------|-------------|
| Medir solo velocidad | Ignoras calidad (bugs) y sostenibilidad (burnout) | Mide las 4 métricas de equipo |
| No tener baseline | No puedes demostrar mejora sin punto de referencia | Siempre mide antes de cambiar algo |
| Medir una sola semana | Varianza natural entre semanas distorsiona datos | Mínimo 2 semanas por medición |
| Ignorar la satisfacción | El burnout reduce productividad a medio plazo | Encuesta mensual obligatoria |
| Comparar desarrolladores entre sí | Genera competencia tóxica y gaming de métricas | Compara equipo consigo mismo en el tiempo |

---

## Resumen

- Sin datos, "somos más productivos con IA" es una opinión; con benchmark, es un hecho
- 4 métricas personales (tareas, ratio, coste, supervisión) + 4 de equipo (cycle time, bugs, satisfacción, ROI)
- El proceso de 5 pasos (baseline, mejoras, medición, delta, iteración) se repite trimestralmente
- El bug escape rate es la métrica de calidad más importante: si sube, la IA no está ayudando
- La satisfacción del desarrollador es predictiva: si baja, la productividad bajará después
- Nunca compares desarrolladores entre sí; compara al equipo consigo mismo en el tiempo
