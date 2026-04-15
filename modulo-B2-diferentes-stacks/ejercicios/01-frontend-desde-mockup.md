# Ejercicio 1: Implementar un Componente React desde un Mockup

## Objetivo

Usar un agente de código para implementar un componente React completo a partir de una descripción textual, aplicando los patrones de frontend del módulo.

## Descripción del componente

Implementa un **formulario de login** como componente React con las siguientes características visuales:

- **Card** centrada vertical y horizontalmente en la página, fondo blanco, sombra suave, bordes redondeados
- **Campo email**: input con label, icono de sobre a la izquierda, validación de formato email
- **Campo password**: input con label, icono de candado a la izquierda, botón para mostrar/ocultar contraseña
- **Checkbox** "Recordarme" debajo de los campos
- **Botón Submit** "Iniciar Sesión": ancho completo, color primario (indigo-600), con estado de loading (spinner + texto "Cargando...")
- **Área de error**: mensaje de error en rojo debajo del botón, visible solo cuando hay error
- **Responsive**: mobile-first, card ocupa el 100% en mobile con padding, max-width 400px en desktop

## Requisitos técnicos

1. **Componente funcional** con TypeScript
2. **Tailwind CSS** para todos los estilos (no CSS modules ni styled-components)
3. **Validación del formulario**:
   - Email: requerido, formato válido
   - Password: requerido, mínimo 8 caracteres
   - Mostrar errores de validación debajo de cada campo
   - Validar al hacer submit, no mientras escribe (validación on blur opcional)
4. **Estados del botón**: normal, hover, loading (deshabilitado con spinner), disabled si el form tiene errores
5. **TypeScript types**: definir interfaces para los props del componente y el estado del formulario
6. **Tests con React Testing Library**:
   - Renderiza correctamente todos los elementos
   - Muestra error si email es inválido
   - Muestra error si password es menor a 8 caracteres
   - Botón se deshabilita durante loading
   - Toggle de visibilidad de password funciona
   - Llama a `onSubmit` con los datos correctos
7. **Accesibilidad**: labels asociados a inputs, roles ARIA para el spinner, atributo `aria-invalid` en campos con error

## Instrucciones

### Paso 1: Genera el componente

Usa tu agente de código con un prompt que incluya toda la descripción anterior. Aplica los patrones del módulo:
- Pide composición (no un monolito)
- Especifica Tailwind y mobile-first
- Pide types y tests

### Paso 2: Revisa el resultado

Verifica estos puntos antes de aceptar:
- [ ] El componente está dividido en subcomponentes (FormField, PasswordInput, SubmitButton, o similar)
- [ ] Tailwind usa clases responsive (`sm:`, `md:`)
- [ ] Los tests cubren los 6 casos listados
- [ ] Hay interfaces TypeScript para props y estado
- [ ] Los labels tienen `htmlFor` asociado al input correspondiente

### Paso 3: Itera si es necesario

Si el agente genera un componente monolítico o falta responsive, corrige con feedback específico:

```markdown
The LoginForm is a single 150-line component. Split it into:
- FormField (reusable: label + input + error message)
- PasswordInput (extends FormField with show/hide toggle)  
- SubmitButton (with loading state)
- LoginForm (composes the above)
```

## Criterios de evaluación

| Criterio | Peso | Descripción |
|----------|------|-------------|
| Match visual | 25% | El componente coincide con la descripción (card centrada, campos, botón, responsive) |
| Validación | 25% | La lógica de validación es correcta y los errores se muestran adecuadamente |
| Tests | 25% | Los 6 tests pasan y cubren los escenarios principales |
| Accesibilidad | 15% | Labels, ARIA attributes, focus management correctos |
| Composición | 10% | El código está dividido en subcomponentes reutilizables |

## Entregables

- `LoginForm.tsx` — componente principal
- `LoginForm.types.ts` — interfaces TypeScript
- `LoginForm.test.tsx` — tests con React Testing Library
- Subcomponentes en archivos separados si aplica
