# Ejercicio 3: Migración Batch con Fan-Out

## Objetivo

Implementar una migración batch de 10 ficheros usando el patrón fan-out con logging individual por fichero, gestión de fallos y resumen final.

## Contexto

Las migraciones masivas (CommonJS a ESM, class components a hooks, callbacks a async/await) son una aplicación perfecta del patrón fan-out: cada fichero se migra de forma independiente con el mismo prompt. Este ejercicio te enseña a orquestar la migración con control, visibilidad y tolerancia a fallos.

---

## Instrucciones

### Paso 1: Prepara los ficheros a migrar

Crea 10 ficheros JavaScript con exports en CommonJS (o usa ficheros de un proyecto real):

```bash
mkdir -p src/migration-exercise
```

Crea los ficheros con este patrón (adapta nombres a tu proyecto):

```javascript
// src/migration-exercise/string-utils.js
const capitalize = (str) => str.charAt(0).toUpperCase() + str.slice(1);
const truncate = (str, len) => str.length > len ? str.slice(0, len) + "..." : str;

module.exports = { capitalize, truncate };
```

Crea un fichero con la lista de ficheros a migrar:

```text
# files-to-migrate.txt
src/migration-exercise/string-utils.js
src/migration-exercise/array-utils.js
src/migration-exercise/date-utils.js
src/migration-exercise/math-utils.js
src/migration-exercise/validation-utils.js
src/migration-exercise/format-utils.js
src/migration-exercise/file-utils.js
src/migration-exercise/http-utils.js
src/migration-exercise/crypto-utils.js
src/migration-exercise/config-loader.js
```

### Paso 2: Escribe el script de migración

Crea un script bash que orqueste la migración:

```bash
#!/bin/bash
# migrate-batch.sh

INPUT_FILE="files-to-migrate.txt"
LOG_DIR="logs/migration-$(date +%Y%m%d-%H%M%S)"
PARALLEL=3
MAX_TURNS=8
BUDGET=0.30

mkdir -p "$LOG_DIR"

TOTAL=$(wc -l < "$INPUT_FILE")
echo "=== Migración Batch CJS → ESM ==="
echo "Ficheros: $TOTAL"
echo "Paralelismo: $PARALLEL"
echo "Presupuesto/fichero: \$$BUDGET"
echo "Logs: $LOG_DIR"
echo ""

# Inicializar contadores
touch "$LOG_DIR/success.txt" "$LOG_DIR/failed.txt"

# Ejecutar en paralelo
cat "$INPUT_FILE" | xargs -P "$PARALLEL" -I {} bash -c '
  FILE="{}"
  BASENAME=$(basename "$FILE" .js)
  LOG="'"$LOG_DIR"'/${BASENAME}.log"

  echo "[$(date +%H:%M:%S)] START: $FILE"

  claude -p "Migra $FILE de CommonJS a ESM:
  - Cambia module.exports a export
  - Cambia require() a import
  - Renombra la extensión a .mjs si es necesario
  - Mantén la misma funcionalidad
  - NO modifiques la lógica, solo la sintaxis de módulos" \
    --allowedTools "Read,Edit" \
    --max-turns '"$MAX_TURNS"' \
    --max-budget-usd '"$BUDGET"' \
    > "$LOG" 2>&1

  if [ $? -eq 0 ]; then
    echo "[$(date +%H:%M:%S)] OK: $FILE"
    echo "$FILE" >> "'"$LOG_DIR"'/success.txt"
  else
    echo "[$(date +%H:%M:%S)] FAIL: $FILE"
    echo "$FILE" >> "'"$LOG_DIR"'/failed.txt"
  fi
'

# Resumen
echo ""
echo "=== RESUMEN ==="
SUCCESS=$(wc -l < "$LOG_DIR/success.txt")
FAILED=$(wc -l < "$LOG_DIR/failed.txt")
echo "OK: $SUCCESS / $TOTAL"
echo "Fallos: $FAILED / $TOTAL"

if [ "$FAILED" -gt 0 ]; then
  echo ""
  echo "Ficheros fallidos:"
  cat "$LOG_DIR/failed.txt"
  echo ""
  echo "Revisar logs en: $LOG_DIR/"
fi
```

### Paso 3: Ejecuta la migración

```bash
chmod +x migrate-batch.sh
./migrate-batch.sh
```

### Paso 4: Verifica los resultados

Después de la ejecución:

1. **Revisa el resumen**: ¿cuántos OK vs fallos?
2. **Verifica un fichero migrado**: abre uno de los ficheros migrados y comprueba que los imports/exports son correctos
3. **Revisa los logs de fallos**: si alguno falló, lee el log para entender por qué
4. **Ejecuta tests** (si los hay): verifica que la funcionalidad se mantiene

### Paso 5: Reintenta los fallos

Si hay ficheros fallidos, reintenta con más presupuesto:

```bash
if [ -f "logs/migration-*/failed.txt" ]; then
  cat logs/migration-*/failed.txt | xargs -P 2 -I {} \
    claude -p "Migra {} de CommonJS a ESM..." \
      --allowedTools "Read,Edit" \
      --max-turns 15 \
      --max-budget-usd 0.75
fi
```

---

## Criterios de Éxito

- [ ] El script procesa los 10 ficheros con logging individual
- [ ] Al menos 8 de 10 ficheros se migran correctamente
- [ ] Cada fichero tiene su log independiente en `logs/`
- [ ] El resumen final muestra OK vs fallos claramente
- [ ] Los ficheros fallidos están identificados para retry
- [ ] Has verificado al menos 2 ficheros migrados manualmente

## Tiempo Estimado

15 minutos
