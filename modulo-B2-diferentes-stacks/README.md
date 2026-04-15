# Módulo B2: Trabajar con Diferentes Stacks

## Descripción General

Cada stack tecnológico tiene sus particularidades cuando trabajas con un agente de código. Lo que funciona perfectamente en frontend puede ser un desastre en DevOps; las fortalezas del agente en Python no son las mismas que en Go o Rust. Este módulo te da un mapa completo: **qué esperar del agente en cada stack**, dónde brilla, dónde falla, y qué patrones aplicar para obtener resultados profesionales.

No se trata de aprender cada tecnología — se trata de saber **cómo dirigir al agente** según el contexto técnico en el que estés trabajando. Desde generar un componente React hasta escribir una migración SQL o un Dockerfile multi-stage, aprenderás a adaptar tu forma de trabajar al stack.

**Tiempo estimado: 2 horas 30 minutos**

---

## Objetivos de Aprendizaje

Al completar este módulo serás capaz de:

1. **Identificar** las fortalezas y debilidades del agente en frontend, backend, DevOps, data y mobile
2. **Adaptar** tus prompts y tu workflow al stack tecnológico específico del proyecto
3. **Aplicar** patrones efectivos para cada dominio: VDD en frontend, OpenAPI-first en backend, revisión humana obligatoria en IaC
4. **Detectar** las trampas comunes por stack: componentes monolíticos en React, queries ineficientes en SQL, configuraciones inseguras en Docker
5. **Implementar** un flujo de trabajo completo para mobile que compense la incapacidad del agente de ver la UI
6. **Ejecutar** tareas reales en cada stack usando las técnicas del módulo: desde un componente hasta un feature fullstack

---

## Contenido del Módulo

### Teoría

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-frontend-react-vue.md](teoria/01-frontend-react-vue.md) | IA agéntica y frontend: fortalezas, debilidades, patrones efectivos y Visual-Driven Development | 15 min |
| 2 | [02-backend-node-python-go.md](teoria/02-backend-node-python-go.md) | IA agéntica y backend: tabla por lenguaje (Python, Node, Go, Java, Rust) y patrones comunes | 15 min |
| 3 | [03-devops-iac.md](teoria/03-devops-iac.md) | IA agéntica y DevOps/IaC: Terraform, Kubernetes, Docker, CI/CD — seguridad y revisión humana | 15 min |
| 4 | [04-data-sql-pandas.md](teoria/04-data-sql-pandas.md) | IA agéntica y data: SQL, pandas, notebooks — queries eficientes y análisis exploratorio | 15 min |
| 5 | [05-mobile.md](teoria/05-mobile.md) | IA agéntica y mobile: React Native, Flutter — el workflow visual iterativo | 10 min |

### Ejercicios Prácticos

| # | Archivo | Tema | Tiempo estimado |
|---|---------|------|-----------------|
| 1 | [01-frontend-desde-mockup.md](ejercicios/01-frontend-desde-mockup.md) | Implementar un componente React de login desde una descripción textual con Tailwind y tests | 20 min |
| 2 | [02-backend-crud-api.md](ejercicios/02-backend-crud-api.md) | Crear una REST API de librería con CRUD, validación, paginación y tests de integración | 20 min |
| 3 | [03-dockerfile-optimizado.md](ejercicios/03-dockerfile-optimizado.md) | Optimizar un Dockerfile naive: multi-stage, seguridad, caching y tamaño | 15 min |
| 4 | [04-analisis-dataset.md](ejercicios/04-analisis-dataset.md) | Analizar un dataset CSV de ventas con pandas, visualizaciones y documentación | 15 min |
| 5 | [05-feature-fullstack.md](ejercicios/05-feature-fullstack.md) | Implementar un feature "favoritos" fullstack: React + Express + PostgreSQL con TDD | 20 min |

---

## Prerequisitos

- Haber completado el [Módulo A1: Prompting Efectivo](../modulo-A1-prompting-efectivo/README.md) — necesitas saber escribir buenos prompts antes de adaptarlos por stack
- Experiencia práctica con al menos una herramienta de coding con IA (Claude Code, Cursor, Copilot, etc.)
- Conocimiento básico de al menos 2 de los 5 stacks cubiertos (frontend, backend, DevOps, data, mobile)
- **Recomendado**: haber completado el [Módulo B1: Escenarios End-to-End](../modulo-B1-escenarios-end-to-end/README.md)

---

## Conceptos Clave

- **Fortalezas por stack**: el agente destaca en generación de componentes UI, queries SQL complejas y scaffolding de infraestructura
- **Debilidades por stack**: componentes monolíticos en frontend, queries ineficientes en SQL, configuraciones inseguras en Docker/Kubernetes
- **Visual-Driven Development (VDD)**: workflow de mockup → análisis → código → iteración para frontend y mobile
- **OpenAPI-first**: en backend, generar la especificación antes del código de implementación
- **Revisión humana obligatoria**: en IaC (Terraform, Kubernetes), nunca aplicar sin revisión del plan
- **El agente no ve la UI**: en mobile, el workflow iterativo con screenshots compensa esta limitación
- **Adaptar el prompt al stack**: cada dominio requiere restricciones y verificaciones diferentes

---

## Flujo de Trabajo Recomendado

1. **Lee las 5 teorías**: incluso si solo trabajas con un stack, entender las diferencias te hace más consciente de las limitaciones generales del agente
2. **Identifica tu stack principal**: enfócate en los ejercicios que coinciden con tu trabajo diario
3. **Haz al menos 3 ejercicios**: el de tu stack principal, el de Dockerfile (aplicable a casi todos) y el fullstack
4. **Compara resultados**: observa cómo cambia la calidad del output del agente según el stack y las restricciones que le das
5. **Documenta tus patrones**: crea prompts reutilizables para los stacks que más uses

---

## Navegación

| | |
|---|---|
| **Anterior** | [Módulo B1: Escenarios End-to-End](../modulo-B1-escenarios-end-to-end/README.md) |
| **Siguiente** | [Módulo C1: Adopción en Equipos](../modulo-C1-adopcion-equipos/README.md) |
