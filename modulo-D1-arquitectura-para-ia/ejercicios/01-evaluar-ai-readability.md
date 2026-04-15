# Ejercicio 1: Evaluar la AI-Readability de un Proyecto

## Objetivo

Evaluar la AI-readability de un proyecto existente usando los 5 pilares del Módulo D1. Producir un informe con puntuación 1-5 por pilar y acciones de mejora priorizadas.

## Contexto

La AI-readability no se mide con una herramienta automática — se evalúa observando cómo interactúa un agente con el código. En este ejercicio usarás los 5 pilares como rúbrica para auditar un proyecto real.

---

## Instrucciones

### Paso 1: Selecciona un proyecto

Elige un proyecto en el que trabajes activamente. Puede ser:
- Un proyecto personal en GitHub
- Un repositorio de tu equipo (con permiso)
- Un proyecto open source que conozcas bien

**Requisito**: el proyecto debe tener al menos 10 archivos de código y algún test.

### Paso 2: Evalúa cada pilar (1-5)

Usa esta rúbrica para cada uno de los 5 pilares:

| Puntuación | Significado |
|------------|-------------|
| 1 | Inexistente: no hay evidencia de este pilar |
| 2 | Mínimo: algún esfuerzo pero inconsistente |
| 3 | Aceptable: presente en la mayoría del código |
| 4 | Bueno: consistente y bien implementado |
| 5 | Excelente: modelo a seguir |

**Pilar 1 — Modularidad explícita**:
- ¿Los directorios tienen responsabilidades claras?
- ¿Hay READMEs en los módulos principales?
- ¿Puede un agente trabajar en un módulo sin leer los demás?

**Pilar 2 — Interfaces como contrato**:
- ¿Las funciones públicas tienen tipos (TypeScript, type hints, etc.)?
- ¿Los inputs y outputs están documentados con tipos o JSDoc?
- ¿Un agente puede generar una llamada correcta solo leyendo la firma?

**Pilar 3 — Tests como especificación**:
- ¿Los tests describen el "qué" (comportamiento) y no solo el "cómo"?
- ¿Se puede entender el comportamiento esperado leyendo solo los tests?
- ¿Los tests de un módulo se ejecutan independientemente?

**Pilar 4 — Convenciones explícitas**:
- ¿Hay un CLAUDE.md, README o documento de convenciones?
- ¿Las reglas de naming, estructura y patrones están escritas?
- ¿Un desarrollador nuevo (o un agente) puede seguirlas sin preguntar?

**Pilar 5 — Bajo acoplamiento**:
- ¿Los módulos se pueden entender aislados?
- ¿Hay imports circulares o dependencias globales?
- ¿Cambiar un módulo requiere cambiar muchos otros?

### Paso 3: Genera el informe

Crea un documento con el siguiente formato:

```markdown
# Informe de AI-Readability: [Nombre del Proyecto]

## Puntuaciones

| Pilar | Puntuación | Observaciones |
|-------|------------|---------------|
| Modularidad explícita | X/5 | ... |
| Interfaces como contrato | X/5 | ... |
| Tests como especificación | X/5 | ... |
| Convenciones explícitas | X/5 | ... |
| Bajo acoplamiento | X/5 | ... |
| **Media** | **X.X/5** | |

## Acciones de Mejora Priorizadas

1. **[Prioridad Alta]** ...
2. **[Prioridad Alta]** ...
3. **[Prioridad Media]** ...
4. **[Prioridad Baja]** ...
```

### Paso 4: Valida con el agente

Pide al agente una tarea real en el proyecto y observa:
- ¿Dónde se atasca?
- ¿Qué preguntas necesita que respondas?
- ¿Qué archivos lee innecesariamente?

Estas observaciones confirman o ajustan tu evaluación.

---

## Criterios de Éxito

- [ ] Has evaluado los 5 pilares con puntuación justificada
- [ ] Has identificado al menos 3 acciones de mejora priorizadas
- [ ] Has validado tu evaluación con una tarea real usando un agente
- [ ] El informe es concreto y accionable, no genérico

## Tiempo Estimado

20 minutos
