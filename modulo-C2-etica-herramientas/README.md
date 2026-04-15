# Módulo C2: Ética, Responsabilidad y Panorama de Herramientas

## Descripción General

Usar IA para escribir código plantea preguntas que van más allá de la productividad: ¿de quién es el código generado? ¿Qué datos estás enviando al proveedor? ¿Cumple con las regulaciones de tu sector? Y en un mercado con múltiples herramientas, ¿cuál es la adecuada para tu contexto?

Este módulo te da un marco práctico para responder estas preguntas. No es un curso de derecho ni un análisis exhaustivo del mercado — es lo que un developer profesional necesita saber para tomar decisiones informadas y responsables.

**Tiempo estimado: 2 horas**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Entender** el estado actual de la propiedad intelectual sobre código generado por IA y aplicar una regla práctica para tu trabajo
2. **Evaluar** los riesgos de privacidad según el contexto (open source, startup, enterprise regulada) y aplicar mitigaciones adecuadas
3. **Identificar** los requisitos de compliance relevantes para tu sector (GDPR, SOC 2, PCI-DSS, HIPAA)
4. **Aplicar** prácticas de responsible disclosure cuando el agente encuentra o introduce vulnerabilidades
5. **Comparar** las principales herramientas de coding con IA usando criterios objetivos y seleccionar la adecuada para tu contexto
6. **Distinguir** tendencias claras en el futuro del desarrollo con IA de lo que probablemente no cambiará

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-propiedad-intelectual.md](teoria/01-propiedad-intelectual.md) | Propiedad intelectual y copyright de código generado por IA: estado legal, implicaciones y regla práctica | 15 min |
| 2 | [02-privacidad-datos.md](teoria/02-privacidad-datos.md) | Privacidad y protección de datos: qué envías, riesgos por contexto, mejores prácticas | 15 min |
| 3 | [03-compliance-regulacion.md](teoria/03-compliance-regulacion.md) | Compliance y sectores regulados: GDPR, SOC 2, PCI-DSS, HIPAA y controles recomendados | 15 min |
| 4 | [04-responsible-disclosure.md](teoria/04-responsible-disclosure.md) | Responsible disclosure y seguridad: cuando el agente encuentra o introduce vulnerabilidades | 10 min |
| 5 | [05-comparativa-herramientas.md](teoria/05-comparativa-herramientas.md) | Comparativa objetiva de herramientas: Claude Code, Cursor, Copilot, Windsurf, Cline | 15 min |
| 6 | [06-futuro-desarrollo-ia.md](teoria/06-futuro-desarrollo-ia.md) | El futuro del desarrollo con IA: tendencias claras y lo que no cambiará | 10 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-auditoria-privacidad.md](ejercicios/01-auditoria-privacidad.md) | Auditoría de privacidad: revisar la configuración de tu herramienta de IA | 15 min |
| 2 | [02-analisis-compliance.md](ejercicios/02-analisis-compliance.md) | Análisis de compliance: diseñar política de uso de IA para una fintech | 15 min |
| 3 | [03-comparativa-practica.md](ejercicios/03-comparativa-practica.md) | Comparativa práctica: realizar la misma tarea con 2 herramientas y comparar | 15 min |
| 4 | [04-caso-etico.md](ejercicios/04-caso-etico.md) | Caso ético: la IA genera código que parece copiado de un proyecto GPL | 10 min |

---

## Prerequisitos

- Experiencia usando al menos una herramienta de IA para desarrollo
- Haber completado los módulos A1-A4 y B1-B2 de este curso (recomendado)
- Conocimiento básico de conceptos de seguridad y privacidad en software

---

## Conceptos Clave

- **Propiedad intelectual**: el código generado por IA con tu dirección se trata como obra derivada tuya, pero la responsabilidad final es siempre del developer
- **Privacidad por contexto**: el riesgo varía de bajo (open source) a muy alto (gobierno/defensa), y la mitigación debe corresponderse
- **Compliance**: GDPR, SOC 2, PCI-DSS e HIPAA tienen implicaciones específicas para el uso de IA en desarrollo
- **Responsible disclosure**: protocolo diferente cuando el agente encuentra una vulnerabilidad vs. cuando la introduce
- **No hay herramienta perfecta**: cada herramienta tiene fortalezas y debilidades; la elección depende de tu contexto
- **Lo que no cambia**: pensamiento crítico, buenas specs, tests, y la responsabilidad del developer sobre su código

---

## Flujo de Trabajo Recomendado

1. **Lee la teoría en orden**: los temas legales (01-03) construyen un marco, la seguridad (04) lo complementa, y las herramientas (05-06) dan perspectiva
2. **Evalúa tu situación actual**: ¿en qué contexto trabajas (open source, startup, enterprise)? Los riesgos son muy diferentes
3. **Haz la auditoría de privacidad**: el ejercicio 01 te deja con un diagnóstico accionable de tu configuración actual
4. **Compara herramientas con datos**: el ejercicio 03 te da una comparativa basada en tu experiencia, no en marketing

---

## Relación con el Curso de Claude Code

Este módulo complementa especialmente:

- [M11 - Enterprise y Seguridad](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-11-enterprise-seguridad/README.md): configuración enterprise, políticas organizacionales, deployment on-premise
- [M05 - Configuración y Permisos](https://github.com/josefcohernandez/claude-code-course/blob/master/curso/modulo-05-configuracion-permisos/README.md): jerarquía de permisos allow/deny que implementa las políticas de este módulo

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo C1: Adopción en Equipos y Métricas](../modulo-C1-adopcion-equipos/README.md) |
| **Siguiente** | [Módulo D1: Arquitectura de Software Orientada a IA](../modulo-D1-arquitectura-para-ia/README.md) |
