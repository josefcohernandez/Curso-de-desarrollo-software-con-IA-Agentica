# ROI para Management

## Introducción

Adoptar una herramienta de IA tiene un coste: licencias, tiempo de aprendizaje, cambio de procesos. Para justificar esta inversión ante management, necesitas datos concretos, no promesas. Este capítulo te da un framework de cálculo de ROI, datos orientativos para el primer business case, y un plan paso a paso para presentarlo.

---

## Framework de Cálculo de ROI

La fórmula base es simple:

```text
ROI = ((Ahorro anual - Coste anual) / Coste anual) × 100

Donde:
  Ahorro anual = Horas ahorradas/semana × Coste/hora × 52 semanas × Num developers
  Coste anual  = (Licencia/mes × 12 × Num developers) + Coste de adopción
```

### Ejemplo Concreto

```text
Datos:
  - Equipo: 5 developers
  - Coste/hora por developer: $60 (salario + overhead)
  - Ahorro estimado: 3 horas/semana por developer (conservador)
  - Licencia herramienta: $100/mes por developer
  - Coste de adopción (one-time): $2,000 (tiempo de setup + formación)

Cálculo:
  Ahorro anual = 3 h/sem × $60/h × 52 sem × 5 devs = $46,800
  Coste anual  = ($100/mes × 12 × 5) + $2,000 = $8,000
  ROI = (($46,800 - $8,000) / $8,000) × 100 = 485%
```

Incluso con estimaciones conservadoras, el ROI suele ser muy favorable porque el coste de las herramientas es bajo comparado con el coste del tiempo de los developers.

---

## Datos Orientativos

Estos datos varían enormemente por contexto, equipo y tipo de proyecto. Úsalos como punto de partida, no como verdad absoluta.

| Dato | Rango típico | Fuente |
|------|-------------|--------|
| Ahorro de tiempo por developer | 2-5 horas/semana | Surveys de la industria (2024-2026) |
| Coste de herramienta | $20-100/mes por developer | Precios públicos (mediados 2026) |
| Tiempo hasta break-even | 2-4 semanas de uso activo | Experiencia reportada por equipos |
| Curva de aprendizaje | 3-5 días para ser productivo | Con formación; más sin ella |
| Impacto en cycle time | Reducción del 15-40% | Depende del tipo de tareas |

### Dónde se ahorra más tiempo

| Tipo de tarea | Ahorro típico | Por qué |
|---------------|---------------|---------|
| Boilerplate y scaffolding | 60-80% | La IA es excelente generando código repetitivo |
| Tests unitarios | 40-60% | La IA genera tests rápidamente, el dev revisa |
| Debugging | 30-50% | El agente explora el codebase más rápido que un humano |
| Code review (preparación) | 20-40% | La IA identifica problemas mecánicos, el humano se centra en lógica |
| Diseño de arquitectura | 5-15% | La IA puede proponer, pero la decisión es humana |

---

## Plan de Presentación a Management

### Paso 1: Piloto Acotado (2-4 semanas)

- Seleccionar 3-5 developers voluntarios (early adopters)
- Medir cycle time baseline durante la primera semana (sin IA)
- Medir cycle time con IA durante las semanas 2-4
- Documentar 3-5 casos de éxito concretos con tiempos

### Paso 2: Datos del Piloto

Preparar una tabla como esta con datos reales:

```markdown
| Tarea | Sin IA | Con IA | Ahorro |
|-------|--------|--------|--------|
| CRUD de usuarios (API + tests) | 4h | 1.5h | 62% |
| Fix bug de autenticación | 2h | 45min | 63% |
| Migración de endpoint legacy | 6h | 2.5h | 58% |
| Code review de PR (200 líneas) | 45min | 25min | 44% |
| Scaffolding de nuevo microservicio | 3h | 40min | 78% |
```

### Paso 3: Cálculo de ROI con Datos Reales

Usar la fórmula del framework con los datos del piloto en lugar de estimaciones genéricas.

### Paso 4: Propuesta de Rollout

```markdown
## Propuesta: Adopción de IA para Desarrollo

### Resultados del piloto (4 semanas, 5 developers)
- Cycle time medio: reducción del 32%
- PRs por semana: incremento del 18%
- Bugs post-merge: sin cambio significativo
- Satisfacción del equipo: 8.2/10

### ROI proyectado (anual, equipo completo de 15 devs)
- Ahorro: $140,400 (3h/sem × $60/h × 52 sem × 15 devs)
- Coste: $21,000 (licencias) + $5,000 (formación)
- ROI: 440%

### Plan de rollout
- Mes 1: Formación del equipo restante (10 devs) en 2 sesiones de 2h
- Mes 2: Adopción progresiva con buddy system (early adopter + nuevo usuario)
- Mes 3: Revisión de convenciones y ajuste de CLAUDE.md
- Mes 4+: Métricas mensuales y optimización continua

### Riesgos y mitigaciones
- Resistencia: abordada con enfoque voluntario y buddy system
- Calidad: code review obligatorio para todo código IA
- Privacidad: configuración de permisos y política de datos sensibles
```

### Paso 5: Presentación

- Duración: 15-20 minutos (no más)
- Estructura: Problema > Piloto > Resultados > ROI > Propuesta > Riesgos
- Evitar jerga técnica excesiva (management no necesita saber qué es un token)
- Llevar los datos del piloto impresos o en slides simples
- Anticipar preguntas sobre seguridad, privacidad y dependencia del proveedor

---

## Errores al Presentar ROI

| Error | Consecuencia |
|-------|-------------|
| Usar solo estimaciones, sin datos reales | Management no confía en números inventados |
| Prometer ahorros de 50%+ | Genera expectativas irrealistas |
| Ignorar los costes ocultos (formación, curva de aprendizaje) | El ROI real será menor de lo presentado |
| No mencionar riesgos | Parece una venta, no una propuesta seria |
| Presentación de 45 minutos | Management pierde interés después de 15 |

---

## Navegación

| | |
|---|---|
| **Anterior** | [04-metricas-productividad.md](04-metricas-productividad.md) |
| **Módulo** | [README del módulo](../README.md) |
