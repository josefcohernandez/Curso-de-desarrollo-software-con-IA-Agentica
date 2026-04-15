# Curso de Desarrollo Profesional con IA Agéntica

> **Domina el oficio de desarrollar software con agentes de código**

## Acerca de este curso

Este curso enseña las habilidades, el pensamiento crítico y las metodologías que necesitas para trabajar de forma profesional y efectiva con agentes de código IA. No se centra en una herramienta específica — los conceptos son transferibles entre Claude Code, Cursor, Copilot, Windsurf, Cline o cualquier herramienta de coding con IA.

Mientras que un curso de herramienta enseña *qué botones pulsar*, este curso enseña **cómo pensar**: escribir prompts efectivos, detectar errores del agente, revisar código generado por IA, debuggear de forma sistemática, y adoptar IA en equipos de desarrollo.

### Público objetivo

- Desarrolladores que ya usan Claude Code u otra herramienta de coding con IA y quieren ser más efectivos
- Usuarios de Cursor, Copilot, Windsurf o Cline que quieren mejorar su forma de trabajar
- Equipos evaluando qué herramienta adoptar y cómo integrarla en su workflow
- Tech leads y managers que necesitan entender la disciplina sin dominar una herramienta específica
- Cualquier desarrollador que quiera prepararse para el futuro del desarrollo de software

### Requisitos previos

- Experiencia en programación (cualquier lenguaje)
- Haber usado al menos una herramienta de coding con IA (no necesitas ser experto)
- Familiaridad con conceptos básicos de desarrollo: tests, control de versiones, code review

**Recomendado**: si usas Claude Code, haber completado los módulos M01-M06 del [Curso de Claude Code](https://github.com/josefcohernandez/claude-code-course) o tener experiencia equivalente.

---

## Estructura del curso

El curso está organizado en **4 bloques progresivos** con **12 módulos** + proyecto final:

### Bloque A: El Oficio (Módulos A1-A4)

| Módulo | Título | Tiempo | Descripción |
|--------|--------|--------|-------------|
| A1 | Prompting Efectivo para Agentes de Código | 2h | Anatomía de un buen prompt, patrones por tipo de tarea, cookbook, ejercicios comparativos |
| A2 | Limitaciones, Fallos y Pensamiento Crítico | 2h | Alucinaciones, regresiones, over-engineering, degradación por contexto, cuándo NO usar IA |
| A3 | Revisión de Código Generado por IA | 2h | Qué buscar, red flags, patrones de diff review, deuda técnica silenciosa |
| A4 | Debugging Sistemático con IA | 2h | Workflow reproducir-aislar-diagnosticar-verificar, logs, cross-stack, post-mortems |

### Bloque B: Escenarios Reales (Módulos B1-B2)

| Módulo | Título | Tiempo | Descripción |
|--------|--------|--------|-------------|
| B1 | El Día a Día — Escenarios End-to-End | 2.5h | Onboarding, incidente en producción, proyecto greenfield, legacy code, día típico |
| B2 | Trabajar con Diferentes Stacks | 2.5h | Frontend, backend, DevOps/IaC, data, mobile — patrones por dominio |

### Bloque C: Equipo y Organización (Módulos C1-C2)

| Módulo | Título | Tiempo | Descripción |
|--------|--------|--------|-------------|
| C1 | Adopción en Equipos y Métricas | 2h | Fases de adopción, resistencia al cambio, convenciones de equipo, ROI |
| C2 | Ética, Responsabilidad y Panorama de Herramientas | 2h | IP, privacidad, compliance, comparativa de herramientas, futuro del desarrollo |

### Bloque D: Nivel Experto (Módulos D1-D4)

| Módulo | Título | Tiempo | Descripción |
|--------|--------|--------|-------------|
| D1 | Arquitectura de Software Orientada a IA | 2h | AI-readability, patrones modulares, documentación como contrato, anti-patrones |
| D2 | Orquestación Multi-Agente y Automatización a Escala | 2h | Patrones de coordinación, prompts para CI/CD, batch processing, testing de prompts |
| D3 | Testing Avanzado y AI Pair Programming | 2h | Property-based testing, mutation testing, visual regression, flow state |
| D4 | Seguridad, Costes y Optimización | 2h | Threat modeling, security review profundo, token budgeting, benchmarking |

### Proyecto Final

| Módulo | Título | Tiempo | Descripción |
|--------|--------|--------|-------------|
| P | Caso Práctico Integrador | 4-6h | Sprint simulado de 3 días aplicando todas las técnicas del curso |

**Tiempo total estimado: 29-31 horas**

---

## Ruta de aprendizaje

```text
BLOQUE A: EL OFICIO
┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐
│   A1    │──▶│   A2    │──▶│   A3    │──▶│   A4    │
│Prompting│   │Limitac. │   │ Code    │   │Debugging│
│         │   │y fallos │   │ Review  │   │ con IA  │
└─────────┘   └─────────┘   └─────────┘   └─────────┘
                                                │
BLOQUE B: ESCENARIOS REALES                     ▼
                              ┌─────────┐   ┌─────────┐
                              │   B2    │◀──│   B1    │
                              │ Stacks  │   │Escenario│
                              │         │   │End-to-E │
                              └─────────┘   └─────────┘
                                  │
BLOQUE C: EQUIPO Y ORGANIZACIÓN  ▼
                              ┌─────────┐   ┌─────────┐
                              │   C1    │──▶│   C2    │
                              │Adopción │   │ Ética y │
                              │equipos  │   │herramie.│
                              └─────────┘   └─────────┘
                                  │
BLOQUE D: NIVEL EXPERTO          ▼
┌─────────┐   ┌─────────┐   ┌─────────┐   ┌─────────┐
│   D1    │──▶│   D2    │──▶│   D3    │──▶│   D4    │
│Arquitec.│   │Orquesta.│   │Testing +│   │Segurida│
│para IA  │   │multi-ag.│   │Pair Prog│   │+ Costes│
└─────────┘   └─────────┘   └─────────┘   └─────────┘
                                                │
PROYECTO FINAL                                  ▼
                              ┌──────────────────────┐
                              │   PROYECTO FINAL     │
                              │  Sprint simulado     │
                              │  de 3 días           │
                              └──────────────────────┘
```

---

## Cómo usar este curso

### Como formación guiada

Sigue los bloques en orden (A → B → C → D → Proyecto). Cada módulo construye sobre los anteriores. Dedica al menos 1 semana al Bloque A — es la base de todo lo demás. El Bloque D (Experto) es opcional si no necesitas nivel avanzado.

### Como referencia rápida

- **"¿Cómo escribo un buen prompt?"** → Módulo A1
- **"El agente genera código malo, ¿qué hago?"** → Módulo A2
- **"¿Cómo reviso un PR generado por IA?"** → Módulo A3
- **"¿Cómo debuggeo con IA?"** → Módulo A4
- **"¿Cómo introduzco IA en mi equipo?"** → Módulo C1
- **"¿Qué herramienta de IA elijo?"** → Módulo C2
- **"¿Cómo diseño software para que la IA trabaje mejor?"** → Módulo D1
- **"¿Cómo coordino múltiples agentes?"** → Módulo D2
- **"¿Cómo hago testing avanzado con IA?"** → Módulo D3
- **"¿Cómo gestiono costes y seguridad?"** → Módulo D4

### En paralelo con el Curso de Claude Code

Si estás haciendo el [Curso de Claude Code](https://github.com/josefcohernandez/claude-code-course), la combinación recomendada es:

| Fase | Curso de Claude Code | Este curso |
|------|---------------------|------------|
| Semana 1-2 | M01-M06 (Fundamentos + Intermedio) | — |
| Semana 3-4 | M07-M10 (Avanzado) | A1-A2 (Prompting + Limitaciones) |
| Semana 5-6 | M11-M15 (Experto) | A3-A4 (Code Review + Debugging) |
| Semana 7 | — | B1-B2 (Escenarios + Stacks) |
| Semana 8 | — | C1-C2 (Equipos + Ética) |
| Semana 9-10 | — | D1-D4 (Nivel Experto) |
| Semana 11-12 | M16 (Proyecto Final) | Proyecto (combinable) |

---

## Interrelación con el Curso de Claude Code

Este curso es **independiente pero complementario** al [Curso de Desarrollo Asistido con Claude Code](https://github.com/josefcohernandez/claude-code-course).

```text
Curso de Claude Code                     Este curso
━━━━━━━━━━━━━━━━━━━                      ━━━━━━━━━━

M01 Introducción ◄──────────────────────► A1 Prompting
M03 Contexto     ◄──────────────────────► A2 Limitaciones
M09 Agentes      ◄──────────────────────► B1 Escenarios
M11 Enterprise   ◄──────────────────────► C1 Adopción equipos
M12 Metodologías ◄──────────────────────► A3 Code Review
                 ◄──────────────────────► A4 Debugging
M04 Memoria      ◄──────────────────────► D1 Arquitectura para IA
M09 Agentes      ◄──────────────────────► D2 Orquestación
M10 CI/CD        ◄──────────────────────► D2 Orquestación
M12 Metodologías ◄──────────────────────► D3 Testing avanzado
M11 Enterprise   ◄──────────────────────► D4 Seguridad y costes
M03 Contexto     ◄──────────────────────► D4 Seguridad y costes
M16 Proyecto     ◄──────────────────────► Proyecto
```

- El **Curso de Claude Code** enseña a dominar la herramienta: CLI, configuración, MCP, hooks, subagentes, CI/CD
- **Este curso** enseña el oficio: cómo pensar, cómo detectar errores, cómo revisar, cómo adoptar en equipos
- Los conceptos de este curso son **transferibles** a cualquier herramienta de coding con IA
- Los ejemplos usan Claude Code como referencia principal, con notas sobre equivalencias en otras herramientas

---

## Estructura de carpetas

```text
Curso-Desarrollo-Profesional-con-IA-Agentica/
├── README.md                              # Este archivo
├── modulo-A1-prompting-efectivo/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-prompt-como-interfaz.md
│   │   ├── 02-anatomia-buen-prompt.md
│   │   ├── 03-patrones-por-tarea.md
│   │   ├── 04-cookbook-antes-despues.md
│   │   ├── 05-contexto-vs-exploracion.md
│   │   └── 06-iteracion-correccion.md
│   └── ejercicios/
│       ├── 01-comparativa-prompts.md
│       ├── 02-prompt-por-patron.md
│       ├── 03-rescue-prompt-fallido.md
│       └── 04-cookbook-personal.md
├── modulo-A2-limitaciones-fallos/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-taxonomia-fallos.md
│   │   ├── 02-alucinaciones-codigo.md
│   │   ├── 03-regresiones-silenciosas.md
│   │   ├── 04-over-engineering.md
│   │   ├── 05-degradacion-contexto.md
│   │   └── 06-cuando-no-usar-ia.md
│   └── ejercicios/
│       ├── 01-caza-alucinaciones.md
│       ├── 02-deteccion-regresiones.md
│       ├── 03-poda-over-engineering.md
│       ├── 04-simulacion-degradacion.md
│       └── 05-decision-ia-si-no.md
├── modulo-A3-revision-codigo-ia/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-por-que-es-diferente.md
│   │   ├── 02-que-buscar.md
│   │   ├── 03-red-flags-codigo-ia.md
│   │   ├── 04-patrones-diff-review.md
│   │   └── 05-deuda-tecnica-silenciosa.md
│   └── ejercicios/
│       ├── 01-review-pr-ia.md
│       ├── 02-encontrar-regresion.md
│       ├── 03-poda-complejidad.md
│       └── 04-red-flags-tests.md
├── modulo-A4-debugging-sistematico/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-debugging-amplificado.md
│   │   ├── 02-workflow-debugging.md
│   │   ├── 03-analisis-logs.md
│   │   ├── 04-debugging-cross-stack.md
│   │   └── 05-post-mortems.md
│   └── ejercicios/
│       ├── 01-bug-con-stack-trace.md
│       ├── 02-bug-sin-stack-trace.md
│       ├── 03-analisis-logs.md
│       ├── 04-debugging-cross-stack.md
│       └── 05-post-mortem.md
├── modulo-B1-escenarios-end-to-end/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-onboarding-codebase.md
│   │   ├── 02-incidente-produccion.md
│   │   ├── 03-proyecto-greenfield.md
│   │   ├── 04-codigo-legacy.md
│   │   └── 05-dia-tipico.md
│   └── ejercicios/
│       ├── 01-onboarding-simulado.md
│       ├── 02-incidente-simulado.md
│       ├── 03-greenfield.md
│       └── 04-legacy-rescue.md
├── modulo-B2-diferentes-stacks/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-frontend-react-vue.md
│   │   ├── 02-backend-node-python-go.md
│   │   ├── 03-devops-iac.md
│   │   ├── 04-data-sql-pandas.md
│   │   └── 05-mobile.md
│   └── ejercicios/
│       ├── 01-frontend-desde-mockup.md
│       ├── 02-backend-crud-api.md
│       ├── 03-dockerfile-optimizado.md
│       ├── 04-analisis-dataset.md
│       └── 05-feature-fullstack.md
├── modulo-C1-adopcion-equipos/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-fases-adopcion.md
│   │   ├── 02-resistencia-cambio.md
│   │   ├── 03-convenciones-equipo.md
│   │   ├── 04-metricas-productividad.md
│   │   └── 05-roi-management.md
│   └── ejercicios/
│       ├── 01-claude-md-equipo.md
│       ├── 02-plan-adopcion.md
│       ├── 03-calculo-roi.md
│       └── 04-gestionar-resistencia.md
├── modulo-C2-etica-herramientas/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-propiedad-intelectual.md
│   │   ├── 02-privacidad-datos.md
│   │   ├── 03-compliance-regulacion.md
│   │   ├── 04-responsible-disclosure.md
│   │   ├── 05-comparativa-herramientas.md
│   │   └── 06-futuro-desarrollo-ia.md
│   └── ejercicios/
│       ├── 01-auditoria-privacidad.md
│       ├── 02-analisis-compliance.md
│       ├── 03-comparativa-practica.md
│       └── 04-caso-etico.md
├── modulo-D1-arquitectura-para-ia/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-ai-readability.md
│   │   ├── 02-patrones-modulares.md
│   │   ├── 03-anti-patrones-arquitectonicos.md
│   │   └── 04-documentacion-como-contrato.md
│   └── ejercicios/
│       ├── 01-evaluar-ai-readability.md
│       ├── 02-refactorizar-para-ia.md
│       └── 03-documentar-para-ia.md
├── modulo-D2-orquestacion-automatizacion/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-patrones-orquestacion.md
│   │   ├── 02-prompts-para-cicd.md
│   │   ├── 03-batch-processing.md
│   │   ├── 04-testing-de-prompts.md
│   │   └── 05-gestion-fallos-desatendidos.md
│   └── ejercicios/
│       ├── 01-pipeline-multi-agente.md
│       ├── 02-prompt-para-cicd.md
│       ├── 03-migracion-batch.md
│       └── 04-test-suite-prompts.md
├── modulo-D3-testing-pair-programming/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-testing-generativo.md
│   │   ├── 02-mutation-testing.md
│   │   ├── 03-visual-regression.md
│   │   ├── 04-pair-programming-ia.md
│   │   └── 05-flow-state-productividad.md
│   └── ejercicios/
│       ├── 01-property-based-testing.md
│       ├── 02-mutation-testing.md
│       └── 03-sesion-pair-programming.md
├── modulo-D4-seguridad-costes/
│   ├── README.md
│   ├── teoria/
│   │   ├── 01-threat-modeling.md
│   │   ├── 02-security-review-profundo.md
│   │   ├── 03-token-budgeting.md
│   │   ├── 04-estrategias-modelo.md
│   │   └── 05-benchmarking-productividad.md
│   └── ejercicios/
│       ├── 01-threat-model-proyecto.md
│       ├── 02-audit-seguridad-profundo.md
│       ├── 03-optimizar-costes.md
│       └── 04-benchmark-equipo.md
├── proyecto-final/
│   ├── README.md
│   ├── enunciado/
│   │   └── README.md
│   └── criterios-evaluacion/
│       └── rubrica.md
└── recursos/
    ├── prompt-templates/
    │   └── templates-reutilizables.md
    └── checklists/
        └── checklist-revision-codigo-ia.md
```

---

## Recursos oficiales

- **Documentación de Claude Code**: https://code.claude.com/docs
- **Curso complementario de Claude Code**: https://github.com/josefcohernandez/claude-code-course
- **OWASP Top 10**: https://owasp.org/www-project-top-ten/
- **Conventional Commits**: https://www.conventionalcommits.org/
- **GDPR para desarrolladores**: https://gdpr.eu/

---

## Versiones

| Versión | Fecha | Cambios principales |
|---------|-------|---------------------|
| 1.0 | Abril 2026 | Versión inicial — 12 módulos + proyecto en 4 bloques (A: El Oficio, B: Escenarios, C: Equipo, D: Experto) |

---

## Licencia

[![CC BY-NC-SA 4.0](https://licensebuttons.net/l/by-nc-sa/4.0/88x31.png)](https://creativecommons.org/licenses/by-nc-sa/4.0/)

Este curso está licenciado bajo [Creative Commons Attribution-NonCommercial-ShareAlike 4.0 International](https://creativecommons.org/licenses/by-nc-sa/4.0/).

- **Puedes**: compartir, adaptar y crear material derivado para fines no comerciales
- **Debes**: dar crédito, indicar cambios, usar la misma licencia
- **No puedes**: uso comercial sin autorización

---

> **Consejo**: Este curso te enseña a *pensar* con IA, no solo a *usar* IA. La herramienta cambiará; el oficio permanecerá.
