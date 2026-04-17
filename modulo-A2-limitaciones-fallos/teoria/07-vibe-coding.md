# Vibe Coding — Riesgos de Delegar Sin Supervisar

## Qué Es el Vibe Coding

En febrero de 2025, Andrej Karpathy acuñó el término en una publicación que se volvió viral:

> "There's a new kind of coding I call 'vibe coding', where you fully give in to the vibes, embrace exponentials, and forget that the code even exists."

La descripción es precisa: vibe coding es la práctica de generar código con IA sin leerlo, entenderlo ni revisarlo. El criterio de aceptación es si "parece funcionar" en el happy path. No se revisa el diff, no se leen los cambios, no se ejecutan tests. Se acepta lo que el agente produce basándose en la respuesta inmediata.

No es un fenómeno marginal. La adopción de herramientas de IA ya es suficientemente amplia como para que el vibe coding aparezca de forma natural en equipos reales: la velocidad aparente hace que revisar parezca un coste innecesario.

Este fichero examina el fenómeno desde una perspectiva profesional — no para moralizarlo, sino para entender sus riesgos concretos y gestionar el nivel de delegación de forma consciente.

---

## El Espectro de Delegación

La delegación a un agente de código no es binaria. Existe un espectro continuo con cuatro niveles diferenciados por el grado de supervisión y el riesgo asociado.

| Nivel | Nombre | Descripción | Revisión típica | Riesgo |
|-------|--------|-------------|-----------------|--------|
| 1 | **Autocompletado** | Sugerencias línea a línea mientras escribes | Inmediata, en el momento de aceptar | Bajo |
| 2 | **Generación asistida** | Bloques de código o funciones generadas a pedido | Revisión del diff antes de aceptar | Medio |
| 3 | **Delegación supervisada** | Tareas completas delegadas al agente, con supervisión del resultado | Revisión de output + ejecución de tests | Medio-alto |
| 4 | **Vibe coding** | Generación continua sin revisión; se acepta sin leer ni entender | Ninguna o superficial | Alto |

Este curso trabaja en los **niveles 2 y 3**. El nivel 2 es la operación normal de sesiones de trabajo con IA. El nivel 3 es apropiado para tareas bien definidas con buena cobertura de tests. El nivel 4 — vibe coding — es donde los problemas documentados en los ficheros anteriores de este módulo se acumulan sin filtro.

El nivel 1 tiene riesgo bajo no porque las sugerencias sean siempre correctas, sino porque el contexto de revisión es inmediato: ves la sugerencia en el mismo momento en que la escribirías tú.

---

## Los Riesgos Documentados

### Seguridad

Un dato útil para fijar el orden de magnitud: informes recientes como el GenAI Code Security Report de Veracode han encontrado porcentajes altos de problemas de seguridad en salidas generadas por IA cuando no hay revisión rigurosa. En plataformas donde el vibe coding es el modo por defecto, análisis independientes también han detectado vulnerabilidades críticas con frecuencia preocupante (inyección SQL, autenticación bypassable, secretos expuestos en el código).

Esto no es un fallo de los modelos, es una consecuencia del proceso: cuando el criterio de aceptación es "funciona", las vulnerabilidades que no impiden la ejecución pasan sin revisión.

```javascript
// Código que "funciona" pero tiene una vulnerabilidad crítica:
app.get("/api/user/:id", (req, res) => {
  const query = `SELECT * FROM users WHERE id = ${req.params.id}`;
  db.query(query, (err, result) => res.json(result));
});
// SQL injection directa. El happy path funciona perfectamente.
// Un atacante con id="1 OR 1=1" extrae toda la tabla.
```

En un flujo de vibe coding, este código pasa porque la demo funciona.

### Deuda técnica acelerada

El código generado por un agente sin instrucciones de estilo tiende a ser funcional pero inconsistente: patrones mezclados, nomenclatura variable, manejo de errores heterogéneo. Cuando esto se acumula durante semanas sin revisión, el resultado es una base de código que nadie puede mantener con confianza.

La deuda técnica normal se acumula porque las decisiones correctas son costosas. La deuda del vibe coding se acumula porque nadie estaba mirando.

### Pérdida de comprensión

Este es el riesgo menos visible y el más duradero.

Si no puedes explicar por qué una función funciona, no puedes arreglarla cuando falle. Si no puedes arreglarla, dependes del agente para todos los cambios futuros, incluidos los urgentes. Si dependes del agente para todos los cambios, necesitas que el agente tenga suficiente contexto para entender el código que él mismo generó — y aquí entra la degradación de contexto del fichero 05.

El desarrollador que hace vibe coding sistemáticamente pierde la capacidad de debuggear su propio proyecto. Eso no es un problema teórico; es lo que determina si puedes resolver un incidente a las 2 AM o si tienes que esperar a que el agente lo haga.

### Accountability difusa

El código que nadie leyó sigue siendo responsabilidad de quien lo mergeó.

Legalmente, la firma en el commit pertenece al desarrollador. El argumento "lo generó la IA" no es una defensa en una auditoría de seguridad, en un incidente de datos ni en una revisión de compliance. La cuenta del agente no aparece en `git blame`.

### Escalabilidad falsa

Un MVP puede funcionar perfectamente con vibe coding. El problema aparece cuando el MVP tiene usuarios reales y necesita escalar.

Sin arquitectura deliberada, sin separación de concerns, sin patrones consistentes de manejo de errores — lo que funciona a pequeña escala se convierte en un muro cuando intentas añadir caching, migrar bases de datos, o incorporar a nuevos desarrolladores al equipo. La velocidad inicial se paga con interés compuesto.

---

## Por Qué Es Tentador

Reconocer la mecánica de la tentación es útil para gestionarla:

- **Velocidad aparente**: en el corto plazo, no revisar es más rápido que revisar.
- **Dopamina de "funciona"**: ver la demo correr genera una satisfacción real, aunque el código subyacente sea frágil.
- **Reducción de fricción**: la revisión requiere esfuerzo cognitivo. El vibe coding elimina ese esfuerzo — temporalmente.
- **Presión de entrega**: cuando hay un deadline, la revisión parece el coste más fácil de recortar.

Ninguna de estas fuerzas es irracional en el momento. Son racionales a corto plazo e irracionales a largo plazo. Saber que existen ayuda a no ceder a ellas cuando aparecen.

---

## El Posicionamiento Profesional

La diferencia entre un desarrollador que usa IA profesionalmente y uno que hace vibe coding no es la herramienta que usa — es cómo interactúa con el output.

| Aspecto | Developer profesional con IA | Vibe coder |
|---------|------------------------------|------------|
| **Revisión de código** | Lee el diff antes de aceptar cualquier cambio | Acepta sin leer; revisa solo si algo falla |
| **Tests** | Exige tests para el código generado; verifica cobertura | "Funciona en el happy path" |
| **Comprensión** | Puede explicar cada decisión de diseño | "No sé por qué funciona, pero funciona" |
| **Seguridad** | Revisa dependencias, aplica criterios STRIDE, detecta patrones inseguros | No considera seguridad en la revisión |
| **Mantenibilidad** | Pide código limpio, con nombres descriptivos, siguiendo las convenciones del proyecto | Acepta el código en el formato que el agente lo produjo |
| **Accountability** | Firma commits con conocimiento de lo que contienen | Firma commits a ciegas |
| **Deuda técnica** | La gestiona activamente; pide refactors cuando el código se deteriora | No la mide; la descubre cuando ya es un problema |

La distinción no es moralista. Es práctica: el desarrollador profesional puede sostener la velocidad de entrega durante meses. El vibe coder tiene sprints rápidos seguidos de parones para deshacerse de la deuda acumulada — o no se para y el proyecto colapsa.

---

## Cuándo el Vibe Coding Es Aceptable

Hay escenarios legítimos donde el nivel de supervisión puede ser mínimo:

- **Prototipos desechables**: un prototipo cuya función es mostrar una idea y que se va a reescribir desde cero si gusta.
- **Hackathons y exploración personal**: cuando el objetivo es aprender o validar rápidamente y el código no va a ningún lado.
- **Scripts de un solo uso**: automatizaciones personales que se ejecutan una vez y no van a producción.
- **Experimentación local**: código de laboratorio que nunca sale del entorno del desarrollador.

La regla es simple: **si nadie más va a mantenerlo y no va a producción, el riesgo es tuyo**. Eso es una decisión adulta y válida.

En cuanto el código toca producción, datos de usuarios, o el trabajo de otros desarrolladores en el equipo, las reglas cambian.

---

## Guardrails para Evitar Caer en Vibe Coding

La tentación aparece en momentos de presión. Tener reglas pre-comprometidas elimina la necesidad de decidir en esos momentos.

### Regla de los 3 diffs

Nunca aceptes más de 3 cambios consecutivos sin revisar al menos uno en detalle. Si llevas 3 aceptaciones seguidas sin leer el código, para y lee el siguiente diff completo.

### Tests como condición de merge

Si el agente genera código sin tests, la tarea no está terminada. El criterio de "done" incluye tests.

```text
Ejemplo de instrucción en `AGENTS.md`, `CLAUDE.md` o equivalente:
"No generes código de producción sin incluir tests unitarios para los casos
principales. Si no es posible testear algo, explica por qué antes de continuar."
```

### Reset de contexto entre tareas

Cada nueva tarea empieza con contexto limpio. Esto elimina la acumulación silenciosa de contexto que degrada la coherencia del agente (ver fichero 05) y también te fuerza a rearticular qué quieres, lo que activa tu supervisión.

### Revisión del flujo de verificación estándar

Antes de aceptar cualquier cambio en código que va a producción, aplicar el flujo del fichero 01:

1. Compilar o ejecutar el código
2. Revisar el diff completo con `git diff`
3. Ejecutar tests unitarios del área modificada
4. Ejecutar la test suite completa
5. Verificar coherencia con el resto del módulo

### Pair programming con agente

En sesiones largas de generación, un humano supervisa el output mientras el otro dirige al agente. La supervisión explícita de un segundo par de ojos rompe la inercia de aceptación.

---

## Cómo el Vibe Coding Amplifica los 8 Tipos de Fallo

Los 8 tipos de fallo de la taxonomía del fichero 01 existen en cualquier uso de agentes de código. El vibe coding no los genera — los deja pasar sin filtro. Esta tabla muestra la diferencia de probabilidad de detección con y sin supervisión.

| Tipo de fallo | Probabilidad de pasar sin revisión | Por qué |
|---------------|-----------------------------------|---------|
| **Alucinación de API** | Baja — se detecta en ejecución | El código falla al correr |
| **Alucinación de lógica** | Alta | El código "funciona" en el happy path; sin tests, el error no se manifiesta |
| **Regresión silenciosa** | Muy alta | Requiere ejecutar la suite completa; sin esa disciplina, pasa |
| **Over-engineering** | Alta | Solo se detecta leyendo el diff; sin revisión, se acepta |
| **Divergencia de estilo** | Alta | Invisible hasta que alguien tiene que mantener el código |
| **Degradación de contexto** | Media | Puede detectarse si el output falla, pero los errores sutiles no |
| **Sesgo de recencia** | Alta | Requiere conocer las convenciones del proyecto para detectarlo |
| **Confianza excesiva** | Muy alta | El agente afirma que funciona; sin verificación, se acepta |

Con supervisión activa (niveles 2-3 del espectro), la mayoría de estos fallos se interceptan en el flujo de verificación. Con vibe coding, los únicos que se detectan con seguridad son los que hacen que el código no ejecute.

---

## Resumen

El vibe coding es el modo de operación que resulta de maximizar la velocidad a corto plazo a costa de toda supervisión. Es tentador, produce resultados inmediatos visibles, y acumula riesgos que se pagan más tarde.

| Nivel de delegación | Cuándo es apropiado | Supervisión requerida |
|--------------------|--------------------|-----------------------|
| **Nivel 1 — Autocompletado** | Siempre | Inmediata, en el momento |
| **Nivel 2 — Generación asistida** | Trabajo de producción cotidiano | Revisión de diff + compilación + tests unitarios |
| **Nivel 3 — Delegación supervisada** | Tareas bien definidas con buena cobertura de tests | Revisión de resultado + test suite completa |
| **Nivel 4 — Vibe coding** | Prototipos desechables, scripts personales, hackathons | Ninguna obligatoria, riesgo asumido conscientemente |

Trabajar en los niveles 2 y 3 no es trabajar más lento — es trabajar con una velocidad sostenible. Los fallos que no dejas pasar en la revisión son los que no tienes que depurar en producción.
