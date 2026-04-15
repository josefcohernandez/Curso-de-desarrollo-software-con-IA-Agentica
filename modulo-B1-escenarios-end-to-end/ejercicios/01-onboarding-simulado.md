# Ejercicio 1: Onboarding Simulado a un Repositorio Open Source

## Objetivo

Completar el workflow de onboarding de 4 pasos (teoría 01) en un repositorio open source real que no conoces. Al terminar debes tener un mapa de arquitectura documentado y un primer PR preparado.

**Tiempo máximo: 90 minutos**

---

## Preparación

Elige uno de estos repositorios open source según tu stack preferido:

| Repositorio | Lenguaje | Tamaño | Complejidad |
|-------------|----------|--------|-------------|
| [Express](https://github.com/expressjs/express) | JavaScript/Node.js | Medio (~15K líneas) | Media — framework web minimalista con middleware chain |
| [FastAPI](https://github.com/tiangolo/fastapi) | Python | Medio (~20K líneas) | Media — framework web moderno con tipado y OpenAPI auto |
| [Vue.js (core)](https://github.com/vuejs/core) | TypeScript | Grande (~60K líneas) | Alta — sistema reactivo, compiler, runtime |

**Recomendación**: si no tienes preferencia, elige Express. Es el más accesible y tiene una estructura clara.

Clona el repositorio en tu máquina local antes de empezar el cronómetro.

---

## Instrucciones paso a paso

### Fase 1: Exploración inicial (15 minutos)

1. Abre el repositorio con tu herramienta de IA
2. Usa el prompt de exploración de la teoría 01:

```text
Give me a high-level overview of this codebase:
- Main technologies and frameworks used
- Directory structure and what each top-level folder contains
- Entry points (main files, exports, CLI entry)
- Key abstractions and their relationships
Don't modify anything. Only investigate and report.
```

3. Anota las respuestas en un documento llamado `ONBOARDING.md`

### Fase 2: Deep dive (30 minutos)

1. Elige el flujo más importante del proyecto:
   - Express: el ciclo de vida de una request (desde `app.use()` hasta la respuesta)
   - FastAPI: cómo se procesa un endpoint decorado con `@app.get()`
   - Vue.js: el sistema de reactividad (desde `ref()` hasta la actualización del DOM)

2. Pide al agente que trace el flujo completo con archivos y funciones concretas

3. Documenta el flujo en `ONBOARDING.md` con un diagrama en texto

### Fase 3: Convenciones (15 minutos)

1. Usa el prompt de convenciones de la teoría 01
2. Anota en `ONBOARDING.md`:
   - Estilo de naming (archivos, funciones, variables)
   - Framework y estructura de tests
   - Patrones de error handling
   - Cómo se organizan los módulos

### Fase 4: Primera contribución (30 minutos)

1. Pide al agente que encuentre un cambio pequeño y seguro
2. Implementa el cambio siguiendo las convenciones del paso 3
3. Verifica que los tests pasan
4. Prepara el PR (no necesitas enviarlo, solo tener el commit y la descripción listos)

---

## Entregables

1. **Documento de arquitectura** (`ONBOARDING.md`, 3-5 paginas): overview, flujo principal con archivos concretos, convenciones, notas personales
2. **Primer PR**: commit con cambio pequeño, descripcion preparada, tests pasando

---

## Criterios de Evaluación

| Criterio | Peso | Excelente | Insuficiente |
|----------|------|-----------|--------------|
| Profundidad del overview | 25% | Identifica tecnologias, estructura y relaciones entre componentes | Solo lista carpetas |
| Calidad del flujo trazado | 25% | Flujo completo con archivos y funciones concretas | Flujo incompleto o incorrecto |
| Convenciones documentadas | 20% | 4+ convenciones identificadas y verificadas | Solo naming o ninguna |
| Calidad del PR | 20% | Cambio util, consistente con convenciones, tests pasan | No hay PR o rompe tests |
| Gestion del tiempo | 10% | Completado en 90 minutos | No completado |

---

## Reflexión post-ejercicio

1. ¿En qué fase el agente fue más útil? ¿En cuál menos?
2. ¿Cuántas veces tuviste que verificar o corregir información del agente?
3. ¿Cuánto habrías tardado en hacer este onboarding sin IA?
