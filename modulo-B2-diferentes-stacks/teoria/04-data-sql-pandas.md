# IA Agéntica y Data (SQL, Pandas, Notebooks)

## SQL y bases de datos

### Donde el agente brilla

El agente es excelente escribiendo SQL complejo: JOINs de múltiples tablas, window functions (`ROW_NUMBER()`, `LAG()`, `RANK()`), CTEs encadenadas, agregaciones avanzadas (`GROUPING SETS`, `ROLLUP`) y queries recursivas para árboles jerárquicos.

### El peligro: queries ineficientes

El agente escribe queries que **funcionan** pero pueden ser extremadamente ineficientes:

1. **SELECT * en subqueries** cuando solo necesita una columna
2. **Subqueries correlacionadas** donde un JOIN resolvería lo mismo
3. **Sin índices**: no piensa en índices a menos que se lo pidas
4. **DISTINCT como parche**: oculta un JOIN mal hecho en vez de corregirlo
5. **OR en WHERE**: cadenas que impiden uso de índices, cuando `IN` sería más eficiente

### La regla: EXPLAIN antes de producción

```markdown
Write a query to get the top 10 customers by total spending
in the last 90 days, including their most recent order date.
After writing the query, run EXPLAIN ANALYZE and verify:
- No sequential scans on large tables
- Estimated rows are reasonable
- Total execution time is under 100ms
```

### Migraciones: siempre con rollback

Siempre pide migración y rollback juntos. El test "up, insert, down, up" es crucial — muchas migraciones funcionan la primera vez pero fallan en rollback + re-apply.

```markdown
Create a migration to add a 'preferences' JSONB column to users.
Requirements:
- Include both UP and DOWN migrations
- DOWN migration must be safe (no data loss)
- Add an index for common query patterns
- Test: migrate up, insert data, migrate down, migrate up again
```

## Data science con pandas

### Notebooks Jupyter: un caso de uso natural

Los notebooks son uno de los mejores contextos para IA agéntica: celdas independientes que el agente genera y verifica, visualización inline inmediata, y un flujo natural (explorar, limpiar, analizar, visualizar) que coincide con cómo trabaja el agente.

El agente puede generar: pipelines de limpieza, análisis exploratorio (EDA), visualizaciones con matplotlib/seaborn/plotly, feature engineering y documentación inline con markdown cells.

### Precaución: verificar resultados estadísticos

El agente puede generar código que "se ve correcto" pero tiene errores sutiles: `mean()` donde debería ser `median()`, `groupby` con columnas equivocadas, `dropna()` silencioso que elimina el 40% de los datos, mezcla de escalas (miles vs millones).

**Regla**: para cualquier resultado numérico importante, verifica manualmente con un caso que puedas calcular a mano.

### Prompt template para análisis de datos

```markdown
Analyze the dataset in data/sales.csv.
1. Summary statistics and data quality issues
2. Identify the top 3 trends
3. Create visualizations for each trend
   (clear titles, labels, color-blind friendly palette)
4. Document assumptions and limitations
Don't clean or modify the original data.
```

### Workflow recomendado por fases

1. **Exploración**: cargar datos, shape, dtypes, describe(), nulls — sin modificar
2. **Limpieza**: crear df_clean separado, documentar cada transformación
3. **Análisis**: responder preguntas específicas con código y resultados
4. **Visualización**: seaborn con palette 'colorblind', figuras guardadas como PNG 300 DPI

## Errores comunes con data y agentes

| Error | Consecuencia | Prevención |
|-------|-------------|-----------|
| No verificar dtypes | Operaciones silenciosamente incorrectas | Pedir `dtypes` e `info()` siempre |
| Confiar solo en `describe()` | Oculta distribuciones multimodales | Pedir histogramas también |
| No documentar supuestos | Análisis no reproducible | Exigir markdown cells por decisión |
| Generar todo de una vez | Errores propagados | Trabajar celda por celda |
| Olvidar rollback en SQL | Migraciones irreversibles | Pedir UP + DOWN + test de ciclo |

## Resumen

| Dominio | Fortaleza del agente | Tu responsabilidad |
|---------|---------------------|-------------------|
| SQL queries | JOINs, CTEs, window functions | EXPLAIN antes de producción |
| Migraciones | Genera UP correctamente | Exigir DOWN y test de ciclo |
| Pandas/EDA | Exploración y visualización rápida | Verificar resultados numéricos |
| Notebooks | Generación celda a celda natural | Documentar supuestos y limitaciones |
