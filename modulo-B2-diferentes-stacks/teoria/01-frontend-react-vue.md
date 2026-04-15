# IA Agéntica y Frontend (React, Vue, Angular)

## Fortalezas del agente en frontend

El frontend es uno de los dominios donde la IA agéntica brilla con más fuerza. Las razones son claras:

1. **Generación rápida de componentes UI**: describes lo que necesitas y el agente genera componentes funcionales en segundos. Botones, formularios, modales, tablas, cards — el agente tiene excelente conocimiento de los patrones comunes.

2. **Conversión entre frameworks**: migrar de React class components a hooks, de React a Vue, de CSS modules a Tailwind — el agente maneja estas transformaciones con alta fiabilidad.

3. **CSS y Tailwind desde descripciones visuales**: "un card con sombra, bordes redondeados, imagen arriba y texto abajo" se traduce directamente a código funcional.

4. **Testing con React Testing Library / Vitest**: el agente genera tests que cubren rendering, interacciones de usuario y estados de error de forma natural.

## Debilidades y precauciones

Aquí es donde necesitas pensamiento crítico. El agente tiene patrones recurrentes que debes vigilar:

### Componentes monolíticos

El agente tiende a meter todo en un solo componente. Un formulario de 200 líneas con lógica de validación, llamadas a API y estilos, todo junto. **Siempre pide explícitamente composición**:

> "Divide el componente en subcomponentes: FormField, SubmitButton, ErrorMessage. Cada uno en su propio archivo."

### Mezcla de state management

En un mismo proyecto, el agente puede usar `useState` local para un componente, Redux para otro y Context API para un tercero. Sin dirección explícita, no mantiene consistencia:

> "Usa solo useState para estado local y React Query para estado del servidor. No uses Redux ni Context en este proyecto."

### CSS no responsive ni accesible

El agente genera CSS que "se ve bien" en desktop, pero rara vez incluye responsive design o atributos de accesibilidad por defecto. Debes pedirlo:

> "Incluye responsive breakpoints (mobile-first) y atributos ARIA para accesibilidad."

### Sin optimización de renders

El agente casi nunca aplica `React.memo`, `useMemo` o `useCallback` a menos que se lo pidas. Para componentes con listas grandes o cálculos costosos, necesitas mencionarlo explícitamente.

## Patrones efectivos para frontend

### Prompt template para componentes React

```markdown
Create a React component for [descripción del componente].
Follow the patterns in src/components/ (functional components, 
Tailwind for styling, React Query for data fetching).
Include: component, types, tests, and storybook story.

Requirements:
- Mobile-first responsive design
- ARIA attributes for accessibility
- Split into subcomponents if the component exceeds 80 lines
- Use only useState for local state, React Query for server state
```

### Prompt template para conversión entre frameworks

```markdown
Convert this Vue 3 component to React 18 with hooks.
Preserve: all functionality, prop types, event handlers.
Replace: Vue reactive() with useState, computed with useMemo,
watch with useEffect.
Include tests that verify the same behavior.
```

## Visual-Driven Development (VDD)

> **Nota terminológica**: VDD es un término acuñado en este curso para describir este workflow. No es un estándar de la industria. En el mercado encontrarás el concepto bajo nombres como **"design-to-code"**, con herramientas como v0 (Vercel), Emergent o Banani que lo implementan de distintas formas. El concepto subyacente — generar código a partir de representaciones visuales con IA — es real y ampliamente practicado; la etiqueta VDD es nuestra forma de enmarcarlo didácticamente.

VDD es un workflow que aprovecha la capacidad multimodal de los agentes para generar código desde representaciones visuales.

### El flujo VDD

1. **Mockup**: proporcionas un screenshot, wireframe o descripción visual detallada
2. **Análisis**: el agente interpreta la estructura visual (layout, componentes, jerarquía)
3. **Código**: genera el código correspondiente (HTML/JSX + CSS/Tailwind)
4. **Iteración**: comparas el resultado con el mockup y pides ajustes específicos

### Cuándo funciona bien VDD

- Diseños simples y bien definidos (landing pages, formularios, dashboards básicos)
- Componentes individuales con layout claro
- Conversión de diseños Figma a código cuando la estructura es evidente

### Cuándo VDD tiene limitaciones

- Diseños complejos con muchos estados (hover, focus, error, loading, empty)
- Layouts con interacciones sofisticadas (drag & drop, animaciones complejas)
- Diseños que dependen de datos dinámicos — el agente no puede imaginar cómo se ve con 100 items vs 3

### Estrategia para diseños complejos

Para diseños complejos, la clave es **dividir y conquistar**:

1. Descomponer el diseño en componentes individuales
2. Generar cada componente por separado
3. Componer los componentes en el layout final
4. Iterar sobre el resultado completo

```markdown
I have a dashboard with 3 sections:
1. Top bar: logo left, search center, user avatar right
2. Sidebar: navigation links with icons, collapsible
3. Main content: grid of cards (2 columns on desktop, 1 on mobile)

Let's start with section 1 only. Create the TopBar component.
Use Tailwind CSS, mobile-first responsive.
```

## Resumen

| Aspecto | Qué esperar del agente | Tu responsabilidad |
|---------|----------------------|-------------------|
| Generación de componentes | Excelente para componentes individuales | Pedir composición, no monolitos |
| State management | Funcional pero inconsistente | Definir el patrón en CLAUDE.md |
| CSS/Tailwind | Bueno en desktop, limitado en responsive | Pedir mobile-first y accesibilidad |
| Testing | Buena cobertura básica | Verificar edge cases y accesibilidad |
| Performance | No optimiza renders por defecto | Pedir memoización explícitamente |
| VDD | Muy efectivo para diseños simples | Dividir diseños complejos en partes |
