# Visual Regression Testing

## Introducción

Un test unitario puede verificar que un botón existe en el DOM. Un test de integración puede verificar que el click dispara la acción correcta. Pero ninguno de los dos detecta que el botón se desplazó 20 píxeles a la izquierda y ahora se solapa con otro elemento. Para eso necesitas **visual regression testing**: comparar screenshots antes y después de un cambio.

---

## Qué es Visual Regression Testing

### El concepto

1. Se capturan screenshots de las páginas/componentes en un estado conocido (**baseline**)
2. Se implementa un cambio en el código
3. Se capturan nuevos screenshots
4. Se comparan pixel a pixel (o con algoritmos de diferencia perceptual) con el baseline
5. Las diferencias se marcan: el humano decide si son esperadas o si son regresiones

### Cuándo es imprescindible

- Cambios en CSS global, themes o design tokens
- Actualización de dependencias de UI (frameworks CSS, librerías de componentes)
- Refactors que no deberían afectar la presentación visual
- Componentes compartidos que se renderizan en múltiples páginas

---

## Herramientas Principales

| Herramienta | Tipo | Ventaja principal | Coste |
|-------------|------|-------------------|-------|
| **Playwright** | Open source, local | Screenshots nativas + comparación built-in, multiplataforma | Gratuito |
| **Chromatic** | SaaS para Storybook | Integración perfecta con Storybook, review colaborativo | Free tier + planes pagos |
| **Percy** | SaaS genérico | Soporte multi-framework, renderizado cross-browser | Free tier + planes pagos |
| **BackstopJS** | Open source, local | Configuración simple con JSON, Docker support | Gratuito |

### Ejemplo con Playwright (TypeScript)

```typescript
import { test, expect } from "@playwright/test";

test("homepage visual regression", async ({ page }) => {
  await page.goto("http://localhost:3000");

  // Esperar a que la página cargue completamente
  await page.waitForLoadState("networkidle");

  // Comparar screenshot con el baseline almacenado
  await expect(page).toHaveScreenshot("homepage.png", {
    maxDiffPixelRatio: 0.01, // Tolerar hasta 1% de diferencia
  });
});

test("login form visual regression", async ({ page }) => {
  await page.goto("http://localhost:3000/login");
  await page.waitForLoadState("networkidle");

  // Screenshot de un componente específico
  const form = page.locator("[data-testid='login-form']");
  await expect(form).toHaveScreenshot("login-form.png", {
    maxDiffPixelRatio: 0.005,
  });
});
```

### Actualizar baselines cuando el cambio es intencional

```bash
# Regenerar todos los baselines
npx playwright test --update-snapshots

# Regenerar solo los baselines de un test específico
npx playwright test tests/visual/homepage.spec.ts --update-snapshots
```

---

## Workflow con Agente de IA

El visual regression testing se integra naturalmente con el flujo de desarrollo asistido por IA:

### Workflow paso a paso

```text
1. ANTES del cambio:
   - Ejecutar tests visuales para generar baselines actualizados
   - Verificar que todos pasan (estado limpio)

2. IMPLEMENTAR el cambio:
   - Pedir al agente que implemente la feature o el refactor
   - El agente modifica código, estilos, componentes

3. DESPUÉS del cambio:
   - Ejecutar tests visuales de nuevo
   - Los tests que detectan diferencias fallan

4. REVISAR diferencias:
   - Abrir el reporte visual (HTML, Chromatic UI, Percy dashboard)
   - Decidir para cada diferencia: ¿esperada o regresión?

5. ACTUAR:
   - Diferencia esperada → actualizar baseline
   - Regresión → pedir al agente que la corrija
```

### Prompt para el agente cuando detectas una regresión

```text
El test visual de la página /dashboard detectó una regresión: 
el componente sidebar se solapa con el contenido principal en 
resolución 1024x768. 

El cambio que introduje fue [descripción del cambio].
Corrige la regresión visual sin revertir la funcionalidad añadida.
Ejecuta los visual tests después del fix para verificar.
```

---

## Integración con CI/CD

### GitHub Actions con Playwright

```yaml
name: Visual Regression Tests
on:
  pull_request:
    branches: [main]

jobs:
  visual-tests:
    runs-on: ubuntu-latest
    steps:
      - uses: actions/checkout@v4
      - uses: actions/setup-node@v4
        with:
          node-version: "20"
      - run: npm ci
      - run: npx playwright install --with-deps chromium
      - run: npm run build && npm run start &
      - run: npx playwright test tests/visual/
      - uses: actions/upload-artifact@v4
        if: failure()
        with:
          name: visual-diff-report
          path: test-results/
```

### Estrategias para evitar falsos positivos

| Problema | Solución |
|----------|----------|
| Fechas/horas dinámicas | Mockear `Date.now()` en tests |
| Animaciones | Desactivar animaciones CSS en entorno de test |
| Fuentes que cargan lento | `waitForLoadState("networkidle")` + web fonts preloaded |
| Datos aleatorios | Usar seeds fijos o datos de fixture |
| Diferencias cross-browser | Ejecutar tests en un solo browser para consistencia |

---

## Buenas Prácticas

1. **No captures la página completa si puedes capturar componentes**: screenshots más pequeños producen diffs más claros
2. **Usa tolerancia razonable**: `maxDiffPixelRatio: 0.01` evita falsos positivos por anti-aliasing
3. **Almacena baselines en git**: son la referencia del equipo; los cambios en baselines deben revisarse en PR
4. **Ejecuta visual tests en CI, no solo en local**: las diferencias de renderizado entre SO son reales
5. **Combina con tests funcionales**: visual regression no reemplaza tests de comportamiento, los complementa

---

## Resumen

- Visual regression testing detecta cambios visuales que los tests funcionales ignoran
- Playwright es la opción más completa para equipos que quieren una solución gratuita y local
- Chromatic y Percy simplifican el review colaborativo pero tienen coste para equipos grandes
- La integración con CI/CD garantiza que ningún PR introduzca regresiones visuales inadvertidas
- El agente de IA puede implementar cambios, pero eres tú quien decide si un cambio visual es esperado o una regresión
