# Ejercicio 4: Diseñar un Benchmark de Productividad para un Equipo

## Objetivo

Diseñar un benchmark completo de productividad para un equipo de 5 personas que está adoptando IA agéntica en su flujo de desarrollo. El benchmark debe medir antes/después, incluir métricas cuantitativas y cualitativas, y producir un formato de reporte que el equipo pueda usar trimestralmente.

---

## Contexto del Equipo

Tu equipo tiene las siguientes características:

| Persona | Rol | Experiencia | Uso actual de IA |
|---------|-----|-------------|-----------------|
| Ana | Tech Lead | 8 años | Usa Claude Code desde hace 3 meses, nivel intermedio |
| Bruno | Senior Dev | 5 años | Usa Cursor desde hace 6 meses, power user |
| Carmen | Mid Dev | 3 años | Usa Copilot básico, quiere probar herramientas más potentes |
| Diego | Junior Dev | 1 año | No usa IA, escéptico pero abierto a probar |
| Elena | QA Engineer | 4 años | Usa ChatGPT para generar test cases, no herramientas de código |

### Contexto del proyecto

- Stack: React + Node.js + PostgreSQL
- Sprints de 2 semanas
- Usan Jira para tracking, GitHub para código
- Deployment semanal a producción
- Bug rate actual: ~3 bugs en producción por sprint

---

## Parte 1: Definir Métricas (10 min)

Diseña el conjunto de métricas que vas a medir. Usa el agente para ayudarte:

```text
Necesito definir métricas de productividad para un equipo de 5 personas 
que adopta IA agéntica. Las métricas deben cubrir:
1. Velocidad (qué tan rápido se completan las tareas)
2. Calidad (qué tan bueno es el código producido)
3. Coste (cuánto cuesta la IA)
4. Sostenibilidad (fatiga, satisfacción, skills)

Para cada métrica, define:
- Nombre
- Cómo se mide (fórmula o proceso)
- Fuente de datos (Jira, GitHub, encuesta, etc.)
- Frecuencia de medición
- Valor baseline esperado (antes de optimizar)
```

### Plantilla de métricas

Completa esta tabla (mínimo 8 métricas, 2 por categoría):

| Categoría | Métrica | Cómo se mide | Fuente | Frecuencia |
|-----------|---------|-------------|--------|------------|
| Velocidad | | | | |
| Velocidad | | | | |
| Calidad | | | | |
| Calidad | | | | |
| Coste | | | | |
| Coste | | | | |
| Sostenibilidad | | | | |
| Sostenibilidad | | | | |

---

## Parte 2: Diseñar el Proceso de Medición (10 min)

Define cómo vas a ejecutar el benchmark siguiendo el proceso de 5 pasos:

### Paso 1: Baseline (2 semanas)

```text
¿Qué datos necesitas recoger durante las 2 semanas de baseline?
¿Cómo los recoges sin alterar el flujo de trabajo del equipo?
¿Qué herramientas necesitas configurar?
```

### Paso 2: Implementación de mejoras

```text
¿Qué formación o cambios introduces después del baseline?
¿Cómo adaptas las mejoras al nivel de cada persona?
  - Ana (intermedia): ¿qué mejorar?
  - Bruno (power user): ¿qué mejorar?
  - Carmen (básica): ¿qué mejorar?
  - Diego (sin IA): ¿cómo onboardear?
  - Elena (QA): ¿qué herramientas específicas?
```

### Paso 3: Medición post-mejora (2 semanas)

```text
¿Mides exactamente las mismas métricas que en el baseline?
¿Cómo controlas variables externas (sprint más fácil/difícil, 
vacaciones, etc.)?
```

### Paso 4: Cálculo de delta y ROI

```text
¿Cómo calculas el ROI?
¿Qué haces si las métricas de calidad bajan aunque la velocidad suba?
¿Cómo presentas los resultados a dirección?
```

### Paso 5: Iteración trimestral

```text
¿Qué ajustas cada trimestre?
¿Cuándo decides que una herramienta/práctica no funciona?
```

---

## Parte 3: Diseñar la Encuesta de Satisfacción (10 min)

Crea una encuesta mensual para el equipo. Usa el agente:

```text
Diseña una encuesta de satisfacción mensual para un equipo de 5 
desarrolladores que usan IA agéntica. La encuesta debe:
- Ser anónima
- Tardar menos de 3 minutos
- Cubrir: productividad percibida, fatiga, confianza en la IA, 
  impacto en habilidades, satisfacción general
- Tener preguntas en escala 1-5 y al menos 1 abierta
- Incluir alertas automáticas (qué puntajes disparan una acción)
```

---

## Parte 4: Crear el Template de Reporte (10 min)

Diseña el formato del reporte trimestral que presentarás:

```markdown
# Reporte de Productividad IA - Q[N] 2026

## Resumen ejecutivo
[2-3 frases con los hallazgos principales]

## Métricas de velocidad
[Tabla comparativa: baseline vs periodo actual]

## Métricas de calidad
[Tabla comparativa + tendencia]

## Análisis de costes
[Coste total, coste por tarea, ROI]

## Satisfacción del equipo
[Resumen de encuestas, alertas activadas]

## Acciones para Q[N+1]
[3-5 acciones concretas basadas en los datos]
```

---

## Entregable

Un documento completo de "Plan de Benchmark" con:

1. **Tabla de métricas**: mínimo 8 métricas con todos los campos
2. **Proceso de 5 pasos**: adaptado a este equipo específico
3. **Plan de formación**: personalizado para cada persona del equipo
4. **Encuesta de satisfacción**: preguntas concretas con escala y alertas
5. **Template de reporte trimestral**: listo para usar

---

## Criterios de Evaluación

- Las métricas cubren las 4 categorías (velocidad, calidad, coste, sostenibilidad)
- El proceso de medición es realista (no requiere que el equipo deje de trabajar para medir)
- El plan de formación está personalizado por nivel (no genérico)
- La encuesta es breve (< 3 min) y tiene alertas accionables
- El template de reporte es presentable a dirección (no solo técnico)
- Se contempla qué hacer si las métricas muestran resultados negativos
