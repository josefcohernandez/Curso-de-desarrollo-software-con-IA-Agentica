# Frameworks de Orquestación Multi-Agente

## De Patrones a Producción

Los cinco patrones del fichero 01 (fan-out, pipeline, jerárquico, writer/reviewer, investigación paralela) describen cómo deberían coordinarse los agentes. Pero implementarlos desde cero — gestión de estado, reintentos, trazabilidad, handoffs entre agentes, human-in-the-loop — requiere resolver los mismos problemas de infraestructura una y otra vez.

Los frameworks de orquestación resuelven esa infraestructura repetitiva y te permiten centrarte en la lógica de coordinación de tu caso de uso específico.

> **Nota**: nombres de producto, versiones y estados GA/beta cambian rápido. Usa este capítulo como mapa conceptual y verifica la documentación oficial antes de comprometer una decisión de arquitectura.

**El ecosistema en 2025-2026**: el mercado se ha consolidado alrededor de dos tipos de herramientas. Los "LLM frameworks" de primera generación (LangChain clásico, LlamaIndex) abstraían llamadas a modelos y chains estáticos. Los "Agent SDKs" de segunda generación (LangGraph, OpenAI Agents SDK, Google ADK) modelan agentes autónomos con estado, ciclos y coordinación dinámica. El valor ya no está en envolver la API del modelo — está en orquestar agentes que colaboran.

---

## Los 6 Frameworks Principales

| Framework | Modelo de orquestación | Dependencia de LLM | Lenguajes | Madurez | Mejor caso de uso |
|-----------|------------------------|-------------------|-----------|---------|-------------------|
| **LangGraph** | Grafo de estado dirigido con ciclos | Agnóstico (OpenAI, Anthropic, Google, Ollama) | Python, JavaScript | GA desde oct. 2025 (v1.0) | Flujos complejos con condicionales, ciclos y estado persistente |
| **CrewAI** | Declarativo: agentes con roles, goals y backstory | Agnóstico | Python | Estable, muy activo | Equipos de agentes con roles fijos y colaboración estructurada |
| **OpenAI Agents SDK** | Handoffs entre agentes + guardrails + tracing | Optimizado para OpenAI, extensible | Python, TypeScript | GA desde mar. 2025 | Ecosistema OpenAI, aplicaciones con guardrails de seguridad nativos |
| **Google ADK** | Orquestación jerárquica nativa | Multi-modelo (Gemini, Anthropic, etc.) | Python | GA desde abr. 2025 (Google Cloud NEXT) | Ecosistema Google Cloud, integración con Vertex AI |
| **Semantic Kernel** | Planners que descomponen tareas en pasos | Multi-modelo (Azure, OpenAI, Hugging Face) | C#, Python, Java | Estable, 18K+ GitHub stars | Ecosistema Azure, aplicaciones enterprise multi-lenguaje |
| **AutoGen** | Conversación multi-agente, GroupChat | Multi-modelo | Python | Maduro pero menos adoptado en producción | Prototipado rápido, investigación, patrones conversacionales |

### LangGraph

Creado por LangChain como respuesta a las limitaciones de las chains lineales. Modela flujos como grafos dirigidos donde los nodos son funciones y las aristas pueden ser condicionales. El estado del grafo persiste entre pasos (checkpointing nativo), lo que permite reanudar flujos interrumpidos, implementar human-in-the-loop de forma natural y depurar paso a paso.

Alcanzó v1.0 estable en octubre de 2025 tras dos años de desarrollo. Es el framework más adoptado para flujos complejos y el más agnóstico respecto al modelo.

**Punto débil**: curva de aprendizaje. Modelar tu flujo como un grafo de estado requiere pensar diferente a las chains lineales. Para flujos simples, es sobredimensionado.

### CrewAI

Propone una abstracción de alto nivel: defines agentes con `role`, `goal` y `backstory` (contexto narrativo que guía al LLM), y los organizas en crews (equipos) con tareas asignadas. La orquestación ocurre de forma automática según el tipo de proceso (secuencial, jerárquico o paralelo).

Muy popular para prototipado rápido porque la configuración declarativa reduce el boilerplate. Menos flexible que LangGraph para grafos con ciclos arbitrarios o estado complejo.

**Punto débil**: la abstracción de "backstory" puede parecer artificial para equipos de ingeniería más rigurosos. Los flujos complejos requieren bypasses de la abstracción.

### OpenAI Agents SDK

Lanzado en marzo de 2025 reemplazando a Swarm (el experimento previo de OpenAI). Introduce tres conceptos clave: **agents** (instrucciones + herramientas), **handoffs** (transferencia de control entre agentes con contexto) y **guardrails** (validaciones de input/output que se ejecutan de forma paralela al agente). El tracing integrado con la plataforma OpenAI simplifica la depuración.

Funciona con cualquier modelo que siga la API de OpenAI, pero las optimizaciones de tracing y evaluación están pensadas para el ecosistema OpenAI.

**Punto débil**: menor flexibilidad para grafos con ciclos profundos comparado con LangGraph. La dependencia implícita del ecosistema OpenAI puede ser un freno si tu stack es multi-proveedor.

### Google Agent Development Kit (ADK)

Presentado en Google Cloud NEXT de abril de 2025. Diseñado para la orquestación jerárquica nativa: un agente orquestador delega en sub-agentes especializados que pueden a su vez delegar. Integración profunda con Vertex AI (ejecución gestionada, logging, evaluación) y con herramientas del ecosistema Google (Search, Maps, BigQuery).

Multi-modelo desde el día uno: soporta Gemini, Anthropic, modelos de Hugging Face y cualquier endpoint compatible.

**Punto débil**: la integración profunda con Vertex AI facilita el lock-in en Google Cloud. Menos adopción fuera del ecosistema Google comparado con LangGraph.

### Microsoft Semantic Kernel

El framework más maduro de la lista (empezó en 2023) y el más adoptado en entornos enterprise por su integración nativa con Azure. El modelo de orquestación se basa en "planners": el agente recibe un objetivo de alto nivel y el planner lo descompone automáticamente en una secuencia de "skills" (funciones) disponibles.

Soporta C#, Python y Java, lo que lo hace único en un ecosistema dominado por Python. Los 18K+ stars en GitHub reflejan una comunidad enterprise sólida.

**Punto débil**: la abstracción del planner puede ser impredecible. No siempre descompone las tareas como esperarías, y depurar el plan generado requiere experiencia con el framework.

### AutoGen (Microsoft)

El framework más orientado a la investigación. Su abstracción central es la conversación entre agentes: defines agentes con capacidades diferentes y los dejas conversar hasta llegar a un resultado. Los patrones GroupChat permiten múltiples agentes en la misma conversación con un moderador.

Excelente para prototipado y exploración de ideas. Menos adoptado en producción porque la naturaleza conversacional hace los flujos menos deterministas y más costosos.

**Punto débil**: menor control sobre el flujo exacto comparado con grafos de estado. Los costes pueden ser altos por la verbosidad de las conversaciones.

---

## Criterios de Selección

Antes de evaluar frameworks, responde estas preguntas:

| Pregunta | Respuesta | Framework recomendado |
|----------|-----------|----------------------|
| ¿Tu flujo tiene ciclos (el agente puede volver a pasos anteriores)? | Sí | LangGraph |
| ¿Necesitas definir agentes con roles narrativos de forma rápida? | Sí | CrewAI |
| ¿Tu stack es 100% OpenAI y necesitas guardrails nativos? | Sí | OpenAI Agents SDK |
| ¿Estás en Google Cloud y usas Vertex AI? | Sí | Google ADK |
| ¿Tu equipo trabaja con Azure y/o necesitas C# o Java? | Sí | Semantic Kernel |
| ¿Estás prototipando o investigando patrones conversacionales? | Sí | AutoGen |
| ¿Ninguna de las anteriores y quieres la opción más flexible? | Sí | LangGraph |

**LangGraph como base por defecto**: si no tienes restricciones de ecosistema, LangGraph es el punto de partida más razonable. Es agnóstico de modelo, tiene la mayor adopción en producción (excluyendo enterprise Azure) y la documentación más completa para casos de uso avanzados.

---

## Mapeo con los 5 Patrones del Módulo

| Patrón | LangGraph | CrewAI | OpenAI Agents SDK | Google ADK | Semantic Kernel |
|--------|-----------|--------|-------------------|------------|-----------------|
| **Fan-out** | `Send` API para bifurcación paralela en el grafo | `Process.parallel` con múltiples agentes | Múltiples agentes lanzados desde el orquestador | Sub-agentes en paralelo con ADK Runner | Invocación paralela de skills con el planner |
| **Pipeline** | Nodos en secuencia con aristas directas | `Process.sequential` con tareas encadenadas | Handoffs lineales A→B→C | Agentes en cadena con delegación secuencial | Planner descompone en pasos ordenados |
| **Jerárquico** | Nodo supervisor que lanza sub-grafos | `Process.hierarchical` con manager | Agente orquestador con handoffs condicionales | Arquitectura nativa del ADK (diseñado para esto) | Planner con skills anidados |
| **Writer/Reviewer** | Dos nodos con arista condicional para iteración | Dos agentes con roles writer y critic | Dos agentes con handoff bidireccional | Dos sub-agentes con feedback loop | Dos skills con validación entre pasos |
| **Investigación paralela** | `Send` API con N ramas paralelas que convergen | N agentes en paralelo con tarea de síntesis | N agentes paralelos + agente de consolidación | N sub-agentes + agente coordinador | N skills paralelos + step de consolidación |

---

## Ejemplo Práctico: Writer/Reviewer en LangGraph

El patrón Writer/Reviewer requiere un grafo con ciclo: el writer genera, el reviewer evalúa, y si la calidad no es suficiente, el writer vuelve a iterar. LangGraph es el framework natural para esto.

```python
from typing import TypedDict, Annotated
from langgraph.graph import StateGraph, END
from langgraph.graph.message import add_messages
from langchain_anthropic import ChatAnthropic

# Estado compartido entre los nodos del grafo
class DocumentState(TypedDict):
    content: str           # El documento generado
    feedback: str          # Feedback del reviewer
    iterations: int        # Contador de iteraciones
    approved: bool         # Si el reviewer aprobó

model = ChatAnthropic(model="claude-opus-4-5")

# Nodo writer: genera o refina el documento
def writer_node(state: DocumentState) -> DocumentState:
    if state["iterations"] == 0:
        prompt = f"Escribe un artículo técnico sobre: {state['content']}"
    else:
        prompt = (
            f"Tienes este artículo:\n{state['content']}\n\n"
            f"El reviewer ha dado este feedback:\n{state['feedback']}\n\n"
            "Aplica las correcciones y devuelve el artículo mejorado."
        )
    result = model.invoke(prompt)
    return {
        **state,
        "content": result.content,
        "iterations": state["iterations"] + 1
    }

# Nodo reviewer: evalúa la calidad
def reviewer_node(state: DocumentState) -> DocumentState:
    prompt = (
        f"Evalúa este artículo técnico:\n{state['content']}\n\n"
        "Responde con:\n"
        "APROBADO si el artículo tiene calidad suficiente para publicación.\n"
        "RECHAZADO: <feedback específico> si necesita mejoras."
    )
    result = model.invoke(prompt)
    response = result.content.strip()
    approved = response.startswith("APROBADO")
    feedback = "" if approved else response.replace("RECHAZADO:", "").strip()
    return {**state, "approved": approved, "feedback": feedback}

# Arista condicional: ¿aprobado o nueva iteración?
def should_continue(state: DocumentState) -> str:
    if state["approved"]:
        return "end"
    if state["iterations"] >= 3:  # Máximo 3 iteraciones
        return "end"
    return "writer"

# Construcción del grafo
workflow = StateGraph(DocumentState)
workflow.add_node("writer", writer_node)
workflow.add_node("reviewer", reviewer_node)

workflow.set_entry_point("writer")
workflow.add_edge("writer", "reviewer")
workflow.add_conditional_edges(
    "reviewer",
    should_continue,
    {"writer": "writer", "end": END}
)

graph = workflow.compile()

# Ejecutar el grafo
result = graph.invoke({
    "content": "patrones de diseño para sistemas de agentes de IA",
    "feedback": "",
    "iterations": 0,
    "approved": False
})

print(f"Iteraciones: {result['iterations']}")
print(f"Aprobado: {result['approved']}")
print(result["content"])
```

El ciclo `writer → reviewer → writer` se repite hasta que el reviewer aprueba o se alcanzan 3 iteraciones. La arista condicional `should_continue` controla el flujo. Sin LangGraph, implementar este estado persistente y las transiciones condicionales requeriría bastante boilerplate.

---

## Ejemplo Práctico 2: Fan-Out en CrewAI

CrewAI simplifica el fan-out con su proceso paralelo declarativo. Defines los agentes con sus roles y las tareas, y el framework gestiona la ejecución en paralelo.

```python
from crewai import Agent, Task, Crew, Process
from langchain_anthropic import ChatAnthropic

llm = ChatAnthropic(model="claude-opus-4-5")

# Definición declarativa de agentes especializados
security_agent = Agent(
    role="Auditor de Seguridad",
    goal="Identificar vulnerabilidades de seguridad en el código",
    backstory="Experto en OWASP Top 10 y seguridad de APIs REST",
    llm=llm,
    verbose=False
)

performance_agent = Agent(
    role="Analista de Performance",
    goal="Detectar cuellos de botella y código ineficiente",
    backstory="Especialista en profiling y optimización de aplicaciones Node.js",
    llm=llm,
    verbose=False
)

docs_agent = Agent(
    role="Revisor de Documentación",
    goal="Verificar que el código está bien documentado y los tipos son correctos",
    backstory="Defensor de la calidad del código y la mantenibilidad a largo plazo",
    llm=llm,
    verbose=False
)

# Tareas independientes (fan-out): cada agente analiza el mismo código
code_to_review = open("src/api/payments.ts").read()

security_task = Task(
    description=f"Analiza este código en busca de vulnerabilidades:\n{code_to_review}",
    expected_output="Lista de vulnerabilidades encontradas con severidad y recomendación de fix",
    agent=security_agent
)

performance_task = Task(
    description=f"Analiza este código en busca de problemas de performance:\n{code_to_review}",
    expected_output="Lista de cuellos de botella con impacto estimado y refactoring recomendado",
    agent=performance_agent
)

docs_task = Task(
    description=f"Revisa la documentación y tipos de este código:\n{code_to_review}",
    expected_output="Lista de funciones sin documentar, tipos incorrectos o ambiguos",
    agent=docs_agent
)

# Crew con proceso paralelo: los 3 agentes trabajan simultáneamente
crew = Crew(
    agents=[security_agent, performance_agent, docs_agent],
    tasks=[security_task, performance_task, docs_task],
    process=Process.hierarchical,  # hierarchical para fan-out coordinado
    verbose=True
)

results = crew.kickoff()
print(results)
```

Comparado con la implementación bash de fan-out del fichero 03, CrewAI gestiona automáticamente el paralelismo, los reintentos y la agregación de resultados. El coste es la dependencia del framework y menor control sobre el comportamiento exacto de cada agente.

---

## La Transición: de LLM Frameworks a Agent SDKs

La evolución del ecosistema sigue un patrón claro:

| Generación | Ejemplos | Abstracción central | Limitación |
|------------|---------|---------------------|------------|
| **Primera (2022-2023)** | LangChain clásico, LlamaIndex | Chains lineales, wrappers de API | No modelan ciclos ni estado persistente entre agentes |
| **Segunda (2024-2025)** | LangGraph, CrewAI, OpenAI Agents SDK | Agentes autónomos con estado y coordinación | Mayor complejidad, ecosistemas fragmentados |

**Por qué este cambio importa**: envolver la API de un modelo es una commodity. Cualquier equipo puede hacerlo en horas. Orquestar agentes que colaboran, mantienen estado coherente, se recuperan de fallos y escalan horizontalmente es el problema difícil. Ahí está el valor diferencial en 2025-2026.

Los frameworks de segunda generación lo reconocen: no intentan abstraer el modelo, intentan abstraer la coordinación.

---

## Cuándo No Usar un Framework

Los frameworks añaden una dependencia, una curva de aprendizaje y un nivel de abstracción que puede dificultar el debugging. No siempre se justifican.

| Situación | Recomendación |
|-----------|--------------|
| Una sola tarea con un agente | `claude -p "..."` directamente, sin framework |
| Script de una vez (no recurrente) | Bash + Claude CLI, más simple y sin dependencias |
| Prototipo para validar una idea | Script Python simple con llamadas directas a la API |
| Equipo sin experiencia en el framework | El coste de aprendizaje supera el beneficio hasta las 3-4 semanas |
| Flujo lineal sin ciclos ni estado complejo | LangChain clásico o simplemente una función Python |
| Orquestación recurrente con estado y ciclos | Aquí sí tiene sentido un framework |

**Regla práctica**: si puedes implementar tu flujo en menos de 100 líneas de Python sin un framework, hazlo. Introduce un framework cuando el boilerplate de gestión de estado, reintentos y trazabilidad supere el coste de aprender el framework.

---

## Resumen: Guía de Selección Rápida

| Si tu situación es... | Usa... |
|-----------------------|--------|
| Flujos complejos con ciclos, estado y agnóstico de modelo | LangGraph |
| Equipos de agentes con roles definidos, prototipado rápido | CrewAI |
| Stack OpenAI, necesitas guardrails y tracing nativo | OpenAI Agents SDK |
| Google Cloud + Vertex AI, orquestación jerárquica | Google ADK |
| Azure, enterprise, necesitas C# o Java | Semantic Kernel |
| Investigación, prototipado conversacional | AutoGen |
| Tarea simple, un agente, script de una vez | Sin framework |
