# Testing Generativo con IA

## Introducción

Los tests unitarios tradicionales validan casos concretos que el desarrollador imagina. Pero los bugs más peligrosos viven en los casos que nadie imaginó. El testing generativo invierte el enfoque: en lugar de escribir ejemplos, defines **propiedades** que siempre deben cumplirse, y dejas que un framework (o un agente de IA) genere miles de inputs para intentar romperlas.

---

## Property-Based Testing

### El concepto

En vez de escribir `assert sum(2, 3) == 5`, defines la propiedad: "para cualesquiera dos números, `sum(a, b)` debe ser igual a `sum(b, a)`" (conmutatividad). El framework genera automáticamente cientos de pares `(a, b)` para verificar la propiedad.

### Frameworks principales

| Framework | Lenguaje | Instalación | Característica clave |
|-----------|----------|-------------|----------------------|
| **Hypothesis** | Python | `pip install hypothesis` | Shrinking automático de contraejemplos |
| **fast-check** | JS/TS | `npm install fast-check` | Integración nativa con Jest y Vitest |
| **QuickCheck** | Haskell | Incluido en Haskell Platform | El framework original, referencia teórica |
| **jqwik** | Java/Kotlin | Dependencia Maven/Gradle | Integración con JUnit 5 |

### Ejemplo con Hypothesis (Python)

```python
from hypothesis import given, strategies as st
from my_module import serialize, deserialize

@given(st.text())
def test_roundtrip_serialization(data):
    """La serialización seguida de deserialización debe devolver el original."""
    assert deserialize(serialize(data)) == data

@given(st.integers(), st.integers())
def test_sum_is_commutative(a, b):
    """La suma debe ser conmutativa."""
    assert sum_func(a, b) == sum_func(b, a)

@given(st.lists(st.integers()))
def test_sort_preserves_length(lst):
    """Ordenar una lista no debe cambiar su longitud."""
    assert len(sorted(lst)) == len(lst)
```

### Ejemplo con fast-check (TypeScript)

```typescript
import fc from "fast-check";
import { serialize, deserialize } from "./my-module";

test("roundtrip serialization preserves data", () => {
  fc.assert(
    fc.property(fc.string(), (data) => {
      expect(deserialize(serialize(data))).toBe(data);
    })
  );
});

test("array concatenation is associative", () => {
  fc.assert(
    fc.property(fc.array(fc.integer()), fc.array(fc.integer()), (a, b) => {
      expect([...a, ...b].length).toBe(a.length + b.length);
    })
  );
});
```

### Cómo usar el agente de IA para property-based testing

El agente es especialmente útil para **identificar propiedades** que un humano podría no pensar. Prompt recomendado:

```text
Genera property-based tests para el módulo src/validators/. 
Define propiedades que siempre deben cumplirse, no ejemplos concretos.
Incluye propiedades de: roundtrip, idempotencia, invariantes de dominio 
y relaciones entre funciones.
Usa Hypothesis para Python.
```

---

## Fuzzing Asistido por IA

### Qué es fuzzing

Fuzzing es la generación de inputs malformados, extremos y adversariales para encontrar errores que no aparecen con datos "normales". El agente de IA puede generar fuzzing inputs más inteligentes que los generadores aleatorios porque entiende la estructura esperada del input.

### Casos de uso principales

- **Parsing**: enviar JSON malformado, XML con entidades infinitas, CSV con delimitadores ambiguos
- **Validación de formularios**: campos con longitud extrema, caracteres Unicode, inyección SQL/XSS
- **APIs públicas**: payloads con tipos incorrectos, campos extra, campos faltantes, valores nulos
- **Serialización**: datos que al serializar y deserializar cambian de tipo o valor

### Prompt para fuzzing con IA

```text
Genera 20 inputs de fuzzing para el endpoint POST /api/users. 
Incluye:
- Caracteres Unicode especiales y emojis en el campo nombre
- Payloads de más de 1MB
- Tipos incorrectos (número donde espera string, array donde espera objeto)
- Intentos de inyección SQL en el campo email
- Campos faltantes (sin nombre, sin email, sin ninguno)
- Valores en el límite (string vacío, número 0, número negativo)
Para cada input, indica qué comportamiento esperas del servidor.
```

---

## Generación de Tests desde Specs

Si tu proyecto tiene una especificación (SPEC.md, historias Gherkin, criterios de aceptación en tickets), el agente puede generar tests que validen directamente esa especificación.

### Workflow

1. Escribe la spec en formato estructurado (Gherkin, criterios de aceptación, o SPEC.md)
2. Pide al agente que genere tests a partir de la spec
3. Revisa que los tests capturen la **intención** del requisito, no solo la implementación actual
4. Ejecuta los tests y verifica que pasan con la implementación correcta

### Prompt para generación desde spec

```text
Lee el archivo SPEC.md y genera tests para cada criterio de aceptación.
Usa pytest con fixtures. Cada test debe:
- Referenciar el criterio de aceptación que valida (en un comentario)
- Ser independiente de los demás
- Tener nombre descriptivo que explique el comportamiento esperado
No generes tests para detalles de implementación, solo para comportamiento.
```

### Ventaja clave

El humano se concentra en definir **qué** debe hacer el software (la spec). El agente genera **cómo** verificarlo (los tests). El humano revisa que los tests realmente validen la intención.

---

## Resumen

| Técnica | Qué define el humano | Qué genera la IA | Riesgo principal |
|---------|----------------------|-------------------|------------------|
| Property-based | Propiedades invariantes | Inputs para verificarlas | Propiedades triviales |
| Fuzzing | Categorías de ataque | Inputs malformados concretos | Falsos positivos |
| Desde specs | Especificación formal | Tests completos | Tests que validan implementación, no intención |

La clave en los tres casos es la **revisión humana**: el agente genera, tú validas que las propiedades, los fuzzing inputs y los tests capturan lo que realmente importa.
