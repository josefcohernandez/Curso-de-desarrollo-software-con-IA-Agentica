# Resistencia al Cambio y Cómo Abordarla

## Introducción

La resistencia a la adopción de IA en equipos de desarrollo no es un problema — es una **señal de que hay personas que se preocupan por su trabajo**. Cada tipo de resistencia tiene un origen legítimo y merece un enfoque específico. Intentar imponer la herramienta o desestimar las preocupaciones es la forma más rápida de fracasar en la adopción.

---

## Los 5 Tipos de Resistencia

| Resistencia | Origen | Cómo abordar |
|-------------|--------|--------------|
| "No confío en código que no escribí" | Legítima — preocupación por calidad | Enseñar técnicas de revisión ([Módulo A3](../../modulo-A3-revision-codigo-ia/README.md)), mostrar que IA es un borrador, no el producto final |
| "Me va a quitar el trabajo" | Miedo laboral | Evidencia: la IA aumenta productividad, no reemplaza. El valor del dev está en las decisiones, no en teclear |
| "Es más lento que hacerlo yo" | Curva de aprendizaje | Dar tiempo. Los primeros 2-3 días son más lentos; después se acelera. Compartir métricas de early adopters |
| "Introduce bugs sutiles" | Experiencia real | Validar la preocupación. Enseñar prácticas de verificación (tests, code review). Es un riesgo real que se mitiga con proceso |
| "No quiero depender de una herramienta" | Autonomía profesional | Respetar. La IA es una opción, no una obligación. Algunos devs son más productivos sin ella en ciertas tareas |

---

## Análisis Detallado de Cada Tipo

### 1. "No confío en código que no escribí"

Esta es probablemente la resistencia más sana. Un developer que no confía ciegamente en código generado tiene **exactamente la actitud correcta** para trabajar con IA.

**Estrategia**:
- Mostrar que el enfoque correcto es revisar el código IA con el mismo rigor (o más) que un PR de un compañero
- Enseñar la checklist de revisión del [Módulo A3](../../modulo-A3-revision-codigo-ia/README.md)
- Demostrar con un ejemplo en vivo: generar código, encontrar un fallo, corregirlo. Esto normaliza el proceso

**Qué decir**: "Tienes razón, no debes confiar ciegamente. Por eso usamos las técnicas de revisión. El agente genera un borrador; tú decides qué entra en producción."

### 2. "Me va a quitar el trabajo"

El miedo laboral es comprensible pero generalmente infundado en el corto-medio plazo. La IA cambia el tipo de trabajo, no lo elimina.

**Estrategia**:
- Compartir datos concretos: las empresas que adoptan IA tienden a contratar más developers, no menos
- Resaltar que el valor del developer está en entender el problema, tomar decisiones de diseño y garantizar calidad — habilidades que la IA no reemplaza
- Ser honesto: la IA sí reduce la necesidad de trabajo mecánico, pero aumenta la capacidad de abordar problemas más complejos

**Qué decir**: "La IA cambia en qué gastas tu tiempo, no si te necesitan. Pasas menos tiempo escribiendo boilerplate y más tiempo diseñando y revisando."

### 3. "Es más lento que hacerlo yo"

Los primeros días con cualquier herramienta nueva son más lentos. Si alguien evalúa la IA basándose en las primeras 2 horas de uso, la conclusión será negativa.

**Estrategia**:
- Pedir una prueba honesta: 5 días hábiles de uso activo antes de evaluar
- Emparejar al developer con un early adopter para las primeras sesiones
- Sugerir empezar con tareas donde la IA brilla (exploración de codebase, tests, boilerplate) en lugar de las más complejas

**Qué decir**: "Dale una semana. Si después de 5 días sigues sintiéndote más lento, lo dejamos. Pero hazme el favor de empezar por estas tareas [lista]."

### 4. "Introduce bugs sutiles"

Esta preocupación es **100% legítima**. La IA introduce bugs. El [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md) documenta en detalle los tipos de fallos y cómo detectarlos.

**Estrategia**:
- Validar la preocupación sin minimizarla
- Mostrar las técnicas de mitigación: tests antes de implementar, code review específico para código IA, verificación de edge cases
- Si es posible, mostrar un ejemplo real donde el agente introdujo un bug y cómo se detectó con las prácticas del curso

**Qué decir**: "Tienes toda la razón, y por eso este proceso incluye verificación obligatoria. Mira cómo funciona [demo]."

### 5. "No quiero depender de una herramienta"

La autonomía profesional es un valor legítimo. Algunos developers prefieren tener control total sobre cada línea de código.

**Estrategia**:
- **Respetar la decisión**. No todo el equipo necesita usar IA para que la adopción sea exitosa
- No vincular evaluaciones de desempeño al uso de IA
- Dejar la puerta abierta: "cuando quieras probar, estamos aquí"
- Con el tiempo, muchos cambian de opinión al ver los resultados de sus compañeros

**Qué decir**: "Entiendo. No es obligatorio. Si en algún momento quieres probar, te ayudamos."

---

## La Regla de Oro de la Adopción

> **Nunca obligar, siempre demostrar.**

Los mejores evangelistas de IA en un equipo no son los managers que imponen herramientas — son los **compañeros que muestran resultados**. Cuando un developer ve que su colega resolvió en 20 minutos algo que normalmente toma 2 horas, la curiosidad es inevitable.

---

## Anti-patrones en la Gestión de la Resistencia

| Anti-patrón | Por qué falla |
|-------------|---------------|
| "Es obligatorio a partir del lunes" | Genera resistencia activa y sabotaje pasivo |
| "Si no usas IA, te quedas atrás" | Crea ansiedad en lugar de motivación |
| Ignorar las preocupaciones | Las preocupaciones no desaparecen, se convierten en resentimiento |
| Forzar pair programming con IA | Invade el espacio de autonomía del developer |
| Vincular bonus/evaluaciones al uso de IA | Incentiva uso superficial, no adopción real |

---

## Checklist para Facilitadores de Adopción

```markdown
- [ ] He identificado a los early adopters naturales del equipo
- [ ] He escuchado (sin rebatir) las preocupaciones de cada miembro
- [ ] Tengo un plan para cada tipo de resistencia identificada
- [ ] He preparado demos con tareas reales del proyecto, no ejemplos genéricos
- [ ] He definido un período de prueba razonable (mínimo 1 semana)
- [ ] No estoy vinculando evaluaciones de desempeño al uso de la herramienta
- [ ] He dejado claro que el uso es voluntario
```

---

## Navegación

| | |
|---|---|
| **Anterior** | [01-fases-adopcion.md](01-fases-adopcion.md) |
| **Siguiente** | [03-convenciones-equipo.md](03-convenciones-equipo.md) |
| **Módulo** | [README del módulo](../README.md) |
