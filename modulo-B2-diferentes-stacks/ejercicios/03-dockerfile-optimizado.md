# Ejercicio 3: Optimizar un Dockerfile

## Objetivo

Analizar y optimizar un Dockerfile naive aplicando mejores prácticas de seguridad, eficiencia y caching.

## El Dockerfile naive

```dockerfile
FROM node:20
WORKDIR /app
COPY . .
RUN npm install
EXPOSE 3000
CMD ["node", "src/index.js"]
```

Contexto del proyecto: carpetas `src/`, `tests/`, `docs/`, `.git/`, `node_modules/` (500MB), `.env` con secrets. No hay `.dockerignore`.

## Instrucciones

### Paso 1: Análisis

```markdown
Analyze this Dockerfile and list ALL problems.
Categorize as: Security, Performance, or Size.
Explain the risk and propose a fix for each.
```

### Paso 2: Genera el Dockerfile optimizado

```markdown
Create an optimized Dockerfile:
- Multi-stage build (build + production)
- Non-root user in production stage
- Alpine base image
- Layer caching: package.json first, then source
- Production-only deps in final image
- HEALTHCHECK instruction
- .dockerignore file
```

### Paso 3: Compara ambas imágenes

Pide al agente que compare: tamaño de imagen, seguridad (root?), health check, layers.

## Problemas que debe resolver

| Problema | Categoría | Naive | Optimizado |
|----------|-----------|-------|------------|
| Ejecuta como root | Seguridad | Sin USER | USER no-root |
| Imagen grande | Tamaño | node:20 (~1GB) | node:20-alpine (~180MB) |
| Copia .git, tests, docs | Tamaño | Sin dockerignore | .dockerignore completo |
| Copia .env | Seguridad | Incluye secrets | .dockerignore excluye .env |
| Cache ineficiente | Rendimiento | COPY . . antes de install | package.json primero |
| Incluye devDependencies | Tamaño | npm install | npm ci --omit=dev |
| Sin health check | Operaciones | Ausente | HEALTHCHECK configurado |
| Sin multi-stage | Tamaño | Una etapa | Build + production |

## Criterios de evaluación

| Criterio | Peso | Descripción |
|----------|------|-------------|
| Seguridad | 30% | Non-root, sin secrets, sin innecesarios |
| Reducción de tamaño | 25% | Al menos 50% menor |
| Build performance | 20% | Layer caching correcto |
| Health check | 15% | HEALTHCHECK configurado |
| .dockerignore | 10% | Completo |

## Entregables

- `Dockerfile` optimizado
- `.dockerignore`
- Tabla comparativa de tamaño naive vs optimizado
