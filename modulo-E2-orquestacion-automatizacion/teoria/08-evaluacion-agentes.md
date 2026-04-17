# Evaluación de Agentes (Evals)

## Testing de Prompts vs. Evaluación de Agentes

El fichero 04 de este módulo cubre el testing de prompts: verificar que un prompt produce output con la estructura correcta, que no se rompe ante inputs adversariales, que el JSON es válido. Es el equivalente al unit testing aplicado a prompts individuales.

La evaluación de agentes, o **evals**, opera a un nivel diferente. No pregunta "¿el output tiene el formato correcto?" sino "¿el agente completó la tarea que debía completar?". Es la diferencia entre unit testing y end-to-end testing aplicada a sistemas de IA.

| Dimensión | Testing de prompts (fichero 04) | Evaluación de agentes (evals) |
|-----------|--------------------------------|-------------------------------|
| Unidad evaluada | Un prompt, un output | Un agente o pipeline completo |
| Pregunta central | ¿El formato es correcto? | ¿La tarea se completó? |
| Condiciones | Controladas, inputs fijos | Realistas, multi-step |
| Evaluador | Assertions en código | Script, LLM-as-judge, humano |
| Velocidad | Segundos | Minutos a horas |
| Analogía en software | Unit test | End-to-end test |

Ambos son necesarios y se complementan. Los tests de prompts son el primer filtro — rápidos, baratos, automatizables. Las evals son el segundo nivel — más lentas, más costosas, pero las únicas que confirman que el sistema funciona como se desea en condiciones reales.

**Por qué importa ahora**: según el informe State of AI Agents 2026 de LangChain, el 57% de las organizaciones ya tienen agentes en producción. En ese mismo estudio, la calidad es la barrera número uno para la adopción: los equipos no saben si sus agentes funcionan correctamente ni cómo medirlo. Las evals son la respuesta a ese problema.

---

## Qué Son las Evals

Las **evaluaciones** son mediciones sistemáticas del rendimiento de un agente o sistema LLM. A diferencia de los tests de software convencionales, donde el output correcto es determinista, las evals trabajan con outputs probabilísticos y miden grados de calidad, no solo correcto/incorrecto.

Una eval típica responde a preguntas como:

- ¿El agente completa la tarea end-to-end cuando se le da un input real?
- ¿Usa las herramientas correctas con los parámetros correctos?
- ¿Mantiene coherencia a lo largo de una conversación multi-turn de 10 pasos?
- ¿Produce resultados correctos para los distintos tipos de input que verá en producción?
- ¿Su rendimiento se degrada cuando el contexto es ambiguo o incompleto?

Las evals no son opcionales una vez que un agente está en producción. Son el mecanismo que te permite detectar regresiones cuando cambias el modelo, el prompt o la arquitectura del sistema.

---

## Las 3 Categorías de Métricas de Agentes

### Task Completion Rate

El porcentaje de tareas que el agente completa correctamente end-to-end. Es la métrica reina porque mide directamente lo que importa: si el agente hace lo que se le pide.

Un task completion rate del 85% significa que en 15 de cada 100 tareas el agente falla, produce un resultado incorrecto o necesita intervención humana. Para un pipeline de code review en CI/CD, eso puede ser tolerable. Para un agente que hace deployments en producción, no lo es.

Para calcularla, necesitas:

1. Un dataset de tareas representativo de tu caso de uso
2. Un criterio claro de "completada correctamente" para cada tarea
3. Un evaluador (script, LLM-as-judge o humano) que aplique ese criterio

### Tool Use Accuracy

¿El agente elige la herramienta correcta y la llama con los parámetros correctos? Esta métrica mide la calidad de la orquestación, no solo del output final.

Un agente puede producir el resultado correcto por casualidad incluso usando las herramientas de forma subóptima. Pero un agente que usa herramientas incorrectamente o con parámetros erróneos es frágil: funciona en condiciones normales y falla en edge cases.

Para medirla, necesitas trazabilidad de las llamadas a herramientas (tool call logs) y un conjunto de trayectorias esperadas para un subconjunto de tus inputs de eval.

### Context Retention

¿El agente mantiene coherencia a lo largo de una conversación multi-turn? Mide la degradación del razonamiento conforme crece el contexto.

En conversaciones largas (>20 turnos), los agentes pueden olvidar decisiones anteriores, contradecirse o perder el hilo del objetivo original. Esta métrica es crítica para agentes de soporte, tutores o cualquier sistema donde el usuario mantiene una conversación extendida.

### Tabla de Métricas Complementarias

| Métrica | Qué mide | Cuándo es crítica |
|---------|----------|-------------------|
| **Task completion rate** | Tareas completadas correctamente / total | Siempre |
| **Tool use accuracy** | Herramienta correcta + parámetros correctos | Agentes con muchas herramientas |
| **Context retention** | Coherencia en conversaciones multi-turn | Agentes conversacionales |
| **Latency (p50/p95)** | Tiempo hasta completar la tarea | Agentes en tiempo real |
| **Cost per task** | Coste en tokens/USD por tarea completada | Producción a escala |
| **Error recovery rate** | Recuperaciones exitosas / errores encontrados | Automatización desatendida |
| **Human intervention rate** | Veces que el agente pide ayuda humana / total | Flujos semi-automáticos |

---

## LLM-as-Judge

Evaluar outputs de agentes a escala requiere un evaluador que no sea el humano. La solución dominante en 2025-2026 es el **LLM-as-judge**: usar un LLM como evaluador automático de los outputs de otro LLM.

### Cómo Funciona

El LLM evaluador recibe tres elementos:

1. El input original que recibió el agente (la tarea, el contexto)
2. El output o resultado que produjo el agente
3. Un rubric (criterio de evaluación con dimensiones específicas y escala de puntuación)

A partir de esos tres elementos, el LLM evaluador produce una puntuación y un razonamiento. El proceso es automático, reproducible y escala sin necesidad de evaluadores humanos.

```python
# Ejemplo conceptual de LLM-as-judge para evaluar code review
def evaluate_code_review(spec: str, generated_code: str, llm_judge) -> dict:
    eval_prompt = f"""
Evalúa si el siguiente código generado por un agente cumple con la especificación.

ESPECIFICACIÓN:
{spec}

CÓDIGO GENERADO:
{generated_code}

Criterios de evaluación:
1. ¿Implementa todos los requisitos de la spec? (0-5)
2. ¿El código compila/ejecuta sin errores? (0-5)
3. ¿Sigue las convenciones del proyecto? (0-5)
4. ¿Maneja edge cases mencionados en la spec? (0-5)

Responde exclusivamente en JSON con este formato:
{{"scores": [N, N, N, N], "total": N, "passed": true/false, "reasoning": "..."}}

Un resultado se considera aprobado si total >= 14 sobre 20.
"""
    response = llm_judge.invoke(eval_prompt)
    return json.loads(response.content)


# Uso en un pipeline de eval
results = []
for task in eval_dataset:
    agent_output = run_agent(task["input"])
    evaluation = evaluate_code_review(
        spec=task["spec"],
        generated_code=agent_output,
        llm_judge=judge_model
    )
    results.append({
        "task_id": task["id"],
        "passed": evaluation["passed"],
        "total": evaluation["total"],
        "reasoning": evaluation["reasoning"]
    })

completion_rate = sum(r["passed"] for r in results) / len(results)
print(f"Task completion rate: {completion_rate:.1%}")
```

### Ventajas

- **Escalable**: evalúa cientos o miles de outputs sin intervención humana
- **Reproducible**: el mismo rubric produce evaluaciones consistentes
- **Más barato que evaluación humana**: en la mayoría de casos, entre 10x y 100x más barato
- **Rápido de implementar**: no requiere infraestructura especial, solo una llamada a la API

### Limitaciones

- **Sesgo de autopreferencia**: un LLM tiende a puntuar mejor outputs con un estilo similar al suyo. Si el agente y el juez son el mismo modelo, las evaluaciones son menos fiables. Usa un modelo diferente o una versión diferente como juez cuando sea posible.
- **Sensibilidad al formato**: pequeñas diferencias en el formato del output pueden alterar la puntuación, aunque el contenido sea equivalente.
- **Ceguera de dominio**: el juez no detecta errores que requieren conocimiento especializado que el LLM no tiene. Un agente que genera código de control de reactores nucleares no puede ser evaluado por un LLM de propósito general.
- **No es verificación formal**: el LLM juez puede alucinarse sobre si el código compila o si la lógica es correcta. Para afirmaciones verificables (sintaxis, ejecución), combina LLM-as-judge con verificación programática.

En Cursor/Copilot no existe un mecanismo equivalente nativo. Si usas esas herramientas, el LLM-as-judge debe implementarse como un paso separado en tu pipeline de CI/CD, independiente del IDE.

---

## Framework de Evaluación en 3 Niveles

Al igual que en software convencional, la evaluación de agentes tiene niveles de granularidad. Usar solo uno de los tres niveles da una imagen incompleta.

### Nivel 1: Unit Evals

Evalúa un paso individual del agente o pipeline. No mide si la tarea completa se completa; mide si una decisión concreta es correcta.

Preguntas que responde:
- ¿El agente eligió la herramienta correcta para este paso?
- ¿El output de este nodo del pipeline tiene la estructura JSON correcta?
- ¿El agente clasificó correctamente este input como "requiere escalación humana"?

Es rápido, barato y altamente automatizable. Es donde viven las técnicas del fichero 04 (assertions, golden tests, adversarial testing, regression testing).

### Nivel 2: Integration Evals

Evalúa un flujo completo de múltiples pasos. No verifica pasos aislados sino que el pipeline entero funciona correctamente de extremo a extremo dentro de un escenario controlado.

Preguntas que responde:
- ¿El pipeline Writer/Reviewer completa el ciclo correctamente?
- ¿El agente de code review detecta la vulnerabilidad, genera el feedback y el desarrollador puede entender la corrección sugerida?
- ¿El merge resultado del pipeline es funcional (compila, pasa los tests)?

Más lento que los unit evals, requiere setup del entorno (repositorio, base de datos de prueba, etc.) y evaluadores más sofisticados.

### Nivel 3: End-to-End Evals

Evalúa la tarea completa en condiciones realistas o cercanas a ellas. No hay simplificaciones del entorno: el agente trabaja con un repositorio real, un issue real, y el resultado se verifica de forma objetiva.

Preguntas que responde:
- Dado este repositorio Python y este issue de GitHub, ¿el agente resuelve el bug correctamente?
- Dado este codebase legacy, ¿el agente migra la feature completa sin romper los tests existentes?

Es el nivel más costoso y lento, pero el único que valida el rendimiento real del agente en producción.

### Tabla Comparativa

| Nivel | Qué mide | Velocidad | Coste por eval | Ejemplo concreto |
|-------|----------|-----------|----------------|------------------|
| **1 - Unit** | Un paso, una decisión | Segundos | Bajo (< 0.01 USD) | ¿El clasificador de issues devuelve el label correcto? |
| **2 - Integration** | Un flujo completo | Minutos | Medio (0.10-1 USD) | ¿El pipeline Writer/Reviewer produce código que compila? |
| **3 - End-to-end** | Tarea real completa | 10-60 min | Alto (1-20 USD) | ¿El agente cierra correctamente este issue de GitHub? |

**Recomendación de distribución**: 70% unit evals (muchos, baratos, automatizados), 20% integration evals (flujos críticos), 10% end-to-end evals (muestra representativa del caso de uso real).

---

## SWE-bench: El Benchmark Estándar

SWE-bench (Software Engineering Benchmark) es el benchmark más adoptado para medir la capacidad de los agentes de resolver tareas de ingeniería de software. Fue publicado en 2023 y se ha convertido en el estándar de facto para comparar agentes de código.

### Qué Es

Un dataset de 2.294 issues reales extraídos de repositorios Python open-source populares (Django, Flask, scikit-learn, etc.). Para cada issue, el benchmark incluye el repositorio en el estado anterior al fix, la descripción del issue y un conjunto de tests que verifican si el issue fue resuelto correctamente.

El agente recibe el repositorio y el issue. Se considera que resolvió el issue si, después de sus cambios, los tests del benchmark pasan.

### Variantes

| Variante | Tamaño | Descripción | Uso recomendado |
|----------|--------|-------------|-----------------|
| **SWE-bench** | 2.294 issues | Dataset completo original | Comparaciones exhaustivas, investigación |
| **SWE-bench Lite** | 300 issues | Subconjunto curado para evaluación rápida | Iteración rápida durante desarrollo |
| **SWE-bench Verified** | 500 issues | Subconjunto con verificación humana de la calidad del issue | Comparaciones publicables, más fiable que el original |
| **SWE-bench Pro** | ~500 issues | Problemas más difíciles, multi-fichero, más testing (Scale AI, 2026) | Diferenciar capacidades de modelos frontier |

### Resultados Actuales (Abril 2026)

En SWE-bench Verified, Claude Opus 4.5 alcanza el 77.2% de task completion rate. Claude Opus 4.6 se sitúa alrededor del 80.9%. En SWE-bench Pro, donde los problemas son significativamente más complejos y multi-fichero, el mejor sistema disponible se sitúa en torno al 23%, lo que indica que resolver programáticamente issues complejos sigue siendo un problema abierto.

### Cómo Interpretar los Resultados

SWE-bench no mide "inteligencia" en sentido general. Mide la capacidad práctica de resolver issues concretos en proyectos Python open-source. Un agente que alcanza el 80% en SWE-bench Verified:

- Es muy capaz en issues bien definidos con tests claros
- Trabaja bien con el estilo de código de repos open-source populares
- No necesariamente rinde igual en tu codebase legacy en Java o en issues mal especificados

**Limitaciones del benchmark**:
- Sesgado hacia Python; los agentes optimizados para Python pueden no generalizar
- Los issues son autocontenidos; no mide capacidad de razonamiento sobre proyectos muy grandes
- Las métricas de repos open-source populares no representan el código de producción de una empresa media

Úsalo como referencia para seleccionar modelos base, no como garantía de rendimiento en tu caso de uso específico.

---

## Diseñando tus Propias Evals

Para la mayoría de equipos, SWE-bench no es suficiente: necesitas evals que reflejen tu caso de uso específico. El proceso tiene seis pasos.

### Paso 1: Define la Tarea

Antes de medir, necesitas una definición precisa de lo que el agente debe lograr. "El agente debe revisar PRs" es demasiado vago. "El agente debe identificar vulnerabilidades de seguridad en diffs de código Python, categorizar cada vulnerabilidad por tipo (OWASP) y severidad, y proponer un fix concreto" es una definición evaluable.

### Paso 2: Crea un Dataset de Prueba

El dataset debe ser representativo de los inputs reales que verá el agente en producción.

```python
# Estructura de un dataset de eval para code review
eval_dataset = [
    {
        "id": "cr-001",
        "category": "happy_path",
        "input": {
            "diff": CLEAN_BUGFIX_DIFF,
            "context": "Feature branch para resolver issue #123"
        },
        "expected": {
            "approved": True,
            "max_issues": 1,
            "no_critical": True
        }
    },
    {
        "id": "cr-002",
        "category": "security",
        "input": {
            "diff": SQL_INJECTION_DIFF,
            "context": "Nuevo endpoint de búsqueda"
        },
        "expected": {
            "approved": False,
            "has_critical": True,
            "vulnerability_type": "sql_injection"
        }
    },
    {
        "id": "cr-003",
        "category": "edge_case",
        "input": {
            "diff": EMPTY_DIFF,
            "context": "PR con solo cambios de whitespace"
        },
        "expected": {
            "approved": False,
            "reason_includes": "no changes"
        }
    }
]
```

Apunta a un mínimo de 20-50 ejemplos para evals de nivel 1, y al menos 10-20 para integration evals. Los end-to-end evals con 5-10 casos ya dan señal suficiente para iterar.

### Paso 3: Define Criterios de Éxito

Para cada input del dataset, define qué cuenta como "completado correctamente". Hay dos enfoques:

- **Criterio objetivo**: el output es correcto si pasa una verificación programática (el código compila, los tests pasan, el JSON tiene los campos requeridos con los valores correctos).
- **Criterio por rubric**: el output es correcto según un LLM-as-judge que aplica un rubric con dimensiones específicas y umbrales de puntuación definidos.

Combina ambos cuando sea posible: verifica primero lo que se puede verificar programáticamente, luego aplica LLM-as-judge solo para las dimensiones que requieren juicio semántico.

### Paso 4: Elige el Evaluador

| Tipo de evaluador | Cuándo usarlo | Coste | Escalabilidad |
|-------------------|--------------|-------|---------------|
| **Script Python** | Output verificable programáticamente (JSON válido, código que compila) | Muy bajo | Alta |
| **LLM-as-judge** | Calidad semántica, coherencia, relevancia | Medio | Alta |
| **Humano** | Calibración inicial del LLM-as-judge, casos ambiguos, dominios especializados | Alto | Baja |
| **Test suite del proyecto** | El agente genera código; los tests existentes validan corrección | Bajo | Media |

### Paso 5: Ejecuta y Mide

```python
def run_eval_suite(dataset: list, agent_fn, evaluator_fn) -> dict:
    results = []

    for task in dataset:
        # Ejecutar el agente
        agent_output = agent_fn(task["input"])

        # Evaluar el output
        evaluation = evaluator_fn(
            input=task["input"],
            output=agent_output,
            expected=task["expected"]
        )

        results.append({
            "id": task["id"],
            "category": task["category"],
            "passed": evaluation["passed"],
            "score": evaluation.get("score"),
            "notes": evaluation.get("notes")
        })

    # Calcular métricas agregadas
    total = len(results)
    passed = sum(r["passed"] for r in results)
    by_category = {}
    for r in results:
        cat = r["category"]
        if cat not in by_category:
            by_category[cat] = {"total": 0, "passed": 0}
        by_category[cat]["total"] += 1
        by_category[cat]["passed"] += r["passed"]

    return {
        "task_completion_rate": passed / total,
        "total_tasks": total,
        "passed_tasks": passed,
        "by_category": by_category,
        "failures": [r for r in results if not r["passed"]]
    }
```

### Paso 6: Itera sobre el Sistema, No sobre las Evals

Este paso es el más importante y el más frecuentemente ignorado. Cuando una eval falla, la solución es mejorar el agente (el prompt, la arquitectura, el modelo), no ajustar los criterios de la eval para que pase.

Modificar las evals para que el agente las pase sin mejorar el sistema es el equivalente a eliminar los tests que fallan en lugar de arreglar el bug. El resultado es un sistema que "pasa las evals" pero falla en producción.

---

## Relación con Testing de Prompts

Las 4 técnicas del fichero 04 son la base del Nivel 1 (unit evals) en el framework de 3 niveles. Se integran de la siguiente forma:

| Técnica (fichero 04) | Nivel en el framework | Rol en el pipeline de eval |
|---------------------|-----------------------|---------------------------|
| **Assertions sobre output** | Nivel 1 - Unit eval | Verificación programática de estructura y valores. Primer filtro, más rápido. |
| **Golden tests** | Nivel 1 - Unit eval | Detección de regresiones en el comportamiento esperado. Complementa las assertions. |
| **Adversarial testing** | Nivel 1 - Unit eval | Verificación de robustez ante inputs hostiles. Cubre los edge cases peligrosos. |
| **Regression testing** | Nivel 1 y 2 | En Nivel 1 verifica pasos individuales; en Nivel 2, flujos completos ante cambios. |

La progresión natural es: unit evals (fichero 04) -> integration evals (flujos multi-paso) -> end-to-end evals (tareas reales). No hay que elegir entre los niveles: se ejecutan en cascada, de más barato a más costoso.

---

## Resumen

| Nivel | Qué evalúa | Velocidad | Cuándo ejecutar |
|-------|-----------|-----------|-----------------|
| **Unit evals** | Un paso o decisión del agente | Segundos | En cada commit, de forma automática |
| **Integration evals** | Un flujo completo multi-paso | Minutos | En cada merge a main, flujos críticos |
| **End-to-end evals** | La tarea completa en condiciones reales | 10-60 min | Antes de un cambio de modelo o arquitectura; periódicamente en producción |

Las evals son el mecanismo que convierte la pregunta "¿funciona mi agente?" en una respuesta medible y repetible. Sin ellas, la calidad del agente es una suposición. Con ellas, es un dato.
