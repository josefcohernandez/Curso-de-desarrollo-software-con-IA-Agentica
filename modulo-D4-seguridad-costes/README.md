# Módulo D4: Seguridad, Costes y Optimización

## Descripción General

Desarrollar con IA agéntica no solo amplifica tu productividad -- también amplifica los riesgos de seguridad y los costes. Un agente puede generar código vulnerable tan rápido como código seguro, y una sesión mal gestionada puede consumir presupuesto sin producir valor. Este módulo te enseña a integrar seguridad por diseño en tu flujo de trabajo con IA, a controlar costes con estrategias de token budgeting, y a medir la productividad real de tu equipo para justificar (o ajustar) la inversión en herramientas de IA.

**Tiempo estimado: 2 horas**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Realizar** un threat model STRIDE asistido por IA, identificando amenazas sistemáticamente en cualquier arquitectura
2. **Ejecutar** security code reviews profundos que van más allá del checklist genérico de OWASP, con prompts específicos por stack tecnológico
3. **Calcular** el coste real de tokens (input, output, cached) y aplicar 6 estrategias de optimización para reducir el gasto sin sacrificar calidad
4. **Seleccionar** el modelo correcto para cada tarea usando un framework de decisión de 5 preguntas
5. **Configurar** effort levels y fallback models para maximizar la relación calidad/coste
6. **Diseñar** un benchmark de productividad para tu equipo con métricas personales y de equipo
7. **Medir** el ROI real de la adopción de IA agéntica en desarrollo de software

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-threat-modeling.md](teoria/01-threat-modeling.md) | Threat modeling asistido por IA: STRIDE, análisis de dependencias, attack surface mapping | 15 min |
| 2 | [02-security-review-profundo.md](teoria/02-security-review-profundo.md) | Security code review profundo: más allá de OWASP, vulnerabilidades por stack, prompt template | 15 min |
| 3 | [03-token-budgeting.md](teoria/03-token-budgeting.md) | Token budgeting y gestión de costes: costes reales, 6 estrategias de optimización, presupuesto por tarea | 20 min |
| 4 | [04-estrategias-modelo.md](teoria/04-estrategias-modelo.md) | Estrategias de selección de modelo: framework de 5 preguntas, fallback-model, effort levels | 15 min |
| 5 | [05-benchmarking-productividad.md](teoria/05-benchmarking-productividad.md) | Benchmarking de productividad: métricas personales y de equipo, proceso de 5 pasos | 15 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-threat-model-proyecto.md](ejercicios/01-threat-model-proyecto.md) | Threat model STRIDE de una API de e-commerce | 15 min |
| 2 | [02-audit-seguridad-profundo.md](ejercicios/02-audit-seguridad-profundo.md) | Security review profundo de un módulo de autenticación: encontrar 3+ vulnerabilidades | 15 min |
| 3 | [03-optimizar-costes.md](ejercicios/03-optimizar-costes.md) | Analizar historial de uso, calcular costes, proponer plan de reducción del 30% | 15 min |
| 4 | [04-benchmark-equipo.md](ejercicios/04-benchmark-equipo.md) | Diseñar benchmark de productividad para un equipo de 5 personas | 15 min |

---

## Prerequisitos

- Experiencia con desarrollo web (al menos un stack: Node, Python, Java o similar)
- Conocimientos básicos de seguridad web (OWASP Top 10, autenticación, autorización)
- Haber completado los módulos D1-D3 del curso, o tener experiencia equivalente con agentes de código
- **Recomendado**: experiencia con al menos un proveedor de IA (Claude, GPT, etc.) y sus modelos de precios

---

## Conceptos Clave

- **STRIDE**: framework de threat modeling (Spoofing, Tampering, Repudiation, Information Disclosure, Denial of Service, Elevation of Privilege)
- **Attack surface mapping**: identificación sistemática de todos los puntos de entrada de un sistema
- **Token budgeting**: gestión consciente del consumo de tokens para controlar costes sin perder productividad
- **Cached tokens**: tokens que se repiten entre llamadas (CLAUDE.md, system prompt) y tienen descuento del ~90%
- **Effort levels**: niveles de razonamiento del modelo (low, medium, high, max) que afectan calidad, velocidad y coste
- **Fallback model**: modelo de respaldo cuando el principal no está disponible, para degradación elegante
- **Mutation score vs coverage**: el mutation score mide calidad real de tests; aquí aplicamos el mismo principio a security reviews

---

## Flujo de Trabajo Recomendado

1. **Seguridad primero**: lee las teorías 01 y 02 antes de las de costes -- la seguridad no se optimiza, se garantiza
2. **Haz el threat model** (ejercicio 01) inmediatamente después de leer la teoría 01 -- se aprende haciendo
3. **Calcula tus costes reales**: antes de optimizar, necesitas saber cuánto gastas (teoría 03)
4. **Experimenta con effort levels**: prueba el mismo prompt con low, medium y high para ver la diferencia real
5. **Diseña tu benchmark** (ejercicio 04) como entregable final: es la herramienta que usarás para medir mejora continua

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo D3: Testing Avanzado y AI Pair Programming](../modulo-D3-testing-pair-programming/README.md) |
| **Siguiente** | [Proyecto Final](../proyecto-final/README.md) |
