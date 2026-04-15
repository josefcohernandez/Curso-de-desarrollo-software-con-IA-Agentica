# Escenario: Trabajar con Código Legacy

## El problema

Hay un módulo de 5 años de antigüedad. Sin tests, con código difícil de entender, nombres crípticos y lógica enredada. Nadie quiere tocarlo, pero necesitas añadir una feature. Cada cambio es una ruleta rusa: puede funcionar o puede romper algo que nadie detectará hasta producción.

Un agente de código puede ayudarte enormemente con legacy, pero solo si sigues el orden correcto: **entender, proteger, modificar**. Si saltas directamente a modificar, el agente introducirá bugs con la misma confianza con la que un junior inexperto los introduciría — pero con más velocidad.

---

## Lo que puede salir mal: el escenario "sin tests"

Antes de ver el workflow correcto, veamos qué pasa cuando se ignoran los tests:

```text
Prompt directo (peligroso):
"Add support for recurring payments to src/legacy/payment-processor.js"
```

**Lo que hace el agente**: lee el archivo, entiende la lógica general (o cree que la entiende), implementa la feature y te dice "listo, funciona".

**Lo que puede ocurrir**:

1. El agente cambia una función que parecía interna pero otros módulos la llaman
2. Un edge case que el código original manejaba silenciosamente deja de funcionar
3. El formato de un dato de salida cambia sutilmente y rompe un proceso downstream
4. Todo parece funcionar en desarrollo pero falla en producción con datos reales

**El problema de fondo**: sin tests, no hay forma de saber si el comportamiento existente se preservó. Ni tú ni el agente pueden verificarlo.

---

## Workflow en 4 pasos

### Paso 1: Entender el código existente (20 minutos)

No intentes entender cada línea. Pide una explicación de alto nivel primero:

```text
Explain what src/legacy/payment-processor.js does.
Focus on:
- The main exported function(s) and their signatures
- Inputs: what data does it expect and in what format?
- Outputs: what does it return or write?
- Side effects: does it write to files, databases, or call external APIs?
- Undocumented behavior: any quirks, workarounds, or edge cases
  I should know about?
Don't modify anything. Only investigate and report.
```

**Consejo**: verifica que la explicación coincide con lo que ves en el código. El agente puede interpretar mal lógica críptica. Identifica los inputs y outputs — son los que necesitas proteger con tests.

### Paso 2: Añadir tests al código existente (30 minutos)

Esta es la fase más importante. Escribes tests para el **comportamiento actual** sin cambiar una sola línea del código original:

```text
Write tests for the CURRENT behavior of payment-processor.js.
Do NOT change the source code — only add test files.
Cover these scenarios:
- Happy path: a normal payment processing with valid data
- Error cases: invalid input, missing fields, malformed data
- Edge cases: zero amount, negative amount, very large amounts,
  special characters in description
- Side effects: verify that the correct external calls are made

These tests will be our safety net. They must all pass with the 
current code, even if the current behavior seems wrong.
```

**Por qué testear "comportamiento incorrecto"**: si el código actual devuelve un formato extraño para importes negativos, tu test debe verificar ese formato extraño. El objetivo no es validar que el código sea correcto, sino **capturar el comportamiento existente** para detectar cualquier cambio no intencionado.

**Checklist de los tests**:

- [ ] Todos los tests pasan con el código original sin modificar
- [ ] Cubren los inputs que realmente recibe el módulo en producción
- [ ] Cubren los edge cases que encontraste en el paso 1
- [ ] Verifican los side effects (llamadas a APIs, escrituras en DB)
- [ ] El coverage es suficiente para detectar regresiones (apuntar a >80%)

### Paso 3: Implementar la feature con protección de tests (30 minutos)

Ahora sí, con la red de seguridad en su sitio, implementa la nueva funcionalidad:

```text
Now add [feature description] to payment-processor.js.
Rules:
- All existing tests MUST keep passing
- Add new tests for the new feature BEFORE implementing it (TDD)
- Minimize changes to existing code — prefer adding new functions 
  over modifying existing ones
- Run the full test suite after each change
```

**Si un test existente se rompe**: para. Analiza si cambiaste comportamiento existente (no solo añadiste funcionalidad). Si es así, reconsidera tu enfoque.

### Paso 4: Refactor opcional (20 minutos)

Con tests y feature funcionales, puedes mejorar el código con tranquilidad:

```text
Now that we have tests covering the original behavior and the new feature,
refactor payment-processor.js for readability.
Priorities:
- Extract clearly named functions from blocks of inline code
- Replace magic numbers with named constants
- Simplify nested conditionals with early returns
- Add JSDoc comments to exported functions

Keep ALL tests passing after each refactoring step.
Run tests after every change, not just at the end.
```

**Cuándo NO refactorizar**:

- Si la feature ya funciona y los tests pasan, el refactor es opcional
- Si te queda poco tiempo, es mejor tener código feo que funciona que código bonito que no has verificado
- Si el módulo va a ser reemplazado pronto, el refactor no tiene ROI

---

## La lección más importante

> **Con legacy code, los tests van primero. Sin red de seguridad, cualquier cambio es una ruleta rusa — con o sin IA.**

El agente puede generar tests excelentes para código legacy porque es bueno leyendo código y deduciendo comportamiento. Pero necesitas verificar que los tests reflejan el **comportamiento real**, no el comportamiento que el agente asume.

---

## Errores comunes con código legacy

| Error | Consecuencia | Alternativa |
|-------|--------------|-------------|
| Modificar el código y los tests a la vez | No sabes si los tests reflejan el comportamiento original o el nuevo | Tests primero, código después — siempre |
| Pedir al agente que "mejore" el código legacy | Reescritura agresiva que rompe comportamiento no documentado | Entender y proteger antes de mejorar |
| Asumir que el agente entiende el código legacy | El agente puede malinterpretar lógica críptica | Verificar la explicación del agente contra el código real |
| Refactorizar antes de tener tests | Introduces cambios que no puedes verificar | Paso 4 es opcional y siempre va al final |

---

## Conexión con otros módulos

- La detección de regresiones silenciosas del [Módulo A2](../../modulo-A2-limitaciones-fallos/README.md) es especialmente relevante aquí
- El workflow de debugging del [Módulo A4](../../modulo-A4-debugging-sistematico/README.md) se aplica cuando los tests existentes fallan tras tu cambio
- Las técnicas de revisión de código del [Módulo A3](../../modulo-A3-revision-codigo-ia/README.md) ayudan a evaluar la calidad de los tests generados
