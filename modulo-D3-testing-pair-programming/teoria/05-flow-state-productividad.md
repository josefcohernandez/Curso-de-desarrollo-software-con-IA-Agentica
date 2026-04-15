# Flow State y Productividad con IA

## Introducción

Trabajar con un agente de IA no es simplemente "más rápido". Cambia fundamentalmente cómo fluye tu jornada: hay más esperas, más revisiones y un tipo de fatiga mental que no existía antes. Gestionar tu productividad con IA requiere entender estos cambios y adoptar estrategias específicas.

---

## El Problema de la Latencia

### Qué es el flow state

El flow state (estado de flujo) es un estado de concentración profunda donde produces tu mejor trabajo. Se caracteriza por:

- Pérdida de noción del tiempo
- Concentración sin esfuerzo
- Feedback inmediato entre acción y resultado

### Cómo la IA interrumpe el flow

Cada vez que envías un prompt y esperas la respuesta del agente, se produce una **micro-interrupción**. Según la investigación en productividad, recuperar el flow state después de una interrupción toma entre 10 y 25 minutos. Si el agente tarda 30 segundos en responder y lo consultas 20 veces por hora, pierdes más flow del que ganas en velocidad.

### El ciclo destructivo

```text
Prompt → Espera (15-60s) → Revisar respuesta → Corregir → Prompt → Espera...
```

Cada ciclo rompe tu concentración. Si la respuesta requiere corrección, se añade frustración al ciclo.

---

## Estrategias para Mantener el Flow

### 1. Tareas en background

En Claude Code, puedes enviar tareas al background con `Ctrl+B` mientras sigues trabajando en otra cosa. Esto convierte la espera en trabajo paralelo.

```text
Ejemplo de flujo:
1. Envías "Refactoriza el módulo de pagos" → Ctrl+B (background)
2. Mientras tanto, trabajas manualmente en la documentación
3. Recibes notificación cuando el agente termina
4. Revisas el resultado cuando tú decides
```

### 2. Plan Mode antes de ejecutar

Usar Plan Mode (pensar antes de actuar) reduce el número de iteraciones. Menos iteraciones = menos interrupciones.

```text
Flujo sin Plan Mode: 5 prompts × 30s espera = 2.5 min de espera + 5 interrupciones
Flujo con Plan Mode: 1 plan + 1 ejecución = 1 min de espera + 2 interrupciones
```

### 3. Acumular tareas (batching)

En vez de enviar una tarea cada vez que se te ocurre, acumula 3-5 tareas y envíalas en ráfaga.

```text
En vez de:
  "Arregla el import en archivo A" → espera → "Añade el type a archivo B" → espera

Haz:
  "Ejecuta estas 3 tareas:
   1. Arregla el import en archivo A
   2. Añade el type a archivo B  
   3. Actualiza el test en archivo C"
```

### 4. Intercalar trabajo manual

Usa las esperas del agente para trabajo que no requiere IA: revisar PRs, responder mensajes, tomar notas, planificar la siguiente tarea.

---

## Fatiga de Supervisión

### El problema real

Revisar código generado por IA es mentalmente agotador. No es como leer código de un colega: el código del agente puede ser sutilmente incorrecto de formas que requieren atención sostenida para detectar. Supervisar 8 horas seguidas produce un tipo de burnout específico.

### Síntomas de fatiga de supervisión

- Empiezas a aprobar código sin leerlo realmente
- Dejas de cuestionar las decisiones del agente
- Sientes que "la IA sabe más que yo" (señal de rendición cognitiva)
- Frustración creciente cuando el agente no entiende algo

### La regla del 50/50

No dediques más del 50% de tu jornada laboral al modo "supervisión de IA". Alterna con:

| Actividad | Tipo | Beneficio |
|-----------|------|-----------|
| Trabajo manual de código | Activo, sin IA | Mantiene tus habilidades técnicas |
| Reuniones y diseño | Colaborativo | Descanso de la pantalla de código |
| Code review de compañeros | Lectura crítica | Diferente tipo de atención |
| Documentación y planificación | Escritura | Pensamiento de alto nivel |
| Aprendizaje y experimentación | Exploración | Recarga cognitiva |

### El límite de 90 minutos

Las sesiones de supervisión intensa de IA no deben superar los 90 minutos sin pausa. Después de 90 minutos:

1. **Para** la sesión (guarda contexto con `/compact` o notas)
2. **Cambia** de actividad por al menos 15-20 minutos
3. **Retoma** con ojos frescos -- es probable que detectes problemas que antes no veías

---

## Métricas Personales de Productividad

### 4 métricas para medirte

| Métrica | Cómo medirla | Qué indica |
|---------|-------------|------------|
| **Ratio de iteraciones** | Prompts enviados / tareas completadas | Calidad de tus prompts (menos = mejor) |
| **Tokens gastados vs valor** | Coste de tokens / features entregadas | Eficiencia económica |
| **Tasa de aceptación** | Respuestas aceptadas sin modificar / total | Calidad de la colaboración |
| **Satisfacción subjetiva** | Auto-evaluación 1-5 al final de cada sesión | Sostenibilidad (burnout es real) |

### Cómo llevar el registro

Formato simple en un archivo de notas diario:

```markdown
## 2026-04-14 - Sesión mañana

- Tareas completadas: 3 (auth refactor, endpoint nuevo, fix pagination)
- Prompts totales: 12
- Ratio: 4 prompts/tarea
- Modo predominante: Ping-pong TDD
- Satisfacción: 4/5
- Nota: El refactor de auth necesitó 6 prompts, demasiados. 
  Debí usar Plan Mode antes de empezar.
```

### Interpretación de las métricas

| Ratio de iteraciones | Interpretación |
|----------------------|----------------|
| 1-2 prompts/tarea | Excelente -- tus prompts son precisos y las tareas están bien definidas |
| 3-5 prompts/tarea | Normal -- hay margen de mejora en especificación o en el modo de trabajo |
| 6+ prompts/tarea | Problema -- necesitas mejorar prompts, cambiar de modo, o la tarea no es adecuada para IA |

| Satisfacción | Interpretación |
|-------------|----------------|
| 5/5 | Flow state sostenido, buen balance |
| 3-4/5 | Normal, con fricciones menores |
| 1-2/5 | Burnout de supervisión -- reduce el uso de IA mañana |

---

## Resumen

- La latencia del agente rompe el flow state; usa background, Plan Mode y batching para minimizar interrupciones
- La fatiga de supervisión es real y peligrosa: cuando dejas de cuestionar al agente, dejas de aportar valor
- Regla del 50/50: máximo la mitad de tu jornada en supervisión de IA
- Sesiones de supervisión: máximo 90 minutos sin pausa
- Mide 4 métricas (iteraciones, coste, aceptación, satisfacción) para mejorar continuamente
- La productividad con IA no es "hacer más rápido" -- es hacer **mejor** con menos fatiga
