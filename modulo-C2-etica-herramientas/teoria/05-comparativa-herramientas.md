# Panorama de Herramientas — Comparativa Objetiva (abril 2026)

## Introduccion

El mercado de herramientas de coding con IA ha madurado significativamente. Ya no se trata solo de autocompletado: hoy existen agentes autonomos en la nube, orquestacion multi-agente, ejecucion asincrona y protocolos abiertos como MCP adoptados por practicamente todos los actores. Este capitulo ofrece una comparativa basada en datos verificados para que elijas la herramienta que mejor encaja con tu workflow, stack y presupuesto.

> **Nota**: esta comparativa refleja el estado del mercado en abril de 2026. Las herramientas se actualizan constantemente. Verifica las features actuales antes de tomar decisiones.

---

## Comparativa de Herramientas Principales

| Herramienta | Tipo | Fortaleza principal | Debilidad | Mejor para | Precio (ref.) |
|-------------|------|---------------------|-----------|------------|---------------|
| **Claude Code** | CLI + IDE (VS Code 2M+ installs) + Web | Agente autonomo completo: 1M tokens (Opus 4.6), herramientas internas (Read/Edit/Bash/Glob/Grep), MCP, hooks, Agent Teams, tareas programadas, headless CI/CD, Computer Use | Curva de aprendizaje pronunciada; CLI-first puede intimidar | Maximo control, automatizacion, CI/CD, tareas multi-archivo | Pro $20, Max $100-200/mes, API pay-per-use |
| **Cursor** | IDE (fork VS Code) + CLI (GA ene 2026) | Composer multi-archivo, inline diffs, Tab completions propietarias. Cursor 3 (abr 2026): agentes paralelos, worktrees, Design Mode, cloud agents | Fork propietario (puede ir por detras de VS Code), precio alto en tiers superiores | Experiencia IDE todo-en-uno | Free / Pro $20 / Pro+ $60 / Ultra $200/mes |
| **GitHub Copilot** | Extension IDE + CLI (GA feb 2026) + Cloud Agent | Mejor integracion con GitHub (Issues, PRs, Actions), Agent Mode (GA en VS Code/JetBrains), Coding Agent autonomo, CLI Fleet Mode, multi-modelo (Claude, GPT, Gemini) | Acoplado al ecosistema GitHub; agente aun madurando vs Claude Code | Equipos deep en GitHub, autocompletado + agente hibrido | Free / Pro $10 / Pro+ $39 / Biz $19 / Ent $39/user/mes |
| **Windsurf** | IDE + Agent (adquirida por Cognition/Devin dic 2025, ~$250M) | Cascade agent, buen balance UX/potencia, Arena Mode, 82M ARR, #1 LogRocket AI Dev Rankings feb 2026 | Direccion futura incierta tras adquisicion, menos ecosistema comunitario | IDE integrado con agente potente | Free / Pro $20 / Teams $40 / Max $200/mes |
| **Cline** | Extension VS Code (open source) | BYOK (cualquier proveedor LLM), totalmente open source, soporta OpenRouter/Anthropic/OpenAI/Google/AWS/Azure/modelos locales | Requiere mas setup (BYOK), comunidad mas pequena | Control total, transparencia, modelos locales | Gratis (pagas tu proveedor LLM) |
| **OpenAI Codex CLI** | CLI (open source, Rust) | Competidor open source de Claude Code, soporte MCP, sub-agentes, plugins, web search | Ecosistema mas joven, atado a modelos OpenAI | Developers en ecosistema OpenAI que quieren CLI agent | API pay-per-use (precios OpenAI) |
| **Devin** | Agente autonomo cloud (Cognition AI) | Primer "AI software engineer", ejecucion totalmente asincrona, crea PRs completos, Devin Wiki para auto-documentacion, propietario de Windsurf | Caro a escala, menos control (cloud-only), requiere confianza en ejecucion autonoma | Ejecucion autonoma sin supervision de developer | Core $20 / Teams $500/mes |

**Mencion breve:**
- **Google Jules**: agente autonomo basado en Gemini 3 Pro, trabaja asincronamente con repos GitHub, ofrece CLI (Jules Tools) y API.
- **Amazon Q Developer**: asistente AWS con agentes autonomos, 25+ lenguajes, migracion Java, integrado en VS Code/JetBrains. Pro $19/user/mes.

---

## Los 5 Criterios para Elegir

### 1. Tu Workflow: Terminal vs IDE vs Cloud

| Si prefieres... | Considera... | Por que |
|-----------------|-------------|---------|
| Trabajar en terminal | Claude Code, Codex CLI | Disenados CLI-first, integracion con shell y Git |
| No salir del IDE | Cursor o Windsurf | La IA vive dentro del editor |
| VS Code sin cambiar de IDE | Copilot, Cline, o Claude Code (extension) | Extensiones que se anaden a tu VS Code existente |
| Delegar tareas completas sin supervisar | Devin, Copilot Coding Agent, Jules | Agentes cloud asincronos que crean PRs autonomamente |
| Flexibilidad total | Claude Code + Copilot | Autocompletado rapido + tareas complejas con agente |

### 2. Nivel de Automatizacion

| Necesidad | Herramienta recomendada |
|-----------|------------------------|
| Solo autocompletado inteligente | GitHub Copilot, Cursor (Tab) |
| Autocompletado + ediciones inline | Cursor, Windsurf |
| Agente que ejecuta tareas completas | Claude Code, Windsurf (Cascade), Codex CLI |
| Agente con orquestacion multi-agente | Claude Code (Agent Teams), Cursor 3 (parallel agents) |
| Pipeline CI/CD con IA | Claude Code (hooks + headless mode) |
| Ejecucion asincrona cloud (fire-and-forget) | Devin, Copilot Coding Agent, Jules |

### 3. Tu Stack Tecnologico

| Stack | Mejor soporte |
|-------|---------------|
| Ecosistema GitHub (Actions, Issues, PRs) | Copilot (integracion nativa), Devin |
| Proyectos con muchos archivos interrelacionados | Claude Code (1M tokens de contexto) |
| Desarrollo web frontend | Cursor (inline edits, Design Mode), Windsurf |
| Infraestructura / DevOps | Claude Code (acceso al terminal), Amazon Q Developer (AWS) |
| Proyectos multi-lenguaje | Claude Code o Cline (flexibilidad de proveedores) |

### 4. Presupuesto

| Herramienta | Tier gratuito | Tier profesional | Tier enterprise |
|-------------|---------------|------------------|-----------------|
| Claude Code | -- | Pro $20/mes, Max $100-200/mes | API pay-per-use |
| Cursor | Hobby (limitado) | Pro $20/mes, Pro+ $60/mes | Ultra $200, Teams $40/user/mes |
| GitHub Copilot | Si (2K completions/mes) | Pro $10, Pro+ $39/mes | Biz $19, Ent $39/user/mes |
| Windsurf | Si | Pro $20/mes | Teams $40, Max $200, Ent ~$60/user/mes |
| Cline | Gratis (OSS) | BYOK (coste del LLM) | BYOK |
| Codex CLI | Gratis (OSS) | API pay-per-use | API pay-per-use |
| Devin | -- | Core $20/mes | Teams $500/mes |

> **Nota**: los precios cambian frecuentemente. Verifica en la web oficial de cada herramienta.

### 5. Privacidad y Deployment

| Necesidad | Opciones |
|-----------|----------|
| Cloud estandar | Cualquier herramienta |
| Cloud privado (AWS/GCP) | Claude Code (Bedrock/Vertex), Copilot Enterprise, Amazon Q |
| On-premise / modelos locales | Cline (con LLM local), Codex CLI (con modelo local) |
| Sin retencion de datos | Claude Code API (configurable), Bedrock, Vertex |

---

## Nota Importante: MCP como Estandar Universal

El protocolo **MCP** (Model Context Protocol), originado por Anthropic, ha sido adoptado como estandar por practicamente todas las herramientas principales: Claude Code, Cursor, Copilot, Windsurf, Cline, Codex CLI. Esto significa que los **servidores MCP que desarrolles son portables** entre herramientas. Al invertir en MCP, no te atas a un vendor.

## Nota Importante: Agentes Cloud y Ejecucion Asincrona

Una dimension nueva del mercado es la **ejecucion autonoma asincrona**: asignas una tarea (un issue de GitHub, una descripcion de feature) y el agente trabaja en la nube sin supervision, entregando un PR cuando termina. Devin, Copilot Coding Agent, Jules y Claude Code (tareas programadas + headless mode) ofrecen variantes de este patron. Evalua si tu equipo esta preparado para confiar en ejecucion sin supervision humana continua.

---

## La Verdad Incomoda

**Ninguna herramienta es "la mejor" en todos los escenarios.** Muchos developers profesionales usan 2-3 herramientas complementarias:

| Combinacion comun | Para que |
|-------------------|----------|
| Copilot + Claude Code | Autocompletado rapido (Copilot) + tareas complejas con agente (Claude Code) |
| Cursor + Claude Code | IDE para ediciones visuales + CLI para automatizacion y CI/CD |
| Cline + herramienta comercial | Control total sobre el LLM + herramienta comercial para tareas especificas |
| Devin + herramienta interactiva | Tareas asincronas delegadas + herramienta interactiva para trabajo en tiempo real |

La pregunta no es "cual es la mejor" sino **"cual combinacion maximiza mi productividad?"**.

---

## Como Evaluar una Herramienta

Antes de comprometerte con una herramienta para tu equipo, haz esta evaluacion:

```markdown
## Evaluacion de Herramienta de IA

### Herramienta: [nombre]
### Evaluador: [nombre], Fecha: [fecha]

### Prueba 1: Fix Bug (15 min)
- Bug elegido: [descripcion]
- ¿Lo resolvio? [ ] Si  [ ] Parcialmente  [ ] No
- Iteraciones necesarias: ___
- Calidad del fix: [1-5]

### Prueba 2: Add Feature (30 min)
- Feature: [descripcion]
- ¿La implemento? [ ] Si  [ ] Parcialmente  [ ] No
- Tests incluidos: [ ] Si  [ ] No
- Calidad: [1-5]

### Prueba 3: Explore Codebase (10 min)
- Pregunta: [que querias entender]
- ¿Respuesta util? [ ] Si  [ ] Parcialmente  [ ] No
- Precision: [1-5]

### Prueba 4: Tarea Asincrona (si aplica, 60 min)
- Tarea delegada: [descripcion]
- ¿PR resultante compilable? [ ] Si  [ ] No
- ¿Tests pasan? [ ] Si  [ ] No
- Calidad del PR: [1-5]

### Veredicto
- Experiencia general: [1-5]
- ¿Lo usaria diariamente? [ ] Si  [ ] No
- ¿Lo recomendaria al equipo? [ ] Si  [ ] No
- Notas: [observaciones]
```

---

## Navegacion

| | |
|---|---|
| **Anterior** | [04-responsible-disclosure.md](04-responsible-disclosure.md) |
| **Siguiente** | [06-futuro-desarrollo-ia.md](06-futuro-desarrollo-ia.md) |
| **Modulo** | [README del modulo](../README.md) |
