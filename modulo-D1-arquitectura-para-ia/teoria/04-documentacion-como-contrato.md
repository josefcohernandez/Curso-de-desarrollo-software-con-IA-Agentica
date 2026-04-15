# Documentación como Contrato con el Agente

## CLAUDE.md como Contrato Bidireccional

CLAUDE.md y `.claude/rules/` funcionan como **contratos bidireccionales**:

- **Tú escribes las reglas**: arquitectura, convenciones, restricciones, flujos de trabajo
- **El agente las sigue**: lee estos archivos al iniciar cada sesión y adapta su comportamiento

Es "bidireccional" porque no es solo documentación pasiva — es una especificación activa que el agente ejecuta. Si escribes "nunca modificar archivos en `src/legacy/`", el agente respetará esa restricción.

---

## Qué Incluir en un CLAUDE.md Orientado a AI-Readability

Un CLAUDE.md efectivo cubre 4 áreas fundamentales:

### 1. Arquitectura

Describe la estructura del proyecto a alto nivel para que el agente sepa dónde buscar y dónde crear archivos.

```markdown
## Arquitectura
- Monorepo con 3 packages: `api/`, `web/`, `shared/`
- `shared/` contiene types y utils comunes — NUNCA duplicar en `api/` o `web/`
- Base de datos: PostgreSQL con Prisma ORM
- `api/src/modules/` contiene los módulos de negocio, cada uno con su propio README
```

### 2. Testing

Documenta exactamente cómo ejecutar tests para que el agente pueda verificar su trabajo.

```markdown
## Testing
- `npm test` ejecuta todos los tests
- `npm run test:unit` solo unitarios (rápidos, sin DB)
- `npm run test:integration` requiere DB corriendo (`docker compose up db`)
- Antes de crear un PR, SIEMPRE ejecutar `npm test`
- Coverage mínimo: 80% en módulos nuevos
```

### 3. Convenciones

Haz explícitas todas las convenciones que el equipo sigue "por costumbre".

```markdown
## Convenciones
- Controllers solo orquestan — la lógica de negocio va en services
- Naming: archivos en kebab-case, funciones en camelCase, clases en PascalCase
- Error handling: usar `AppError(code, message)`, nunca `throw new Error()` genérico
- Imports: primero externos, luego internos, separados por línea en blanco
- No usar `any` en TypeScript — si no conoces el tipo, usar `unknown` y validar
```

### 4. Restricciones

Documenta lo que el agente NO debe hacer — las prohibiciones son tan importantes como las instrucciones.

```markdown
## Restricciones
- NUNCA modificar archivos en `src/legacy/` — están en proceso de deprecación
- NUNCA hacer `git push --force` ni commit directo a `main`
- No instalar dependencias sin preguntar primero
- Los archivos `.env` y `credentials/` son secretos — no leerlos ni commitearlos
```

---

## Organización con `.claude/rules/`

Para proyectos grandes, un solo CLAUDE.md puede volverse demasiado extenso. Usa `.claude/rules/` para organizar por tema:

```text
.claude/
├── rules/
│   ├── arquitectura.md      # Estructura del proyecto y módulos
│   ├── testing.md           # Cómo ejecutar tests y convenciones
│   ├── api-conventions.md   # Patrones específicos de la API
│   ├── database.md          # Migraciones, seeds, queries
│   └── security.md          # Restricciones de seguridad
```

Cada archivo en `.claude/rules/` se carga automáticamente cuando es relevante, proporcionando contexto especializado sin saturar el contexto base.

---

## Architecture Decision Records (ADRs) Asistidos por IA

### Qué son los ADRs

Un ADR documenta una decisión de arquitectura significativa: el contexto, la decisión tomada, las alternativas consideradas y las consecuencias.

### Por qué son valiosos para agentes

Cuando el agente puede leer ADRs anteriores:
- Entiende **por qué** el código es como es, no solo **cómo** es
- No propone soluciones que ya fueron descartadas
- Respeta las restricciones que motivaron las decisiones

### Formato recomendado

```markdown
# ADR-003: Usar Stripe como payment gateway

## Estado
Aceptado (2024-06-15)

## Contexto
Necesitamos procesar pagos en USD y EUR. El volumen esperado es
500-2,000 transacciones/día. Necesitamos soporte para subscripciones
recurrentes y refunds parciales.

## Decisión
Usamos Stripe con el SDK oficial de Node.js, envuelto en una
interfaz `PaymentGateway` propia (ver `src/services/payment-gateway.interface.ts`).

## Consecuencias
- Positivas: SDK maduro, buena documentación, soporte nativo para subscripciones
- Negativas: fees más altos que competidores, lock-in parcial en webhooks
- La interfaz `PaymentGateway` permite migrar a otro proveedor sin tocar la lógica de negocio

## Alternativas descartadas
- **PayPal**: SDK menos maduro, peor DX, webhooks inconsistentes
- **Implementación directa con API bancaria**: demasiado complejo para el volumen actual
```

### Workflow de ADRs con IA

1. **Generación**: pide al agente que genere un borrador de ADR basándose en una discusión o decisión
2. **Revisión humana**: el equipo revisa, ajusta y aprueba
3. **Almacenamiento**: guardar en `docs/adrs/` con numeración secuencial
4. **Consumo**: el agente lee ADRs existentes cuando trabaja en un módulo relacionado

### Prompt para generar ADRs

```text
Genera un ADR para la decisión de usar Redis como caché.
Contexto: aplicación web con 10k usuarios, necesitamos reducir
latencia de queries frecuentes. Lee los ADRs existentes en docs/adrs/
para mantener el formato consistente.
```

---

## Ejemplo Completo: CLAUDE.md para un Proyecto E-Commerce

```markdown
# CLAUDE.md — E-Commerce Platform

## Qué es este proyecto
Plataforma de e-commerce B2C. Stack: Node.js + TypeScript, Express,
PostgreSQL con Prisma, React frontend.

## Arquitectura
- Monorepo: `packages/api`, `packages/web`, `packages/shared`
- `packages/shared` contiene types y validadores — importar desde aquí, no duplicar
- Cada módulo en `packages/api/src/modules/` tiene su README con API y dependencias

## Comandos
- `npm run dev` — levanta API y web en paralelo
- `npm test` — ejecuta todos los tests (requiere DB)
- `npm run test:unit` — solo unitarios (sin DB)
- `npm run db:migrate` — ejecuta migraciones pendientes
- `npm run db:seed` — carga datos de prueba

## Convenciones
- Archivos: kebab-case (`payment-service.ts`)
- Funciones: camelCase
- Clases: PascalCase
- Controllers orquestan, services tienen la lógica
- Error handling: `throw new AppError("PAYMENT_FAILED", "...")`
- Validación: Zod schemas en `*.validator.ts`

## Restricciones
- NUNCA modificar `packages/shared/types/legacy/` — en deprecación
- No usar `any` — usar `unknown` + validación
- No instalar dependencias sin justificación
- Archivos `.env*` son secretos

## ADRs
- Ver `docs/adrs/` para decisiones de arquitectura
- Formato: ADR-NNN con contexto, decisión, consecuencias, alternativas
```

---

## Verificación: ¿Tu Documentación Funciona?

Después de crear o actualizar tu CLAUDE.md, verifica con esta prueba:

1. Abre una nueva sesión con el agente (sin contexto previo)
2. Pide una tarea que requiera conocer la arquitectura (ej: "Añade un endpoint para listar órdenes")
3. Observa si el agente:
   - Crea archivos en las ubicaciones correctas
   - Sigue las convenciones de naming
   - Usa los patrones documentados (AppError, Zod validators)
   - Ejecuta tests como paso de verificación

Si el agente comete errores que están cubiertos por la documentación, revisa si la redacción es suficientemente clara y explícita. Si comete errores no cubiertos, añade las reglas que faltan.
