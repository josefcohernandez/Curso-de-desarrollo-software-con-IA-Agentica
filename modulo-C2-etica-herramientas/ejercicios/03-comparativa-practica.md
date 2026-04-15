# Ejercicio: Comparativa Práctica de Herramientas

## Objetivo

Realizar la misma tarea de desarrollo con 2 herramientas diferentes de IA y comparar los resultados de forma objetiva, usando los criterios de [05-comparativa-herramientas.md](../teoria/05-comparativa-herramientas.md).

**Tiempo estimado: 15 minutos** (sin contar el tiempo de ejecución de las tareas)

---

## Instrucciones

### Parte 1: Preparación

Elige 2 herramientas de las que tengas acceso (por ejemplo: Claude Code + Copilot, Cursor + Claude Code, etc.).

Elige una tarea de desarrollo que puedas realizar con ambas herramientas. Sugerencias:

| Tarea | Complejidad | Tiempo estimado |
|-------|-------------|-----------------|
| Fix de un bug con stack trace dado | Media | 10-15 min |
| Implementar un endpoint REST con tests | Media-Alta | 20-30 min |
| Explorar un codebase desconocido | Media | 10-15 min |
| Refactorizar un archivo de 200+ líneas | Media | 15-20 min |
| Escribir tests para un módulo existente | Media | 15-20 min |

### Parte 2: Ejecución

Realiza la misma tarea con ambas herramientas, documentando:

```markdown
## Herramienta 1: [Nombre]

### Prompt utilizado
[copiar el prompt exacto]

### Resultado
- ¿Completó la tarea? [ ] Sí  [ ] Parcialmente  [ ] No
- Iteraciones necesarias: ___
- Tiempo total: ___
- ¿Generó tests? [ ] Sí  [ ] No
- Bugs encontrados en el resultado: ___

### Experiencia
- Facilidad de uso: [1-5]
- Velocidad percibida: [1-5]
- Calidad del output: [1-5]
- Notas: ___
```

```markdown
## Herramienta 2: [Nombre]

### Prompt utilizado
[copiar el prompt exacto — idealmente el mismo o equivalente]

### Resultado
- ¿Completó la tarea? [ ] Sí  [ ] Parcialmente  [ ] No
- Iteraciones necesarias: ___
- Tiempo total: ___
- ¿Generó tests? [ ] Sí  [ ] No
- Bugs encontrados en el resultado: ___

### Experiencia
- Facilidad de uso: [1-5]
- Velocidad percibida: [1-5]
- Calidad del output: [1-5]
- Notas: ___
```

### Parte 3: Comparativa

Rellena la tabla comparativa:

```markdown
| Criterio | Herramienta 1 | Herramienta 2 | Ganador |
|----------|--------------|--------------|---------|
| Velocidad | | | |
| Calidad del código | | | |
| Necesidad de iteración | | | |
| Experiencia de uso | | | |
| Tests generados | | | |
| Total | | | |
```

### Parte 4: Conclusión

Responde:

1. ¿Cuál usarías para esta tarea específica? ¿Por qué?
2. ¿Hay tareas donde preferirías una sobre la otra?
3. ¿Las usarías de forma complementaria? ¿Cómo?

---

## Criterios de Éxito

```markdown
- [ ] Se usó el mismo prompt (o equivalente) en ambas herramientas
- [ ] Se documentaron los resultados con datos objetivos (tiempo, iteraciones, bugs)
- [ ] La comparativa incluye tanto datos cuantitativos como experiencia subjetiva
- [ ] La conclusión es matizada (no "X es mejor en todo")
- [ ] Se identifica al menos un escenario donde cada herramienta gana
```

---

## Nota Importante

Si solo tienes acceso a una herramienta, puedes hacer la comparativa entre dos modos de la misma herramienta (por ejemplo, Claude Code en modo interactivo vs. con Plan Mode, o Cursor con Composer vs. con inline edits).

---

## Navegación

| | |
|---|---|
| **Ejercicio anterior** | [02-analisis-compliance.md](02-analisis-compliance.md) |
| **Siguiente ejercicio** | [04-caso-etico.md](04-caso-etico.md) |
| **Módulo** | [README del módulo](../README.md) |
