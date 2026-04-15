# IA Agéntica y DevOps / Infrastructure as Code

## La regla de oro de DevOps con IA

Los errores en DevOps tienen consecuencias inmediatas en producción. Un componente React mal generado es una molestia visual; un `terraform apply` mal generado puede borrar una base de datos.

La regla es simple: **el agente genera, tú revisas, tú aplicas**.

## Terraform / Infrastructure as Code

**Fortalezas**: generación de resources y modules desde descripciones, buena separación en `main.tf`/`variables.tf`/`outputs.tf`, conversión entre providers (AWS → GCP).

**Debilidades**: puede inventar resource types inexistentes, generar security groups con `0.0.0.0/0`, no tiene visibilidad del estado actual de la infraestructura, no considera costes.

### Prompt template para Terraform

```markdown
Create a Terraform module for an AWS ECS Fargate service.
Requirements:
- ALB with HTTPS listener (use ACM certificate)
- ECS service with 2 tasks minimum, auto-scaling up to 6
- Security groups: ALB allows 443 inbound, service allows ALB only
Use variables for all configurable values.
Include: main.tf, variables.tf, outputs.tf, versions.tf.
IMPORTANT: Run terraform validate and terraform plan,
but DO NOT run terraform apply. I will review the plan first.
```

### Nunca `terraform apply` sin revisión humana

El flujo correcto: (1) agente genera HCL, (2) agente ejecuta `terraform validate`, (3) agente ejecuta `terraform plan`, (4) **tú** revisas el plan, (5) **tú** ejecutas `apply` manualmente.

## Kubernetes / Docker

El agente genera Dockerfiles y manifests funcionales, pero frecuentemente con problemas de seguridad:

| Problema común | Qué pedir al agente |
|---------------|-----------|
| Ejecutar como root | `USER` con usuario no-root |
| Imagen base grande (node:20 ~1GB) | Base image Alpine o slim |
| `COPY . .` antes de `npm install` | Copiar `package.json` primero |
| Sin health check | `HEALTHCHECK` con endpoint adecuado |
| Secretos en la imagen | Multi-stage build, secrets en runtime |

### Prompt template para Dockerfile seguro

```markdown
Create a Dockerfile for a Node.js Express application.
Security (non-negotiable): multi-stage build, non-root user,
Alpine base image, no secrets in image, HEALTHCHECK instruction.
Performance: layer caching (package.json first), .dockerignore,
production-only dependencies in final stage.
```

### Kubernetes: exigir siempre seguridad

En manifests de Kubernetes, exige siempre: `runAsNonRoot: true`, `readOnlyRootFilesystem: true`, `allowPrivilegeEscalation: false`, resource requests/limits, y probes de liveness/readiness.

```markdown
Create Kubernetes manifests for a web application:
- Deployment with 3 replicas, Service (ClusterIP), Ingress with TLS
Security requirements (include ALL):
- runAsNonRoot, readOnlyRootFilesystem, no privilege escalation
- Resource requests AND limits
- Liveness and readiness probes
```

## CI/CD Pipelines

El agente genera pipelines de GitHub Actions, GitLab CI y similares competentemente. Las precauciones críticas son:

1. **Secrets**: puede exponer secrets en logs o pasarlos como argumentos CLI
2. **Permisos**: puede asignar `permissions: write-all` cuando solo necesita `contents: read`
3. **Triggers**: puede configurar triggers demasiado amplios
4. **Versiones**: puede usar `actions/checkout@main` en vez de `@v4` con SHA — riesgo de supply chain attack

```markdown
Create a GitHub Actions CI workflow for a Node.js project.
Requirements: trigger on push/PR to main, jobs: lint, test, build.
Security: pin action versions with SHA, minimal permissions
(contents: read), never echo secrets.
```

## Checklist de revisión para DevOps

Antes de aplicar cualquier configuración generada por el agente:

- [ ] **Terraform**: revisé el plan, entiendo cada resource que se crea/modifica/destruye
- [ ] **Docker**: no ejecuta como root, base Alpine/slim, health check presente
- [ ] **Kubernetes**: resource limits, security contexts, probes configurados
- [ ] **CI/CD**: secrets protegidos, permisos mínimos, acciones con versión fija
- [ ] **General**: no hay `0.0.0.0/0`, no hay credenciales hardcodeadas

## Resumen

| Stack DevOps | El agente es bueno en... | Siempre verifica... |
|-------------|------------------------|-------------------|
| Terraform | Generar modules y resources | El plan antes de apply |
| Docker | Sintaxis de Dockerfile | Seguridad (root, secrets, base image) |
| Kubernetes | YAML válido y estructura | Security contexts, limits, probes |
| CI/CD | Estructura de pipelines | Secrets, permisos, versiones de actions |
