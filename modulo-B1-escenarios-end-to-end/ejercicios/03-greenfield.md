# Ejercicio 3: Proyecto Greenfield — CLI de Monitorización de Cambios

## Objetivo

Partiendo de una idea de dos líneas, crear un MVP funcional en 2 horas siguiendo el workflow greenfield de la teoría 03: especificación, arquitectura, scaffolding y primera feature con TDD.

**Tiempo máximo: 2 horas**

---

## La idea

> "Un CLI tool que monitoriza un directorio en busca de cambios en archivos y genera un changelog en formato Markdown."

---

## Instrucciones

### Fase 1: Especificación (30 minutos)

Usa el patrón de entrevista de la teoría 03. El agente te preguntará sobre features, constraints, usuarios, edge cases y formato de output. Guía sugerida para tus respuestas:

- **Lenguaje**: Node.js con TypeScript (o Python)
- **Features MVP**: detectar archivos creados, modificados y eliminados; generar `changelog.md`
- **Modo de uso**: ejecución puntual (`changemon ./src`) y modo watch (`changemon --watch ./src`)
- **Formato changelog**: fecha, tipo de cambio (added/modified/deleted), nombre del archivo, tamaño
- **Ignorar**: `node_modules/`, `.git/`, archivos binarios
- **Persistencia**: snapshot del estado en `.changemon/snapshot.json`

**Entregable**: archivo `SPEC.md` con requisitos formalizados.

### Fase 2: Arquitectura (20 minutos)

Pide al agente que diseñe la arquitectura basándose en `SPEC.md`. Verifica antes de aprobar:

- [ ] Estructura proporcional al proyecto (un CLI simple no necesita 15 módulos)
- [ ] Dependencias justificadas
- [ ] Formato del snapshot y changelog definido con ejemplos
- [ ] CLI interface clara (flags `--watch`, `--output`, `--ignore`)

**Entregable**: archivo `ARCHITECTURE.md`.

### Fase 3: Scaffolding (15 minutos)

El agente crea la estructura del proyecto. Verificar que `npm install` y `npm test` funcionan sin errores.

### Fase 4: Primera feature — detectar cambios (55 minutos)

Implementa la feature principal con TDD. Tests para estos escenarios:

1. First run (sin snapshot previo): todos los archivos son "added"
2. Archivo nuevo desde el ultimo snapshot
3. Archivo modificado (contenido cambiado)
4. Archivo eliminado
5. Archivos ignorados (`node_modules`, `.git`) excluidos

Ejemplo del output esperado:

```markdown
# Changelog — 2024-03-15 10:30:00

## Added
- `src/utils/helpers.ts` (1.2 KB)

## Modified
- `src/index.ts` (2.1 KB -> 2.4 KB)

## Deleted
- `src/old-module.ts`
```

---

## Entregables

1. **SPEC.md**: requisitos completos
2. **ARCHITECTURE.md**: diseño tecnico
3. **Proyecto scaffolded**: `npm install` y `npm test` operativos
4. **Feature de deteccion de cambios**: scanner + changelog generator + tests
5. **Al menos un commit** bajo control de versiones

---

## Criterios de Evaluación

| Criterio | Peso | Excelente | Insuficiente |
|----------|------|-----------|--------------|
| Calidad de SPEC.md | 15% | Requisitos claros y completos | SPEC vaga o ausente |
| Calidad de ARCHITECTURE.md | 15% | Diseño proporcionado y justificado | Ausente o over-engineered |
| Feature implementada | 35% | Detecta adds, modifies, deletes con tests | No detecta cambios o sin tests |
| Calidad de tests | 25% | 5+ tests con happy path y edge cases | Menos de 3 tests |
| Proceso seguido | 10% | Spec -> Architecture -> Scaffold -> TDD | Salto directo a codigo |

---

## Reflexión post-ejercicio

1. ¿Cuánto tiempo dedicaste a spec + arquitectura vs. código? ¿Fue la proporción correcta?
2. ¿El agente propuso una arquitectura razonable o tuviste que simplificarla?
3. ¿Los tests del TDD te ayudaron a implementar mejor o fueron overhead?
