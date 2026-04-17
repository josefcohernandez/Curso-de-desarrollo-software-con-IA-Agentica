# 02 — Fase 1: La Entrevista de Requisitos

## Qué es y por qué funciona

La entrevista de requisitos es una conversación estructurada donde delegas al agente el rol de investigador: él hace las preguntas, tú respondes. El agente llega a esa conversación con dos ventajas que tú no tienes en ese momento:

1. **No tiene prejuicios sobre el dominio**: no da nada por sentado porque no conoce tu contexto. Pregunta lo que cualquier desarrollador senior preguntaría el primer día.
2. **Tiene un repertorio sistemático de edge cases**: el agente ha procesado millones de especificaciones y sabe qué preguntas revela los problemas más comunes en cada tipo de sistema.

El resultado es una conversación que descubre en 15 minutos lo que normalmente tarda días o semanas en aparecer como bug en producción.

---

## Técnicas de entrevista

Hay tres formas de iniciar una entrevista según cuánto contexto tienes:

### Entrevista abierta

Cuando tienes una idea general pero poca claridad en los detalles:

```text
Voy a construir [descripción en 2-3 oraciones].
Entrevístame sobre los requisitos antes de empezar a diseñar.
Haz una pregunta a la vez y espera mi respuesta antes de continuar.
Al final, genera un resumen de las decisiones tomadas.
```

### Entrevista dirigida

Cuando ya tienes claridad en la funcionalidad core pero quieres explorar áreas específicas:

```text
Necesito implementar [feature]. Tengo claros los casos felices.
Entrevístame específicamente sobre:
- Seguridad y autenticación
- Manejo de errores y casos límite
- Requisitos de rendimiento y escalabilidad
- Integraciones con sistemas existentes
Una pregunta a la vez.
```

### Entrevista por dominio

Cuando trabajas en un dominio específico con requisitos bien conocidos:

```text
Voy a construir un sistema de [pagos / notificaciones / autenticación / inventario].
Usa las preguntas estándar de este dominio más las específicas de este contexto:
[descripción del contexto]
Una pregunta a la vez.
```

---

## Las seis categorías de preguntas

Una entrevista completa cubre estas seis categorías. Si la entrevista no toca alguna, puedes pedirle al agente que la explore explícitamente.

### 1. Funcionalidad core

- ¿Cuáles son los flujos principales que debe soportar el sistema?
- ¿Quiénes son los actores (usuarios, sistemas) y cuál es el rol de cada uno?
- ¿Cuáles son las operaciones CRUD necesarias y sobre qué entidades?

### 2. Edge cases y errores

- ¿Qué ocurre cuando [el input es inválido / el servicio externo falla / el usuario no tiene permisos]?
- ¿Hay operaciones que deben ser atómicas o transaccionales?
- ¿Cómo se manejan los timeouts?

### 3. Requisitos no funcionales

- ¿Cuántos usuarios concurrentes debe soportar?
- ¿Cuál es el tiempo de respuesta máximo aceptable?
- ¿Hay requisitos de disponibilidad (SLA)?
- ¿Qué datos son sensibles y cómo deben protegerse?

### 4. Integraciones

- ¿Con qué sistemas externos debe integrarse?
- ¿Qué formato de datos esperan esos sistemas?
- ¿Qué ocurre si una integración no está disponible?

### 5. Restricciones técnicas y de negocio

- ¿Hay restricciones de stack tecnológico?
- ¿Hay plazos o dependencias con otros equipos?
- ¿Hay regulaciones (GDPR, PCI, HIPAA) que apliquen?

### 6. UX y flujos de usuario

- ¿Cómo sabe el usuario que la operación fue exitosa?
- ¿Qué feedback se da en caso de error?
- ¿Hay estados intermedios que mostrar (cargando, procesando)?

---

## Ejemplo real: entrevista para sistema de notificaciones

A continuación se muestra una entrevista real condensada. El agente hace las preguntas; el desarrollador responde.

---

**Agente**: ¿Para qué tipo de aplicación es el sistema de notificaciones? ¿Web, móvil, o ambos?

**Desarrollador**: Es una app móvil (React Native). También necesitamos email.

---

**Agente**: ¿Los usuarios recibirán notificaciones en tiempo real o pueden ser asíncronas con algo de delay?

**Desarrollador**: Las de eventos críticos deben ser inmediatas. Las de resumen diario pueden ser asíncronas.

---

**Agente**: ¿Qué ocurre si el usuario tiene la app cerrada cuando llega una notificación push crítica?

**Desarrollador**: ...buena pregunta. Debe reintentarse hasta 3 veces en los siguientes 30 minutos.

---

**Agente**: ¿Cómo gestionáis los tokens de dispositivo cuando el usuario desinstala y reinstala la app?

**Desarrollador**: No lo habíamos pensado. Supongo que hay que invalidar el token anterior y registrar el nuevo.

---

**Agente**: ¿Los usuarios pueden configurar preferencias de notificación (desactivar ciertos tipos, configurar horarios de no molestar)?

**Desarrollador**: Sí, deben poder desactivar cada categoría por separado. No molestar entre 22:00 y 08:00 es obligatorio.

---

**Agente**: ¿Hay requisitos de GDPR? ¿Necesitáis consentimiento explícito antes de enviar notificaciones?

**Desarrollador**: Sí, en Europa necesitamos consentimiento. En Latinoamérica no por ahora.

---

**Agente**: ¿Cuántos usuarios simultáneos debe soportar el sistema en el pico máximo?

**Desarrollador**: Unos 50.000 usuarios activos. Los picos son en horario de oficina.

---

**Agente**: ¿Necesitáis un historial de notificaciones enviadas para auditoría o análisis?

**Desarrollador**: Sí, al menos 90 días de historial.

---

**Resultado de la entrevista**: en 8 preguntas, el agente descubrió cinco requisitos que el desarrollador no había considerado inicialmente: la política de reintentos para push críticos, la gestión de tokens al reinstalar, las preferencias por categoría, el horario de no molestar, y el requisito de GDPR por región geográfica. Cada uno de estos hubiera sido un bug de producción o un retrabajo de diseño.

---

## Cuándo parar la entrevista

La entrevista está completa cuando:

1. El agente ya no hace preguntas nuevas (está repasando lo que ya sabe)
2. Todas las categorías principales están cubiertas
3. Las preguntas se vuelven triviales o demasiado específicas de implementación

Señales de que la entrevista está incompleta:
- Hay categorías enteras que no se han tocado (por ejemplo, no se habló de seguridad en un sistema con datos de usuarios)
- Las respuestas del desarrollador generan más preguntas de las que resuelven
- El desarrollador sigue diciendo "no lo había pensado" en cada respuesta

En ese caso, puedes pedir explícitamente: "¿Hay alguna categoría de requisitos que no hayamos explorado?"

---

## Documentar las decisiones durante la entrevista

Un anti-patrón común es completar la entrevista y luego tener que reconstruir qué se decidió y por qué. La entrevista debe documentar no solo el QUÉ sino el PORQUÉ.

Al final de cada respuesta significativa, puedes añadir contexto:

> "La política de reintentos es hasta 3 veces en 30 minutos **porque nuestro proveedor de push cobra por intento fallido** y queremos limitar el coste."

Este contexto en la spec explica decisiones que de otro modo parecerían arbitrarias cuando alguien (tú mismo, seis meses después) revise el código.

Al terminar la entrevista, pide al agente que genere un resumen de decisiones:

```text
Resume las decisiones clave tomadas en la entrevista:
- Qué se decidió
- Por qué (si se mencionó)
- Qué queda por decidir
```

---

## Anti-patrones de entrevista

| Anti-patrón | Por qué falla | Alternativa |
|-------------|---------------|-------------|
| Entrevista de 50 preguntas | Fatiga, respuestas superficiales, contexto saturado | Limitar a 8-12 preguntas, priorizar por riesgo |
| Preguntas triviales ("¿Necesitas un nombre de usuario?") | Pierden tiempo sin aportar valor | Centrarse en edge cases y requisitos no funcionales |
| No documentar el porqué de las decisiones | En 6 meses nadie sabe por qué se decidió X | Añadir contexto en las respuestas |
| Saltar directamente a la spec sin entrevista | Se pierde el valor de la exploración | Siempre entrevistar antes de especificar |
| Respuestas de una sola palabra | El agente no puede inferir el contexto necesario | Desarrollar las respuestas, añadir el porqué |
| Dejar preguntas sin responder ("ya lo veremos") | Decisiones diferidas se convierten en sorpresas en implementación | Tomar la decisión ahora, aunque sea preliminar |

---

> **En Cursor**: inicia la entrevista en la vista de chat con el prompt de entrevista abierta. Cursor mantendrá el contexto a lo largo de la conversación.
>
> **En GitHub Copilot Chat**: el contexto se mantiene en la sesión activa. Inicia la entrevista en una sesión dedicada.
>
> **En Claude Code**: usa el modo interactivo normal. Al terminar la entrevista, pide al agente que genere el SPEC.md. Si cambias de herramienta o vas a empezar la implementación en una sesión aparte, reinicia el contexto antes de seguir.
