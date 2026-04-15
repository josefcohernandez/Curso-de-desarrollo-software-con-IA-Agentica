# Fases de Adopción de IA en Equipos de Desarrollo

## Introducción

Introducir IA agéntica en un equipo de desarrollo no es un evento puntual — es un **proceso gradual** que pasa por fases predecibles. Entender estas fases te permite planificar la adopción, anticipar problemas y medir el progreso de forma realista.

El error más común es intentar que todo el equipo use la herramienta desde el día 1. Esto genera frustración, resistencia y una percepción negativa que es difícil de revertir. La adopción exitosa es **orgánica y progresiva**.

---

## Las 4 Fases de Adopción

| Fase | Descripción | Duración típica | Señales de avance |
|------|-------------|-----------------|-------------------|
| **1. Exploración** | 1-2 early adopters prueban la herramienta por iniciativa propia | 1-2 semanas | Los primeros fans comparten tips y resultados |
| **2. Estandarización** | El equipo acuerda convenciones (CLAUDE.md, skills, permisos) | 2-4 semanas | CLAUDE.md en el repo, reglas de permisos configuradas |
| **3. Integración** | La IA se incorpora al workflow diario (PRs, code review, debugging) | 1-3 meses | >50% del equipo usa IA diariamente |
| **4. Optimización** | Métricas, skills personalizados, CI/CD con IA | Continua | ROI medible, reducción de cycle time |

---

## Fase 1: Exploración

En esta fase, uno o dos miembros del equipo — los **early adopters** — empiezan a usar la herramienta por curiosidad o necesidad. No hay convenciones ni proceso formal.

### Qué hacer

- Identificar a los early adopters naturales (suelen ser los que ya experimentan con nuevas herramientas)
- Darles tiempo y espacio para experimentar sin presión de resultados
- Pedirles que documenten informalmente qué les funciona y qué no

### Qué NO hacer

- Forzar a nadie a participar
- Pedir métricas de productividad en esta fase
- Esperar resultados inmediatos

### Señales de que la fase está funcionando

- Los early adopters empiezan a compartir tips espontáneamente
- Otros miembros del equipo preguntan "¿qué herramienta estás usando?"
- Se resuelve alguna tarea difícil o tediosa de forma visiblemente más rápida

---

## Fase 2: Estandarización

Cuando hay suficiente interés (no tiene que ser todo el equipo), se formalizan las convenciones. El objetivo es que todos los que usen la herramienta trabajen de forma **consistente**.

### Qué acordar

1. **CLAUDE.md compartido**: reglas de estilo, comandos de build/test, arquitectura
2. **Skills del equipo**: workflows repetibles (review, deploy, test)
3. **Reglas de permisos**: qué puede hacer el agente sin pedir permiso
4. **Política de code review**: el código IA se revisa igual que el código humano
5. **Política de commits**: cómo se atribuyen los cambios generados por IA
6. **Límites explícitos**: qué tareas NO se delegan a IA

> Ver [03-convenciones-equipo.md](03-convenciones-equipo.md) para el detalle completo y template.

### Entregable de esta fase

Un CLAUDE.md en el repositorio del proyecto y un documento breve (puede ser en el wiki o en el README) con las convenciones acordadas.

---

## Fase 3: Integración

La herramienta se convierte en parte del workflow diario. No es algo que se usa "a veces" — es una herramienta más como el IDE, Git o el terminal.

### Indicadores de integración exitosa

- Los PRs incluyen de forma natural código generado con asistencia de IA
- El code review aplica los criterios del [Módulo A3](../../modulo-A3-revision-codigo-ia/README.md) sin necesidad de recordarlo
- Los developers usan la IA para debugging, exploración y tareas repetitivas
- Los nuevos miembros del equipo reciben el CLAUDE.md como parte del onboarding

### Riesgo en esta fase

La **dependencia excesiva**: developers que aceptan todo lo que genera el agente sin revisarlo. Mitigación: mantener la cultura de code review y las técnicas del [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md).

---

## Fase 4: Optimización

Con el equipo usando la herramienta de forma integrada, el foco cambia a **medir y mejorar**.

### Actividades de optimización

- Implementar métricas de productividad (ver [04-metricas-productividad.md](04-metricas-productividad.md))
- Crear skills personalizados para workflows frecuentes del equipo
- Integrar la IA en CI/CD (hooks, validación automática, generación de changelogs)
- Calcular y presentar ROI (ver [05-roi-management.md](05-roi-management.md))
- Compartir aprendizajes con otros equipos de la organización

---

## Errores Comunes en la Adopción

| Error | Consecuencia | Alternativa |
|-------|-------------|-------------|
| Forzar adopción desde día 1 | Resistencia activa, mala experiencia | Empezar con early adopters voluntarios |
| No invertir en CLAUDE.md | Cada dev configura diferente, resultados inconsistentes | Dedicar una sesión a crear convenciones |
| Esperar que la IA reemplace procesos | Frustración cuando no es así | La IA augmenta procesos, no los reemplaza |
| No medir impacto | Imposible justificar la inversión | Definir métricas desde la fase 2 |
| Ignorar la resistencia | Se convierte en conflicto | Abordar con empatía (ver [02-resistencia-cambio.md](02-resistencia-cambio.md)) |

---

## Timeline Orientativo

```text
Semana 1-2:  [Fase 1] Early adopters experimentan
Semana 3-6:  [Fase 2] Convenciones y CLAUDE.md
Semana 7-14: [Fase 3] Integración en workflow diario
Semana 15+:  [Fase 4] Métricas y optimización continua
```

Este timeline es orientativo. Equipos pequeños (3-5 personas) pueden completar las fases 1-3 en 4-6 semanas. Equipos grandes (20+) pueden necesitar 3-6 meses.

---

## Relación con Otros Módulos

- **Módulo A1** (Prompting): la calidad de los prompts determina la experiencia inicial de los early adopters
- **Módulo A2** (Limitaciones): conocer las limitaciones previene frustraciones tempranas
- **Módulo C2** (Ética): las consideraciones de privacidad y compliance afectan qué herramienta elegir

---

## Navegación

| | |
|---|---|
| **Siguiente** | [02-resistencia-cambio.md](02-resistencia-cambio.md) |
| **Módulo** | [README del módulo](../README.md) |
