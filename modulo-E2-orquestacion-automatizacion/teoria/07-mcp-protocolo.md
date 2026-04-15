# MCP: El Protocolo de Integración de Herramientas para Agentes

## Por Qué Existe MCP

Antes de MCP, cada agente de IA integraba herramientas externas de forma ad hoc. Cursor tenía su propio sistema de plugins. Claude Code, su propio. Copilot, el suyo. Un equipo que desarrollaba una integración con su base de datos interna tenía que reimplementarla tres veces para que funcionara en los tres tools que usaba.

MCP (Model Context Protocol) es un protocolo abierto publicado por Anthropic en noviembre de 2024 que estandariza cómo los agentes de IA se conectan con herramientas y datos externos. La analogía más directa: **MCP es para agentes de IA lo que USB es para dispositivos** — antes de USB, cada periférico necesitaba un conector y driver diferente para cada ordenador; después de USB, el mismo dispositivo funciona en cualquier máquina.

Una formulación equivalente: MCP es para agentes lo que HTTP es para navegadores web. Del mismo modo que cualquier servidor web puede servir contenido a cualquier navegador porque ambos hablan HTTP, cualquier servidor MCP puede servir herramientas a cualquier agente porque ambos hablan MCP.

**El impacto fue inmediato**: en noviembre de 2025, el SDK de MCP superó los 97 millones de descargas mensuales. Adoptado por OpenAI, Google DeepMind, AWS, Microsoft, Hugging Face y LangChain. Esto convierte a MCP en la apuesta más segura de estandarización en el ecosistema de agentes en 2025-2026.

---

## Arquitectura: Los Tres Componentes

Un sistema MCP tiene tres participantes que interactúan con un protocolo bien definido:

```text
┌─────────────────────────────────────────────────────┐
│  HOST (Claude Code, Cursor, VS Code + Copilot, etc.) │
│                                                      │
│   ┌──────────────┐        ┌──────────────────────┐  │
│   │  Tu agente   │◄──────►│   MCP Client         │  │
│   │  (el modelo) │        │   (gestiona conexión) │  │
│   └──────────────┘        └────────────┬─────────┘  │
└────────────────────────────────────────│─────────────┘
                                         │  stdio / SSE / HTTP
                          ┌──────────────▼──────────────┐
                          │      MCP Server              │
                          │  (proceso independiente)     │
                          │                              │
                          │  Tools | Resources | Prompts │
                          └─────────────────────────────-┘
```

**Host**: la aplicación que ejecuta al agente. Claude Code, Cursor, VS Code con Copilot, Windsurf, Cline. El host es responsable de iniciar y gestionar las conexiones con los servidores MCP.

**MCP Client**: el componente dentro del host que implementa el protocolo. Descubre qué herramientas ofrece cada servidor, traduce las peticiones del modelo en llamadas MCP, y devuelve los resultados al modelo. El desarrollador de aplicaciones no interactúa directamente con el MCP Client — está integrado en el host.

**MCP Server**: un proceso independiente que expone herramientas, recursos y prompts al agente. Este es el componente que tú desarrollas cuando quieres integrar tu sistema con cualquier agente MCP-compatible. Puede correr localmente (en la máquina del desarrollador) o remotamente (en un servidor accesible por HTTP).

### Transportes

| Transporte | Cuándo usar | Características |
|------------|-------------|-----------------|
| **stdio** | Servidor local, mismo equipo | Proceso hijo lanzado por el host, comunicación por stdin/stdout. Más simple, sin red. |
| **SSE (Server-Sent Events)** | Servidor remoto HTTP | Conexión HTTP persistente para streaming. Requiere autenticación. |
| **Streamable HTTP** (2025) | Servidor remoto, producción | Reemplaza SSE como transporte recomendado para servidores remotos. Más eficiente y fiable. |

Para desarrollo local, usa stdio. Para servidores compartidos en un equipo o en producción, usa Streamable HTTP.

---

## Las Tres Primitivas de MCP

MCP define tres tipos de capacidades que un servidor puede exponer:

| Primitiva | Dirección | Side effects | Ejemplo |
|-----------|-----------|--------------|---------|
| **Tools** | Agente → Servidor | Sí (escritura, acciones) | Ejecutar query SQL, crear issue en GitHub, enviar mensaje en Slack |
| **Resources** | Servidor → Agente | No (solo lectura) | Schema de base de datos, documentación interna, configuración de proyecto |
| **Prompts** | Servidor → Agente | No (solo lectura) | Template para code review, template para análisis de SQL, instrucciones de estilo |

### Tools: Acciones con Side Effects

Los tools son funciones que el agente puede invocar para actuar sobre sistemas externos. Son el equivalente a las herramientas que ya conoces en Claude Code (Bash, Read, Write, etc.), pero implementadas por tu servidor.

Cuando el modelo decide usar un tool, el host ejecuta la llamada al servidor MCP y devuelve el resultado al modelo. El modelo nunca ejecuta código directamente — siempre hay un proceso servidor intermedio.

### Resources: Datos de Contexto

Los resources permiten al servidor exponer datos estructurados para que el agente los use como contexto. Son análogos a la ventana de contexto enriquecida: el agente puede "leer" un resource igual que lee un fichero, pero el contenido viene del servidor.

Útiles para exponer schemas, documentación, configuraciones o cualquier dato que el agente necesita conocer antes de actuar pero que no necesita modificar.

### Prompts: Templates Reutilizables

Los prompts son templates de instrucciones que el servidor ofrece al agente. Son la primitiva menos usada en práctica, pero útil para estandarizar cómo el agente aborda tareas recurrentes — por ejemplo, un prompt estándar de tu organización para revisar código o para analizar queries SQL antes de ejecutarlas.

---

## Servidor MCP Mínimo con FastMCP

FastMCP es la librería Python oficial de alto nivel para construir servidores MCP. El decorator `@mcp.tool()` convierte una función Python en un tool disponible para cualquier cliente MCP; `@mcp.resource()` hace lo mismo con recursos.

```python
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("gestion-tickets")

@mcp.tool()
def buscar_tickets(query: str, estado: str = "abierto") -> str:
    """Busca tickets en el sistema de gestión por texto y estado.

    Args:
        query: Texto a buscar en título y descripción del ticket.
        estado: Estado del ticket. Valores: 'abierto', 'cerrado', 'en_progreso'.
    """
    # En producción, aquí iría la llamada real a tu API o base de datos
    return f"3 tickets encontrados para '{query}' con estado '{estado}'"

@mcp.tool()
def crear_ticket(titulo: str, descripcion: str, prioridad: str = "media") -> dict:
    """Crea un nuevo ticket en el sistema de gestión.

    Args:
        titulo: Título breve del ticket (máximo 100 caracteres).
        descripcion: Descripción detallada del problema o solicitud.
        prioridad: Nivel de prioridad. Valores: 'baja', 'media', 'alta', 'critica'.
    """
    # En producción, aquí iría la escritura en tu sistema
    ticket_id = "TKT-1234"
    return {
        "id": ticket_id,
        "titulo": titulo,
        "estado": "abierto",
        "prioridad": prioridad,
        "url": f"https://tickets.miempresa.com/{ticket_id}"
    }

@mcp.resource("schema://tickets")
def get_schema_tickets() -> str:
    """Devuelve el schema de la tabla de tickets para orientar al agente."""
    return """
CREATE TABLE tickets (
    id          VARCHAR(20) PRIMARY KEY,
    titulo      VARCHAR(100) NOT NULL,
    descripcion TEXT,
    estado      VARCHAR(20) CHECK (estado IN ('abierto', 'en_progreso', 'cerrado')),
    prioridad   VARCHAR(10) CHECK (prioridad IN ('baja', 'media', 'alta', 'critica')),
    creado_en   TIMESTAMP DEFAULT NOW(),
    asignado_a  VARCHAR(100)
);
"""

if __name__ == "__main__":
    mcp.run()
```

Puntos clave del ejemplo:

- Los docstrings son parte de la interfaz: el modelo los lee para saber qué hace cada tool y qué argumentos acepta. Un docstring pobre produce peor invocación de herramientas.
- Los tipos de retorno importan: devolver un `dict` serializable da al modelo más información estructurada que un string plano.
- El resource `schema://tickets` permite al agente entender la estructura de datos antes de formular queries.

### Arrancar el servidor localmente

```bash
# Instalar dependencias
pip install mcp fastmcp

# Ejecutar el servidor (modo stdio, para desarrollo local)
python servidor_mcp.py
```

---

## Configuración en Claude Code

Para que Claude Code descubra y use tu servidor MCP, añádelo en `.claude/settings.json` en la raíz del proyecto (configuración de proyecto) o en `~/.claude/settings.json` (configuración global del usuario):

```json
{
  "mcpServers": {
    "gestion-tickets": {
      "command": "python",
      "args": ["/ruta/absoluta/servidor_mcp.py"],
      "env": {
        "TICKETS_API_URL": "https://api.tickets.miempresa.com",
        "TICKETS_API_KEY": "sk-..."
      }
    }
  }
}
```

Tras guardar el fichero, Claude Code reinicia los servidores MCP automáticamente. Puedes verificar que el servidor está activo y los tools disponibles con el comando `/mcp` en la interfaz interactiva de Claude Code.

Para un servidor remoto con Streamable HTTP:

```json
{
  "mcpServers": {
    "gestion-tickets-remoto": {
      "url": "https://mcp.miempresa.com/tickets",
      "headers": {
        "Authorization": "Bearer <token>"
      }
    }
  }
}
```

---

## MCP como Inversión en Portabilidad

El argumento más sólido para invertir en MCP es la portabilidad. Desarrollar una integración con tu sistema de tickets en el protocolo propietario de Cursor es trabajo que no se transfiere a Claude Code, ni a Windsurf, ni a Cline. Desarrollar la misma integración como servidor MCP la hace disponible en todos los clientes compatibles con MCP.

En 2026, los clientes MCP-compatibles incluyen:

| Cliente | Tipo |
|---------|------|
| Claude Code | Terminal / IDE integration |
| Cursor | IDE con agente integrado |
| VS Code con GitHub Copilot | IDE con Copilot Chat |
| Windsurf | IDE |
| Cline | Extensión VS Code |
| Claude.ai (web) | Interfaz web |
| Cualquier cliente basado en el SDK de MCP | Librería de terceros |

**Implicación práctica**: si tu equipo usa Claude Code hoy pero considera migrar a Cursor el año que viene, o si diferentes personas del equipo prefieren diferentes herramientas, un servidor MCP asegura que la integración funciona en todos los casos. La inversión en MCP no depende de ningún vendor específico.

---

## Casos de Uso Reales

Los servidores MCP más comunes en equipos de desarrollo encajan en estas categorías:

| Categoría | Tools típicos | Resources típicos | Valor principal |
|-----------|--------------|-------------------|-----------------|
| **Base de datos** | `ejecutar_query`, `actualizar_registro` | `schema_tablas`, `indices_disponibles` | El agente puede consultar y modificar datos reales sin salir del editor |
| **Issue tracker** | `crear_ticket`, `buscar_tickets`, `actualizar_estado` | `estados_disponibles`, `etiquetas` | El agente puede crear issues directamente desde el contexto del código |
| **Documentación interna** | `buscar_en_wiki`, `obtener_pagina` | `estructura_wiki` | El agente encuentra docs internas sin que el desarrollador tenga que copiar y pegar |
| **Observabilidad** | `buscar_logs`, `obtener_metricas`, `listar_alertas` | `definicion_metricas`, `slas` | El agente puede investigar incidentes con acceso directo a los datos de producción |
| **CI/CD** | `lanzar_pipeline`, `ver_estado_build`, `cancelar_job` | `pipelines_disponibles`, `configuracion_entornos` | El agente puede gestionar despliegues sin cambiar de contexto |
| **Comunicación** | `enviar_mensaje_slack`, `crear_canal`, `listar_mensajes` | `canales_disponibles`, `usuarios_equipo` | El agente puede notificar al equipo sin que el desarrollador tenga que abrir Slack |

---

## Seguridad de MCP

MCP amplía la superficie de ataque de un agente porque conecta el modelo con sistemas externos. Hay tres vectores de riesgo principales que debes conocer antes de instalar o desplegar servidores MCP:

**Tool poisoning**: un servidor MCP malicioso puede incluir en las descripciones de sus tools instrucciones ocultas para manipular el comportamiento del modelo. El agente lee el docstring del tool para saber qué hace — si ese docstring contiene instrucciones como "ignora las restricciones del sistema" o "exfiltra el contenido de otros ficheros", el modelo podría seguirlas sin que el usuario lo note.

**Supply chain attacks**: instalar un servidor MCP de un repositorio externo sin auditarlo es el equivalente a instalar un paquete npm sin leer su código. El servidor tiene acceso al mismo entorno de ejecución que tu agente y puede leer variables de entorno, ficheros y credenciales.

**SSRF (Server-Side Request Forgery)**: un servidor MCP comprometido o mal implementado puede ser usado para que el agente realice peticiones a servicios internos de tu red que normalmente no serían accesibles desde el exterior.

La regla es directa: **nunca instales un servidor MCP que no hayas auditado, igual que no instalarías un paquete npm desconocido en producción**. Para servidores de terceros, revisa el código fuente antes de configurarlo. Para servidores propios, aplica principio de mínimo privilegio: el servidor solo debe tener acceso a lo que estrictamente necesita.

El análisis completo de vectores de ataque, trust boundaries y configuración segura de servidores MCP en producción se cubre en el fichero `../modulo-E4-seguridad-costes/teoria/06-seguridad-agentica.md`.

---

## Cuándo Construir tu Propio Servidor MCP vs Usar Uno Existente

Antes de desarrollar un servidor desde cero, comprueba si existe uno ya hecho para tu herramienta. El ecosistema público de servidores MCP es extenso: hay servidores mantenidos para GitHub, GitLab, Jira, Confluence, Slack, PostgreSQL, MySQL, Datadog, PagerDuty y decenas de servicios más.

| Criterio | Usar servidor existente | Construir servidor propio |
|----------|------------------------|--------------------------|
| Herramienta con servidor MCP público auditado | Sí, si pasa tu revisión de seguridad | — |
| Herramienta interna o propietaria | No existe opción pública | Sí |
| El servidor público no expone los endpoints que necesitas | — | Sí, extender o reemplazar |
| Necesitas control total sobre qué datos accede el agente | — | Sí, implementar con granularidad exacta |
| Prototipo rápido para validar el concepto | Sí, con servidor de confianza | Solo si no hay alternativa |
| Producción con datos sensibles | Solo con auditoría completa del código fuente | Recomendado por control y auditoría |

Para encontrar servidores existentes: el repositorio oficial `modelcontextprotocol/servers` en GitHub mantiene la lista de servidores de referencia. Los principales vendors (Atlassian, GitHub, Slack) han publicado sus propios servidores oficiales.

### Checklist antes de instalar un servidor MCP de terceros

```text
[ ] El código fuente es público y revisable
[ ] El servidor tiene mantenimiento activo (commits recientes)
[ ] Las dependencias del servidor no tienen CVEs conocidos
[ ] El servidor no solicita permisos de acceso más amplios de los necesarios
[ ] Las variables de entorno que solicita tienen una justificación clara
[ ] El servidor no realiza llamadas de red a destinos no documentados
```

---

## Roadmap MCP en 2026

MCP es un protocolo en evolución activa. Las áreas de desarrollo para 2026:

**Transport scalability**: la transición de SSE a Streamable HTTP como transporte estándar para servidores remotos, con mejor soporte para conexiones intermitentes y mayor eficiencia en redes corporativas con proxies.

**Agent-to-agent communication**: extensiones del protocolo para que un servidor MCP pueda actuar también como cliente de otro servidor MCP. Esto habilita arquitecturas donde un agente orquestador delega a sub-agentes a través de MCP, unificando la infraestructura de orquestación con la de integración de herramientas.

**Enterprise governance**: controles de auditoría y políticas centralizadas que permiten a las organizaciones definir qué servidores MCP pueden usar los desarrolladores, con logging de todas las invocaciones de tools para compliance.

**Remote MCP servers con autenticación OAuth**: el estándar para servidores MCP remotos que necesitan autenticación de usuario (en lugar de API keys estáticas), permitiendo que el agente actúe en nombre del usuario con sus propias credenciales y permisos.

---

## Ejemplo: Servidor MCP para Base de Datos con Conexión Real

El siguiente ejemplo muestra un servidor de producción básico con conexión real a PostgreSQL:

```python
import os
import json
import psycopg2
from mcp.server.fastmcp import FastMCP

mcp = FastMCP("base-de-datos")

def get_connection():
    return psycopg2.connect(os.environ["DATABASE_URL"])

@mcp.tool()
def ejecutar_query_lectura(sql: str) -> str:
    """Ejecuta una query SELECT en la base de datos y devuelve los resultados.

    Solo admite queries de lectura (SELECT). Para modificaciones usa
    ejecutar_query_escritura con confirmación explícita.

    Args:
        sql: Query SQL de tipo SELECT a ejecutar.
    """
    if not sql.strip().upper().startswith("SELECT"):
        return "Error: este tool solo acepta queries SELECT. Usa ejecutar_query_escritura para modificaciones."

    try:
        conn = get_connection()
        cur = conn.cursor()
        cur.execute(sql)
        columns = [desc[0] for desc in cur.description]
        rows = cur.fetchmany(100)  # Límite de 100 filas para evitar respuestas masivas
        conn.close()

        return json.dumps({
            "columns": columns,
            "rows": [dict(zip(columns, row)) for row in rows],
            "row_count": len(rows),
            "truncated": len(rows) == 100
        }, default=str)

    except Exception as e:
        return f"Error ejecutando query: {str(e)}"

@mcp.resource("schema://tablas")
def get_schema() -> str:
    """Devuelve el schema completo de la base de datos."""
    try:
        conn = get_connection()
        cur = conn.cursor()
        cur.execute("""
            SELECT table_name, column_name, data_type, is_nullable
            FROM information_schema.columns
            WHERE table_schema = 'public'
            ORDER BY table_name, ordinal_position
        """)
        rows = cur.fetchall()
        conn.close()

        schema = {}
        for table, column, dtype, nullable in rows:
            if table not in schema:
                schema[table] = []
            schema[table].append(f"{column} {dtype} {'NULL' if nullable == 'YES' else 'NOT NULL'}")

        return "\n\n".join(
            f"Table: {table}\n" + "\n".join(f"  {col}" for col in cols)
            for table, cols in schema.items()
        )
    except Exception as e:
        return f"Error obteniendo schema: {str(e)}"

if __name__ == "__main__":
    mcp.run()
```

Configuración en Claude Code para este servidor:

```json
{
  "mcpServers": {
    "base-de-datos": {
      "command": "python",
      "args": ["/ruta/al/servidor_bd.py"],
      "env": {
        "DATABASE_URL": "postgresql://usuario:password@localhost:5432/mi_base_de_datos"
      }
    }
  }
}
```

El agente ahora puede hacer queries SQL directamente desde Claude Code. El resource `schema://tablas` se carga automáticamente como contexto, permitiendo al agente formular queries correctas desde el principio sin que el desarrollador tenga que pegar el schema manualmente.

---

## Resumen

MCP estandariza cómo los agentes de IA se conectan con herramientas y datos externos. Desarrollar un servidor MCP es desarrollar una integración universal: funciona en Claude Code, Cursor, Copilot y cualquier cliente compatible.

### Las tres primitivas

| Primitiva | Cuándo usarla | Ejemplo concreto |
|-----------|---------------|-----------------|
| **Tools** | El agente necesita ejecutar una acción con side effects | Crear un ticket, ejecutar una query de escritura, enviar una notificación |
| **Resources** | El agente necesita contexto estructurado de solo lectura | Schema de base de datos, documentación interna, lista de configuraciones disponibles |
| **Prompts** | Quieres estandarizar cómo el agente aborda una tarea | Template de code review de tu organización, prompt estándar para análisis de seguridad |

### Tres principios de seguridad MCP

| Principio | Qué significa en práctica |
|-----------|--------------------------|
| **Audita antes de instalar** | Revisa el código fuente de cualquier servidor MCP de terceros antes de añadirlo a tu configuración |
| **Mínimo privilegio** | El servidor solo debe tener acceso a las APIs y datos que sus tools realmente necesitan |
| **Logs de invocación** | En producción, registra todas las invocaciones de tools con usuario, timestamp y argumentos para auditoría |
