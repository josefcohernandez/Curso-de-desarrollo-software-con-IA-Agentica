# Métricas de Productividad con IA

## Introducción

Medir el impacto de la IA en la productividad de un equipo es necesario pero delicado. Las métricas incorrectas no solo son inútiles — pueden generar incentivos perversos (optimizar para la métrica en lugar de para el resultado). Este capítulo te enseña qué medir, cómo medirlo, y qué trampas evitar.

---

## Las 6 Métricas que Sí Importan

| Métrica | Cómo medir | Útil para | Trampa a evitar |
|---------|------------|-----------|-----------------|
| **Cycle time** | Tiempo desde ticket asignado hasta PR mergeado | Productividad general | No atribuir toda la mejora a la IA |
| **PRs por semana** | Count de PRs mergeados por developer | Throughput del equipo | Más PRs no es mejor si son PRs triviales |
| **Tasa de bugs post-merge** | Bugs reportados en código de la última semana | Calidad del código | Diferenciar bugs en código IA vs código humano |
| **Coste de tokens** | Gasto mensual en API / suscripciones | ROI y presupuesto | No optimizar coste sacrificando productividad |
| **Tiempo de onboarding** | Días hasta primer PR significativo de un nuevo miembro | Eficiencia del equipo | Confundir velocidad con comprensión real |
| **Satisfacción del equipo** | Survey periódico (escala 1-10) | Adopción sostenible | No ignorar feedback negativo |

---

## Detalle de Cada Métrica

### 1. Cycle Time

Es la métrica más representativa de productividad. Mide el tiempo total desde que un developer empieza a trabajar en un ticket hasta que el PR se mergea.

```text
Cycle time = fecha_merge - fecha_inicio_trabajo
```

**Cómo comparar**: mide el cycle time promedio durante 4 semanas sin IA, luego 4 semanas con IA. Compara promedios.

**Cuidado**: el cycle time incluye tiempo de review, que no depende del developer. Para aislar el impacto de la IA, mide también el "coding time" (desde primer commit hasta PR abierto).

### 2. PRs por Semana

Mide el throughput — cuánto trabajo completo sale del equipo cada semana.

**Cuidado**: si los developers empiezan a dividir PRs artificialmente para inflar esta métrica, deja de ser útil. Complementa con "tamaño medio de PR" para detectar este patrón.

### 3. Tasa de Bugs Post-Merge

El miedo más común es que la IA introduzca más bugs. Esta métrica lo verifica con datos.

```text
Tasa = bugs_encontrados_en_semana / PRs_mergeados_en_semana
```

**Cuidado**: necesitas un período de medición antes de la adopción de IA para tener un baseline. Sin baseline, los datos no dicen nada.

### 4. Coste de Tokens

El gasto en herramientas de IA es un dato necesario para calcular el ROI.

**Qué incluir**:
- Suscripciones mensuales (ej: $20/mes por developer)
- Uso de API si aplica (tokens consumidos)
- Tiempo de administración de la herramienta

**Cuidado**: no optimizar el coste a costa de la productividad. Un developer que ahorra 5 horas/semana y gasta $100/mes está generando un retorno enorme.

### 5. Tiempo de Onboarding

¿Cuánto tarda un nuevo miembro del equipo en hacer su primer PR significativo? Con un buen CLAUDE.md y la IA como asistente de exploración, este tiempo puede reducirse drásticamente.

**Cuidado**: rapidez en el primer PR no significa comprensión profunda del codebase. Complementa con entrevistas 1-on-1 para verificar que el nuevo miembro entiende la arquitectura.

### 6. Satisfacción del Equipo

Un survey breve (3-5 preguntas, escala 1-10) cada 2-4 semanas:

```markdown
1. ¿Cómo de productivo te sientes esta semana? (1-10)
2. ¿La herramienta de IA te ayudó o te estorbó? (1-10)
3. ¿Confías en la calidad del código que generas con IA? (1-10)
4. ¿Hay algo que te frustra del proceso actual? (texto libre)
```

**Cuidado**: si el feedback negativo se ignora consistentemente, la gente deja de responder. Actúa sobre el feedback o explica por qué no se puede actuar.

---

## Las 3 Métricas Vanidosas (No Medir)

| Métrica vanidosa | Por qué es inútil | Efecto perverso |
|------------------|-------------------|-----------------|
| **Líneas de código generadas por IA** | Más líneas no es mejor. A veces la mejor solución tiene menos líneas | Incentiva código verbose y duplicado |
| **Porcentaje de "código IA"** | Imposible de medir con precisión. ¿Cuenta si el dev editó el 50%? ¿Y el 10%? | Crea una falsa dicotomía "código IA vs código humano" |
| **Tiempo usando la herramienta** | Micromanagement puro. No correlaciona con productividad | Incentiva tener la herramienta abierta sin usarla realmente |

---

## Dashboard Recomendado

Si quieres un dashboard visual para el equipo, estas son las métricas a incluir:

```text
┌─────────────────────────────────────────────────┐
│           Dashboard de Productividad IA         │
├───────────────┬─────────────────────────────────┤
│ Cycle time    │ 3.2 días (▼ 28% vs baseline)   │
│ PRs/semana    │ 12.5 (▲ 15% vs baseline)        │
│ Bugs post-merge│ 0.8/semana (= baseline)        │
│ Coste/mes     │ $500 (5 devs × $100)            │
│ Satisfacción  │ 7.8/10 (▲ desde 7.2)            │
│ Onboarding    │ 3 días (▼ desde 8 días)          │
└───────────────┴─────────────────────────────────┘
```

---

## Frecuencia de Medición

| Métrica | Frecuencia | Quién la revisa |
|---------|------------|-----------------|
| Cycle time | Semanal | Tech lead |
| PRs/semana | Semanal | Tech lead |
| Bugs post-merge | Semanal | Todo el equipo |
| Coste | Mensual | Engineering manager |
| Satisfacción | Quincenal | Todo el equipo |
| Onboarding | Por evento | Tech lead + HR |

---

## Navegación

| | |
|---|---|
| **Anterior** | [03-convenciones-equipo.md](03-convenciones-equipo.md) |
| **Siguiente** | [05-roi-management.md](05-roi-management.md) |
| **Módulo** | [README del módulo](../README.md) |
