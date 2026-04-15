# Mutation Testing y Calidad de Tests

## Introducción

Tienes un módulo con 95% de code coverage. Parece bien testeado, pero... ¿lo está realmente? Coverage mide qué líneas se **ejecutan** durante los tests, no si los tests **detectarían** un bug. Mutation testing responde la pregunta correcta: si introduzco un error en el código, ¿mis tests lo encuentran?

---

## Qué es Mutation Testing

### El concepto

1. Se toma el código fuente original
2. Se introduce una pequeña modificación (un **mutante**): cambiar `>` por `>=`, eliminar una condición, cambiar un `+` por un `-`
3. Se ejecutan los tests contra el código mutado
4. Si algún test falla, el mutante fue **detectado** (killed)
5. Si todos los tests pasan, el mutante **sobrevivió** -- hay un gap en tu suite de tests

### Mutation score

```text
Mutation Score = (Mutantes detectados / Total de mutantes) x 100
```

Un mutation score del 80% significa que el 20% de las mutaciones posibles no serían detectadas por tus tests. Es una métrica más fiable que coverage.

### Coverage vs Mutation Score

| Métrica | Qué mide | Limitación |
|---------|----------|------------|
| **Coverage** | Líneas ejecutadas durante tests | Una línea puede ejecutarse sin verificar su resultado |
| **Mutation score** | Tests que detectan cambios en el código | Más lento de calcular (requiere ejecutar tests N veces) |

Ejemplo concreto: este test tiene 100% coverage del método pero no detecta una mutación:

```python
def calculate_discount(price, percentage):
    if percentage > 50:
        raise ValueError("Discount cannot exceed 50%")
    return price * (1 - percentage / 100)

# Test con 100% coverage pero mutation score bajo
def test_calculate_discount():
    result = calculate_discount(100, 10)
    assert result is not None  # Pasa con CUALQUIER valor de retorno
```

Si un mutante cambia `1 - percentage / 100` por `1 + percentage / 100`, el test sigue pasando. El fix:

```python
def test_calculate_discount():
    result = calculate_discount(100, 10)
    assert result == 90.0  # Ahora detecta la mutación
```

---

## Herramientas de Mutation Testing

| Herramienta | Lenguaje | Instalación | Comando |
|-------------|----------|-------------|---------|
| **Stryker** | JS/TS | `npm install --save-dev @stryker-mutator/core` | `npx stryker run` |
| **mutmut** | Python | `pip install mutmut` | `mutmut run` |
| **pitest** | Java | Plugin Maven/Gradle | `mvn org.pitest:pitest-maven:mutationCoverage` |
| **cargo-mutants** | Rust | `cargo install cargo-mutants` | `cargo mutants` |

### Ejemplo con mutmut (Python)

```bash
# Ejecutar mutation testing sobre el módulo validators
mutmut run --paths-to-mutate=src/validators/

# Ver resultados: cuántos mutantes sobrevivieron
mutmut results

# Inspeccionar un mutante sobreviviente específico
mutmut show 42
```

### Ejemplo con Stryker (TypeScript)

```bash
# Inicializar Stryker en el proyecto
npx stryker init

# Ejecutar mutation testing
npx stryker run

# El reporte HTML se genera en reports/mutation/
```

---

## Usar IA para Mutation Testing Más Inteligente

Las herramientas automáticas generan mutaciones mecánicas (cambiar operadores, eliminar líneas). Un agente de IA puede generar **mutaciones semánticas** más sutiles y realistas.

### Prompt para mutaciones inteligentes

```text
Genera 5 mutaciones sutiles del código en src/services/payment.ts que 
deberían ser detectadas por los tests. No hagas mutaciones triviales 
como cambiar + por -. Busca mutaciones que:
- Cambien lógica de negocio de forma sutil (edge case de redondeo, 
  condición de borde)
- Alteren el orden de operaciones sin romper la compilación
- Modifiquen valores por defecto o constantes
Si algún test actual no detecta la mutación, escribe el test que falta.
```

### Tipos de mutaciones que la IA genera mejor

| Tipo | Ejemplo mecánico | Ejemplo inteligente (IA) |
|------|------------------|--------------------------|
| **Condición de borde** | `>` por `>=` | Cambiar el orden de validaciones para que un edge case se salte |
| **Valor por defecto** | `0` por `1` | Cambiar el timezone por defecto de UTC a local |
| **Lógica de negocio** | Eliminar línea | Invertir el orden de aplicación de descuento e impuesto |
| **Concurrencia** | N/A | Eliminar un lock que solo falla bajo carga |

---

## Workflow Completo de Mutation Testing

1. **Ejecuta tus tests** y verifica que pasan (baseline verde)
2. **Ejecuta mutation testing** con la herramienta de tu stack
3. **Revisa los mutantes sobrevivientes**: ¿son bugs reales que tus tests no cubren?
4. **Pide al agente** que genere los tests faltantes para los mutantes sobrevivientes
5. **Verifica** que los nuevos tests detectan las mutaciones
6. **Itera**: si el mutation score sube a > 85%, tu suite es sólida

### Cuándo usar mutation testing

- Antes de un release importante
- En módulos críticos (pagos, autenticación, autorización)
- Cuando el coverage es alto pero sigues encontrando bugs en producción
- Como parte de la revisión de calidad de tests en code review

---

## Resumen

- **Coverage mide cantidad** (qué líneas se ejecutan), **mutation score mide calidad** (qué errores se detectan)
- Las herramientas automáticas (Stryker, mutmut, pitest) generan mutaciones mecánicas; la IA genera mutaciones semánticas más realistas
- Mutation testing es costoso computacionalmente: úsalo en módulos críticos, no en todo el codebase
- Un mutation score > 85% en módulos críticos es un indicador sólido de calidad de tests
- El workflow ideal: coverage para el día a día, mutation testing para validación profunda periódica
