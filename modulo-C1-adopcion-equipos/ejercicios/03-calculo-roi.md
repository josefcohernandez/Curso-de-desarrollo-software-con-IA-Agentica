# Ejercicio: Cálculo de ROI

## Objetivo

Calcular el ROI de adoptar IA agéntica para un escenario concreto y preparar los datos para una presentación a management.

**Tiempo estimado: 10 minutos**

---

## Escenario

Tu equipo tiene estas características:

| Dato | Valor |
|------|-------|
| Developers en el equipo | 5 |
| Salario medio anual (bruto + coste empresa) | $90,000 |
| Horas de trabajo efectivas por semana | 35 |
| Coste de la herramienta de IA | $50/mes por developer |
| Coste estimado de formación | $1,500 (taller de 1 día para todo el equipo) |
| Semanas de curva de aprendizaje | 2 (productividad reducida un 20%) |

Tras un piloto de 4 semanas con 2 developers, los datos observados son:

| Tarea | Tiempo sin IA | Tiempo con IA | Frecuencia semanal |
|-------|---------------|---------------|-------------------|
| Escribir tests unitarios | 3h | 1.2h | 4 veces/semana |
| Debugging de bugs nivel medio | 2h | 50min | 3 veces/semana |
| Scaffolding de nuevos endpoints | 1.5h | 25min | 2 veces/semana |
| Code review (preparación) | 45min | 25min | 5 veces/semana |
| Documentación técnica | 1h | 30min | 1 vez/semana |

---

## Instrucciones

### Parte 1: Cálculo de Horas Ahorradas

1. Calcula el ahorro semanal por developer para cada tipo de tarea
2. Suma el total de horas ahorradas por semana por developer
3. Calcula el total anual para el equipo completo

### Parte 2: Cálculo de Costes

1. Calcula el coste anual de licencias para el equipo
2. Añade el coste de formación
3. Calcula el coste de la curva de aprendizaje (2 semanas de productividad reducida)
4. Suma el coste total del primer año

### Parte 3: Cálculo de ROI

1. Calcula el coste/hora de un developer (salario anual / horas anuales)
2. Convierte las horas ahorradas en ahorro monetario
3. Aplica la fórmula de ROI

```text
ROI = ((Ahorro anual - Coste total año 1) / Coste total año 1) × 100
```

### Parte 4: Presentación

Rellena esta tabla resumen para presentar a management:

```markdown
## Resumen ROI — Herramienta de IA para Desarrollo

| Concepto | Valor |
|----------|-------|
| Ahorro semanal por developer | ___h |
| Ahorro anual (equipo completo) | $_____ |
| Coste anual (licencias + formación) | $_____ |
| ROI primer año | ___% |
| Break-even | Semana ___ |
```

---

## Criterios de Éxito

```markdown
- [ ] El cálculo de horas incluye todas las tareas del escenario
- [ ] Los costes incluyen licencias, formación y curva de aprendizaje
- [ ] El coste/hora está calculado correctamente
- [ ] El ROI está expresado como porcentaje
- [ ] El break-even está calculado (semana en que ahorro acumulado > coste acumulado)
- [ ] La tabla resumen está completa y lista para presentar
```

---

## Navegación

| | |
|---|---|
| **Ejercicio anterior** | [02-plan-adopcion.md](02-plan-adopcion.md) |
| **Siguiente ejercicio** | [04-gestionar-resistencia.md](04-gestionar-resistencia.md) |
| **Módulo** | [README del módulo](../README.md) |
