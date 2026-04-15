# Ejercicio 4: Analizar un Dataset CSV con Pandas

## Objetivo

Realizar un análisis exploratorio completo de un dataset de ventas, generando un notebook Jupyter con estadísticas, visualizaciones y documentación.

## El dataset: sales.csv

Dataset ficticio de ventas durante 12 meses. Columnas: `date` (YYYY-MM-DD), `product` (nombre), `category` (Electronics, Clothing, Home, Food, Sports), `quantity` (1-50), `unit_price` (0.99-2999.99 EUR), `region` (North, South, East, West, Central), `seller` (nombre).

**Nota**: el agente puede generar datos sintéticos (800 filas) con problemas de calidad intencionales: 3% nulls en quantity/region, 5 duplicados, 2 filas con precio negativo.

## Instrucciones

### Paso 1: Genera el notebook

```markdown
Create a Jupyter notebook 'analysis.ipynb' for data/sales.csv.
Section 1: Data loading + quality assessment (shape, dtypes, nulls,
  duplicates, outliers). Summarize issues found.
Section 2: Summary statistics for numeric and categorical columns.
Section 3: Identify top 3 trends with statistical evidence.
Section 4: One visualization per trend (seaborn, 'colorblind'
  palette, clear titles/labels, figure size 10x6).
Section 5: Document assumptions and limitations.
IMPORTANT: Don't modify original CSV. Use df_clean copy.
Add markdown cells explaining each step.
```

### Paso 2: Verifica los resultados

- [ ] Verifica al menos un cálculo manualmente
- [ ] Nulls y duplicados documentados antes de limpiar
- [ ] Visualizaciones tienen títulos, labels y leyendas
- [ ] Trends están soportados por los datos
- [ ] Supuestos y limitaciones documentados

### Paso 3: Desafío extra

```markdown
Add Section 6: Seller Performance — rank by revenue,
identify most consistent seller, create heatmap
sellers vs months.
```

## Criterios de evaluación

| Criterio | Peso | Descripción |
|----------|------|-------------|
| Calidad de insights | 30% | Trends relevantes y bien sustentados |
| Visualizaciones | 25% | Claras, etiquetadas, chart type apropiado |
| Documentación | 20% | Markdown cells por paso, supuestos claros |
| Data quality | 15% | Problemas identificados y tratados |
| Reproducibilidad | 10% | Notebook ejecutable de principio a fin |

## Entregables

- `data/sales.csv` (generado o proporcionado)
- `analysis.ipynb` con las 5 secciones
- Visualizaciones en `outputs/` (PNG, 300 DPI)
