# Procesamiento en Lote (Batch) a Escala

## El Patrón Fan-Out Escalable

El procesamiento batch con agentes de código sigue el patrón fan-out: una lista de tareas independientes se distribuye entre múltiples instancias del agente, cada una con su propio contexto y límites.

### Ejemplo básico con xargs

```bash
# Migrar 50 ficheros de CommonJS a ESM en paralelo (4 simultáneos)
cat files-to-migrate.txt | xargs -P 4 -I {} \
  claude -p "Migra {} de CommonJS a ESM. Mantén la misma funcionalidad. Ejecuta tests después." \
    --allowedTools "Read,Edit,Bash(npm test)" \
    --max-turns 10 \
    --max-budget-usd 0.50
```

| Parámetro | Propósito |
|-----------|-----------|
| `-P 4` | Máximo 4 agentes simultáneos (controla concurrencia) |
| `-I {}` | Sustituye `{}` por cada línea del archivo |
| `--max-turns 10` | Límite de iteraciones por fichero |
| `--max-budget-usd 0.50` | Presupuesto máximo por fichero |

### Script más robusto con logging

```bash
#!/bin/bash
# batch-migrate.sh — Migración batch con logging y resumen

INPUT_FILE="files-to-migrate.txt"
LOG_DIR="logs/migration-$(date +%Y%m%d-%H%M%S)"
PARALLEL=4
MAX_TURNS=10
BUDGET_PER_FILE=0.50

mkdir -p "$LOG_DIR"

echo "Iniciando migración batch..."
echo "Ficheros: $(wc -l < "$INPUT_FILE")"
echo "Parallelismo: $PARALLEL"
echo "Logs en: $LOG_DIR"

# Procesar en paralelo con logging individual
cat "$INPUT_FILE" | xargs -P "$PARALLEL" -I {} bash -c '
  FILE="{}"
  BASENAME=$(basename "$FILE" .js)
  LOG_FILE="'"$LOG_DIR"'/${BASENAME}.log"

  echo "[$(date +%H:%M:%S)] Procesando: $FILE" | tee -a "$LOG_FILE"

  claude -p "Migra $FILE de CommonJS a ESM. Ejecuta tests después." \
    --allowedTools "Read,Edit,Bash(npm test)" \
    --max-turns '"$MAX_TURNS"' \
    --max-budget-usd '"$BUDGET_PER_FILE"' \
    >> "$LOG_FILE" 2>&1

  EXIT_CODE=$?
  if [ $EXIT_CODE -eq 0 ]; then
    echo "[$(date +%H:%M:%S)] OK: $FILE" | tee -a "$LOG_FILE"
    echo "$FILE" >> "'"$LOG_DIR"'/success.txt"
  else
    echo "[$(date +%H:%M:%S)] FAIL: $FILE (exit $EXIT_CODE)" | tee -a "$LOG_FILE"
    echo "$FILE" >> "'"$LOG_DIR"'/failed.txt"
  fi
'

# Resumen final
echo ""
echo "=== RESUMEN ==="
SUCCESS=$(wc -l < "$LOG_DIR/success.txt" 2>/dev/null || echo 0)
FAILED=$(wc -l < "$LOG_DIR/failed.txt" 2>/dev/null || echo 0)
TOTAL=$(wc -l < "$INPUT_FILE")
echo "Total: $TOTAL | OK: $SUCCESS | Fallos: $FAILED"

if [ -f "$LOG_DIR/failed.txt" ]; then
  echo ""
  echo "Ficheros fallidos:"
  cat "$LOG_DIR/failed.txt"
fi
```

---

## Gestión de Fallos en Batch

### Tipos de fallos esperados

| Tipo de fallo | Causa | Cómo detectarlo |
|---------------|-------|-----------------|
| Timeout | Fichero demasiado complejo | Exit code no-zero, log truncado |
| Error de herramienta | Tests fallan, build roto | Mensaje de error en log |
| Presupuesto agotado | Tarea más costosa de lo estimado | Mensaje "budget exceeded" en log |
| Rate limit | Demasiadas peticiones simultáneas | Error 429 en log |

### Estrategia de retry

```bash
# Reintentar ficheros fallidos con más presupuesto
if [ -f "$LOG_DIR/failed.txt" ]; then
  echo "Reintentando $(wc -l < "$LOG_DIR/failed.txt") ficheros fallidos..."
  cat "$LOG_DIR/failed.txt" | xargs -P 2 -I {} \
    claude -p "Migra {} de CommonJS a ESM. Ejecuta tests después." \
      --allowedTools "Read,Edit,Bash(npm test)" \
      --max-turns 20 \
      --max-budget-usd 1.00 \
      >> "$LOG_DIR/retry-{}.log" 2>&1
fi
```

Principios del retry:
- Reducir paralelismo (de 4 a 2) para evitar rate limits
- Aumentar presupuesto y max-turns para tareas complejas
- Log separado para distinguir primer intento de retry

---

## Monitorización

### Dashboard de progreso

Para batchs grandes (>50 ficheros), monitoriza en tiempo real:

```bash
# En otra terminal, monitoriza el progreso
watch -n 5 'echo "=== Progreso ===" && \
  echo "OK: $(wc -l < logs/migration-*/success.txt 2>/dev/null || echo 0)" && \
  echo "Fail: $(wc -l < logs/migration-*/failed.txt 2>/dev/null || echo 0)" && \
  echo "En proceso: $(ps aux | grep "claude -p" | grep -v grep | wc -l)"'
```

### Control de costes

Calcula el coste máximo antes de lanzar:

```text
Coste máximo = N ficheros x presupuesto por fichero
Ejemplo: 50 ficheros x $0.50 = $25.00 máximo

En la práctica, la mayoría de ficheros cuestan menos del máximo.
Coste real típico: 40-60% del máximo.
```

### Verificación post-batch

Después de que el batch termine, verifica el resultado global:

```bash
# Ejecutar linter y tests sobre todo el código migrado
npm run lint
npm test

# Si hay errores, cruzar con los logs para identificar qué fichero causó el problema
```

---

## Consideraciones de Escala

| Escala | Recomendación |
|--------|--------------|
| 1-10 ficheros | Batch simple con xargs, logging básico |
| 10-50 ficheros | Script con logging por fichero, retry automático, resumen |
| 50-200 ficheros | Añadir monitorización, control de costes, ejecución en fases |
| 200+ ficheros | Pipeline en CI/CD, ejecución por lotes diarios, reporting |

**Regla de oro**: empieza con 5 ficheros de prueba antes de lanzar el batch completo. Verifica que el prompt funciona correctamente a pequeña escala antes de escalar.
