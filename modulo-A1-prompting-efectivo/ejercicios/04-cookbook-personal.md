# Ejercicio 4: Cookbook Personal

## Objetivo

Crear una colección personal de 5 prompts reutilizables para las tareas que realizas con más frecuencia en tu trabajo. Estos prompts se convertirán en tu herramienta diaria — templates que adaptas en segundos para cada tarea concreta.

**Tiempo estimado: 15 minutos**

---

## Contexto

Los desarrolladores que más provecho sacan de los agentes de código no escriben cada prompt desde cero. Tienen una colección de **templates probados** que adaptan rápidamente. En este ejercicio vas a crear la tuya.

---

## Tarea

### Paso 1: Identifica tus 5 tareas más frecuentes

Piensa en las tareas que haces al menos una vez por semana con un agente de código. Algunos ejemplos comunes:

- Corregir un test que falla
- Añadir un nuevo endpoint a un API
- Revisar un PR antes de aprobar
- Entender un módulo nuevo
- Añadir validación a un formulario
- Crear un componente React/Vue
- Escribir tests para código existente
- Optimizar una query lenta
- Actualizar una dependencia

Elige **tus** 5 tareas (no tienen que ser de esta lista).

### Paso 2: Escribe un template para cada tarea

Para cada tarea, escribe un prompt template con placeholders marcados con `<corchetes>`. El template debe seguir el patrón correspondiente de la [teoría 03](../teoria/03-patrones-por-tarea.md).

**Ejemplo de template**:

```text
# Template: Fix Test que Falla

El test <nombre-del-test> en <archivo-de-test> falla con:
<mensaje-de-error-exacto>

El código bajo test está en <archivo-fuente>.
Investiga la causa raíz y corrige.

Verificar:
1. El test que fallaba ahora pasa
2. Los demás tests del archivo siguen pasando: <comando-de-test>
```

### Paso 3: Escribe un ejemplo concreto para cada template

Rellena cada template con datos reales de tu proyecto actual (o de un proyecto de ejemplo).

### Paso 4: Guarda tus templates

Elige una de estas opciones para guardar tus templates:

#### Opción A: En el archivo de instrucciones de tu proyecto

Añade una sección al archivo de instrucciones de tu proyecto (`AGENTS.md`, `CLAUDE.md` o equivalente):

```markdown
## Prompt Templates del Equipo

### Fix Test
El test [nombre] en [archivo] falla con [error].
El código está en [archivo fuente].
Investiga, corrige, y ejecuta [comando test].

### New Endpoint
...
```

> **Referencia**: ver [M04 - Memoria con CLAUDE.md](../../../Curso-desarrollo-software-con-Claude-Code/modulo-04-memoria-claude-md/README.md) para aprender a configurar estos archivos persistentes.

#### Opción B: Como custom slash commands (Claude Code)

Si usas Claude Code, puedes crear slash commands personalizados. Crea archivos en `.claude/commands/`:

```bash
# Crear el directorio de commands
mkdir -p .claude/commands
```

Ejemplo de archivo `.claude/commands/fix-test.md`:

```markdown
Investiga por qué falla el test indicado.
Lee el archivo de test y el código bajo test.
Identifica la causa raíz y corrígela.
Ejecuta los tests del archivo para verificar que pasan.
No modifiques otros archivos sin indicación explícita.
```

Luego invócalo con: `/project:fix-test test/auth.test.js:45`

> **Referencia**: ver [M09 - Agentes, Skills y Teams](../../../Curso-desarrollo-software-con-Claude-Code/modulo-09-agentes-skills-teams/README.md) para más detalles sobre custom slash commands.

#### Opción C: En un archivo de texto personal

Si usas otra herramienta (Cursor, Copilot, etc.), guarda tus templates en un archivo de referencia rápida que puedas consultar y copiar.

---

## Criterios de Evaluación

| Criterio | Puntos |
|----------|--------|
| Los 5 templates corresponden a tareas que realmente haces con frecuencia | 2 |
| Cada template tiene los 3 componentes mínimos (contexto, intención, verificación) | 3 |
| Los placeholders son descriptivos (`<archivo-de-test>`, no `<X>`) | 2 |
| Cada template tiene al menos un ejemplo concreto rellenado | 2 |
| Los templates están guardados en un formato reutilizable | 1 |

**Puntuación**: 10 puntos.

---

## Reflexión

1. Cuánto tiempo crees que ahorrarás por semana usando estos templates?
2. Qué templates compartirías con tu equipo?
3. Cada cuánto revisarías y actualizarías tus templates?

---

## Has Completado el Módulo A1

Felicidades. Ya tienes las bases del prompting efectivo para agentes de código:

- Los 3 componentes fundamentales (contexto, intención, verificación)
- Los 5 componentes de la estructura completa
- Los 6 patrones por tipo de tarea
- El cookbook de antes/después
- Las reglas de cuándo dar contexto y cómo iterar
- Tu colección personal de prompts reutilizables

Continúa con el [Módulo A2: Limitaciones, Fallos y Pensamiento Crítico](../../modulo-A2-limitaciones-fallos/README.md), donde aprenderás a detectar cuándo el agente se equivoca y cómo reaccionar.
