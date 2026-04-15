# El debugging como disciplina amplificada por IA

## Por qué debugging es donde la IA aporta más valor

De todas las tareas de desarrollo, el debugging es donde un agente de código IA brilla con más fuerza — pero solo si el humano dirige el proceso.

**Las razones:**
- El debugging requiere leer mucho código rápidamente — los agentes leen más rápido que cualquier humano
- Requiere correlacionar patrones entre archivos distantes — los agentes mantienen todo en contexto
- Requiere generar y probar múltiples hipótesis — los agentes pueden explorar varias en paralelo
- Requiere conocimiento enciclopédico de mensajes de error — los agentes han "visto" millones de stack traces

**Pero no todo:**
- Los agentes no pueden ejecutar código mentalmente con precisión
- No entienden side effects temporales (race conditions, timing issues)
- No tienen intuición sobre qué "huele mal" en el sistema
- No conocen el historial del proyecto ni las decisiones pasadas

## El modelo mental correcto

> La IA es un **asistente de investigación**, no un detective independiente.

Piensa en el debugging con IA como dirigir a un equipo de investigadores:

| Rol | Tú (detective) | La IA (asistente) |
|-----|----------------|-------------------|
| **Decide qué investigar** | Defines el bug y su prioridad | Busca en el código las áreas relevantes |
| **Formula hipótesis** | Propones las causas más probables | Verifica cada hipótesis en el código |
| **Evalúa evidencia** | Decides si la evidencia es concluyente | Lee logs, traces y diffs rápidamente |
| **Decide el fix** | Apruebas o rechazas la solución | Propone e implementa el cambio |
| **Valida el resultado** | Confirmas que el bug está resuelto | Ejecuta tests y verifica edge cases |

## El workflow de debugging asistido

```text
┌──────────┐    ┌──────────┐    ┌──────────────┐    ┌──────────────┐
│ REPRODUCIR│───▶│  AISLAR  │───▶│ DIAGNOSTICAR │───▶│ FIX+VALIDAR  │
│           │    │          │    │  (con IA)    │    │              │
│ Error     │    │ Scope    │    │ Hipótesis    │    │ Implementar  │
│ exacto +  │    │ del      │    │ verificadas  │    │ + tests +    │
│ repro     │    │ problema │    │ una a una    │    │ edge cases   │
│ steps     │    │          │    │              │    │              │
└──────────┘    └──────────┘    └──────────────┘    └──────────────┘
     ▲                                                      │
     └──────────────── Si el fix no resuelve ───────────────┘
```

Cada paso tiene un propósito claro y un entregable:

| Paso | Propósito | Entregable |
|------|-----------|------------|
| **Reproducir** | Confirmar que el bug es real y consistente | Error exacto + pasos para reproducir |
| **Aislar** | Reducir el espacio de búsqueda | Lista de archivos involucrados + 2-3 hipótesis |
| **Diagnosticar** | Encontrar la causa raíz | Causa confirmada con evidencia |
| **Fix + Validar** | Resolver sin introducir regresiones | Código corregido + tests nuevos + tests existentes pasando |

## Diferencias clave con debugging manual

### Sin IA

```text
1. Ver el error
2. Poner breakpoints donde "creo" que está el problema
3. Ejecutar paso a paso
4. Inspeccionar variables
5. Mover breakpoints si no era ahí
6. Repetir 3-5 hasta encontrar la causa
7. Arreglar
8. Probar manualmente
```

**Problema**: el paso 2 depende de tu intuición. Si el bug está en un módulo que no conoces bien, puedes tardar horas.

### Con IA

```text
1. Ver el error
2. Pedir al agente que trace la cadena de llamadas
3. Recibir 2-3 hipótesis con justificación
4. Pedir verificación de la hipótesis más probable
5. Confirmar o descartar con evidencia
6. Pedir el fix cuando la causa está confirmada
7. Validar con tests automáticos
```

**Ventaja**: el paso 2 no depende de tu intuición sino de la capacidad del agente de leer todo el código relevante en segundos.

## Cuándo el debugging con IA falla

Hay categorías de bugs donde la IA tiene dificultades:

| Tipo de bug | Dificultad para IA | Por qué |
|-------------|--------------------|---------| 
| Errores lógicos con stack trace | Baja | Información rica, patrón reconocible |
| Errores de configuración | Baja-Media | Suele ser un valor incorrecto en un archivo |
| Race conditions | Alta | Dependen del timing, no del código estático |
| Memory leaks | Alta | Requieren análisis de runtime, no de código |
| Bugs intermitentes | Alta | No se pueden reproducir consistentemente |
| Bugs de UI/UX | Alta | Requieren contexto visual que el agente no tiene |

Para bugs de alta dificultad, la IA sigue siendo útil pero como **asistente de investigación**, no como solucionador directo. Úsala para buscar patrones en el código que podrían causar el problema, mientras tú usas herramientas de profiling y debuggers tradicionales.

---

[Siguiente: Workflow de debugging con agente →](02-workflow-debugging.md)
