# Ejercicio 4: Simulación de Degradación por Contexto

## Objetivo

Realizar una sesión deliberadamente larga con un agente de código para **observar en primera persona** cómo se degrada la calidad de las respuestas. Documentar el punto de inflexión y practicar la técnica de `/clear` + reinyección de contexto.

**Tiempo estimado: 10 minutos de preparación + sesión práctica**

---

## Preparación

### Elige un proyecto

Selecciona un proyecto de tamaño medio que ya tengas en tu máquina. Ideal:
- Una aplicación web con backend y frontend
- Al menos 10-15 archivos de código
- Que conozcas bien (para poder evaluar si las respuestas del agente son correctas)

### Prepara la hoja de seguimiento

Copia esta tabla y tenla abierta durante la sesión:

| Checkpoint | Intercambio # | Calidad (1-5) | Coherencia con decisiones previas | Contradicciones detectadas | Notas |
|------------|---------------|---------------|-----------------------------------|---------------------------|-------|
| Inicio | 1-5 | | | | |
| Punto 1 | ~10 | | | | |
| Punto 2 | ~20 | | | | |
| Punto 3 | ~30 | | | | |
| Post-clear | 31+ | | | | |

**Escala de calidad**:
- 5 = Respuestas precisas, contextualizadas, sin errores
- 4 = Respuestas buenas con algún detalle genérico
- 3 = Respuestas correctas pero menos adaptadas al proyecto
- 2 = Contradicciones o duplicaciones evidentes
- 1 = Respuestas incoherentes o que ignoran instrucciones previas

---

## Protocolo de la Sesión

### Fase 1: Establecer contexto (intercambios 1-5)

Empieza con instrucciones claras y específicas:

```text
Estamos trabajando en [nombre del proyecto]. Usa TypeScript, no JavaScript.
La base de datos es PostgreSQL. El estilo de código sigue las convenciones
de [X]. No uses librerías externas sin consultarme primero.
```

Pide al agente que lea 2-3 archivos clave del proyecto. Anota las decisiones explícitas que se toman.

### Fase 2: Trabajo progresivo (intercambios 6-20)

Asigna tareas de complejidad creciente, sin hacer `/clear`:

1. Añadir una función simple a un archivo existente
2. Crear un nuevo endpoint que interactúe con la base de datos
3. Pedir que lea varios archivos para entender una parte del código
4. Refactorizar una función existente
5. Añadir tests para lo implementado
6. Implementar una feature que toque 3-4 archivos

**En cada checkpoint (~10 y ~20), evalúa**:
- ¿Sigue usando TypeScript o se cambió a JavaScript?
- ¿Las soluciones son consistentes con las convenciones establecidas?
- ¿Recuerda las decisiones tomadas en los primeros intercambios?

### Fase 3: Provocar degradación (intercambios 21-30)

Ahora empuja deliberadamente los límites:

1. Pide que lea 3-4 archivos más
2. Pide un cambio que contradiga ligeramente algo anterior y observa si el agente lo nota
3. Haz preguntas sobre código de los primeros intercambios
4. Pide implementar algo que requiera recordar múltiples decisiones previas

**En el checkpoint ~30, evalúa cuidadosamente**:
- ¿La calidad bajó respecto a los primeros intercambios?
- ¿Hay contradicciones con las decisiones iniciales?
- ¿Las respuestas se sienten más genéricas?

### Fase 4: Recuperación con `/clear` (intercambio 31+)

Ejecuta `/clear` (o su equivalente en tu herramienta). Luego reinyecta **solo** el contexto esencial:

```text
Estamos trabajando en [proyecto]. Contexto esencial:
- Lenguaje: TypeScript (no JavaScript)
- Base de datos: PostgreSQL
- En la sesión anterior implementamos [resumen de 3-4 líneas]
- Próximo paso: [siguiente tarea]
```

Pide la misma tarea que asignaste en el intercambio ~28-30. Compara la calidad.

---

## Qué Documentar

Al terminar la sesión, responde estas preguntas:

1. **¿En qué intercambio notaste el primer signo de degradación?**
2. **¿Cuál fue el signo más claro?** (contradicción, duplicación, olvido, calidad genérica)
3. **¿La calidad post-`/clear` fue comparable a la del inicio?**
4. **¿Qué información fue esencial reinyectar y cuál pudiste omitir?**
5. **¿Qué harías diferente la próxima vez?** (dividir antes, usar subagentes, etc.)

---

## Criterio de Éxito

- Completar la sesión de 30+ intercambios documentando cada checkpoint
- Identificar al menos 1 signo claro de degradación
- Practicar la recuperación con `/clear` y comprobar la diferencia de calidad
- Formular al menos 2 reglas personales para gestionar sesiones largas
