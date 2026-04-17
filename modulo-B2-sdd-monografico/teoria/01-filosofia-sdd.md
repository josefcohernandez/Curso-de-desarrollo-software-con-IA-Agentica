# 01 — Filosofía y Fundamentos de SDD

## Por qué las especificaciones importan más con IA que sin ella

Cuando un desarrollador humano recibe una tarea ambigua, usa intuición, experiencia del dominio, y conversaciones informales con el equipo para rellenar los huecos. No lo hace explícitamente — lo hace de forma inconsciente en tiempo real.

Un agente de IA no tiene esa intuición. Cuando encuentra ambigüedad, **toma una decisión basada en su training data**, no en el contexto de tu proyecto. El resultado es código que técnicamente funciona pero resuelve el problema incorrecto, o que resuelve el problema correcto pero con supuestos implícitos que chocarán con la realidad en producción.

La especificación elimina esa ambigüedad antes de que el agente tome decisiones por ti.

---

## La analogía del plano de arquitectura

Un arquitecto no empieza a construir sin planos. El contratista necesita un documento que defina exactamente qué se va a construir antes de poner el primer ladrillo. Sin planos:

- El contratista toma decisiones de diseño que no le corresponden
- Los cambios durante la construcción cuestan diez veces más que en papel
- El resultado final puede cumplir los requisitos técnicos pero no la visión del cliente

El agente de IA es el contratista. La spec es el plano. Tú eres el arquitecto.

---

## Specs vs. prompts: una distinción crítica

Es tentador pensar que un prompt muy detallado es equivalente a una spec. No lo es.

| Dimensión | Prompt largo | Spec (SPEC.md) |
|-----------|-------------|----------------|
| Persistencia | Desaparece al limpiar el contexto | Artefacto permanente en el repositorio |
| Verificabilidad | No se puede comprobar sistemáticamente | Cada requisito es verificable |
| Compartibilidad | Solo en esa sesión | Disponible para cualquier sesión, herramienta, o miembro del equipo |
| Evolución | Se reescribe cada vez | Versión controlada con git |
| Estructura | Libre, sin garantías | Secciones definidas con criterios claros |
| Fuente de verdad | No — el agente lo interpreta como contexto | Sí — es la referencia definitiva del sistema |

Un prompt es una instrucción transitoria. Una spec es un contrato.

---

## La curva de coste del cambio

El coste de cambiar algo en software sigue una curva exponencial: cambiar un requisito en el papel es barato, cambiarlo durante el diseño tiene un coste moderado, cambiarlo durante la implementación es caro, y cambiarlo en producción es muy caro.

Con IA, esta curva se aplica de forma aún más pronunciada porque el agente puede generar código a una velocidad que supera la capacidad de verificación humana. Sin spec, en 2 horas puedes tener 500 líneas de código que resuelven el problema incorrecto. Con spec, esas 2 horas producen código que resuelve exactamente lo que necesitas.

```text
Sin SDD:
Coste de cambio
    |                                         ████
    |                              ████████████
    |                  ████████████
    |       ████████████
    |████████
    |_______________________________________
    Idea  Diseño  Impl.  Tests  Producción

Con SDD:
Coste de cambio
    |
    |     ██
    |     ██                                ██
    | ████████  ██   ██   ██   ██   ██████
    |███████████████████████████████████████
    |_______________________________________
    Idea  Diseño  Impl.  Tests  Producción
```

Con SDD hay una inversión inicial en la fase de especificación, pero el coste de cambio se mantiene controlado durante toda la vida del proyecto.

---

## El ROI de la entrevista de requisitos

Los 10-15 minutos que dura una entrevista de requisitos bien conducida ahorran en promedio 2 horas de retrabajo. La razón es que la entrevista descubre dos categorías de problemas que el desarrollador no anticipa:

**1. Edge cases no considerados**: el agente hace preguntas sistemáticas sobre escenarios límite que el desarrollador asume que "no van a pasar" — y que pasan exactamente en producción.

**2. Requisitos implícitos**: necesidades que el desarrollador da por sentadas pero que no ha verbalizado, y que el agente no puede inferir sin contexto explícito.

Un ejemplo real: en una entrevista para un sistema de notificaciones push, el agente pregunta "¿qué ocurre si el usuario desinstala la app antes de recibir la notificación?" El desarrollador no lo había considerado. La respuesta define el comportamiento de eliminación de tokens — un detalle que, sin la entrevista, habría producido errores silenciosos en producción semanas después del lanzamiento.

---

## Cuándo usar SDD — y cuándo no

SDD es una inversión. Como toda inversión, tiene un umbral de rentabilidad.

**Usar SDD cuando:**
- El proyecto tiene 2 o más archivos relacionados
- La feature tarda más de 30 minutos en implementarse
- El código va a ser mantenido más de una semana
- El agente va a tomar decisiones de diseño (no solo "añade este endpoint")
- Trabajas en equipo y otros desarrolladores van a trabajar sobre el mismo código

**No usar SDD cuando:**
- Bug fix puntual en una función de 10 líneas
- Script que se ejecuta una vez y se descarta
- Prototipo desechable para validar una idea
- Cambio cosmético (renombrar variable, ajustar CSS de un color)
- Pregunta de exploración ("¿cómo funciona esta parte del código?")

La señal práctica: si puedes describir la tarea completa en una sola oración sin ambigüedad, no necesitas SDD. Si al intentar describirla en una oración te das cuenta de que hay decisiones que tomar, SDD te va a ahorrar tiempo.

---

## SDD vs. No-SDD: comparativa de resultados

| Métrica | Sin SDD | Con SDD |
|---------|---------|---------|
| Tiempo hasta primer código funcional | Rápido (30 min) | Más lento (45 min con entrevista) |
| Requisitos implementados correctamente en primer intento | ~60% | ~90% |
| Horas de retrabajo por malentendidos | 2-4 horas | 0-1 hora |
| Cobertura de edge cases en el diseño inicial | Baja | Alta |
| Capacidad de onboarding de otro desarrollador | Difícil (el contexto está en la sesión del agente) | Fácil (SPEC.md es la fuente de verdad) |
| Verificabilidad del resultado final | Subjetiva ("¿funciona como quería?") | Objetiva (lista de requisitos verificados) |

El coste inicial de 15 minutos de entrevista y 10 minutos de redacción de spec se recupera en el primer ciclo de implementación, y genera valor compuesto: la spec sirve como documentación, como base para tests, y como referencia para futuras modificaciones.

---

## Las cuatro fases de SDD

SDD se articula en cuatro fases secuenciales:

```text
Fase 1: ENTREVISTA
El agente entrevista al desarrollador para descubrir
requisitos, edge cases y restricciones.
Resultado: lista de decisiones tomadas

Fase 2: ESPECIFICACIÓN
El agente genera SPEC.md con toda la información
recopilada estructurada en secciones verificables.
Resultado: SPEC.md en el repositorio

Fase 3: IMPLEMENTACIÓN
Sesión nueva. El agente implementa fase por fase
siguiendo la spec como única fuente de verdad.
Resultado: código + tests funcionando

Fase 4: VERIFICACIÓN
Sesión nueva. El agente compara la implementación
contra cada requisito de la spec.
Resultado: VERIFICACION.md con clasificación completa
```

Los siguientes cuatro ficheros profundizan en cada fase con técnicas, ejemplos y anti-patrones.

---

> **Nota sobre herramientas**: los conceptos de SDD son independientes de la herramienta que uses. Claude Code, Cursor, GitHub Copilot Chat y Windsurf pueden ejecutar las cuatro fases. La diferencia está en cómo gestionas el contexto entre fases: unas herramientas ofrecen comandos de limpieza de contexto y otras se apoyan en abrir una sesión nueva.
