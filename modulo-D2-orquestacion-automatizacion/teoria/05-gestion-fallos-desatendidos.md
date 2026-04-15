# Gestión de Fallos en Ejecución Desatendida

## Tipos de Fallos

Cuando un agente se ejecuta sin supervisión humana (CI/CD, batch, cron), los fallos tienen consecuencias diferentes a los del uso interactivo. No hay humano para corregir el rumbo.

### 1. Timeout

La tarea es demasiado larga o compleja para los límites configurados.

| Causa | Síntoma | Prevención |
|-------|---------|------------|
| Tarea demasiado amplia | Agente alcanza `--max-turns` sin terminar | Descomponer en subtareas más pequeñas |
| Loop infinito | Agente repite acciones sin progreso | `--max-turns` y `--max-budget-usd` como safety net |
| Fichero muy grande | Agente necesita leer/procesar todo el contexto | Limitar scope a secciones específicas |

### 2. Error de herramienta

Tests que fallan, build roto, comando que devuelve error.

| Causa | Síntoma | Prevención |
|-------|---------|------------|
| Tests fallan | El agente reporta test failures | Incluir "si los tests fallan, reporta y detente" en el prompt |
| Dependencia faltante | `npm install` falla | Setup del entorno antes de lanzar el agente |
| Permisos | Sin acceso a archivos o APIs | Verificar permisos en el setup del pipeline |

**Principio clave**: el agente debe reportar errores, no reintentar infinitamente. Si los tests fallan después de 2 intentos de arreglo, el agente debe parar y documentar el problema.

### 3. Alucinación en batch

El agente genera código incorrecto para 1 de 50 ficheros. Funciona la mayoría del tiempo, pero produce un resultado silenciosamente malo en algunos casos.

| Causa | Síntoma | Prevención |
|-------|---------|------------|
| Fichero atípico | Cambio que compila pero tiene bug lógico | Verificación post-batch (tests, linter) |
| Contexto insuficiente | Agente asume algo incorrecto | Prompt más específico con más contexto |
| Edge case del modelo | Comportamiento inesperado ante input raro | Adversarial testing antes del batch |

### 4. Rate limit

Demasiadas peticiones simultáneas a la API del modelo.

| Causa | Síntoma | Prevención |
|-------|---------|------------|
| Paralelismo excesivo | Errores 429 en logs | Reducir `-P` en xargs |
| Múltiples pipelines simultáneos | Rate limit global alcanzado | Coordinar ejecución de pipelines |

---

## Estrategia de Recuperación en 4 Pasos

### Paso 1: Log detallado de cada acción

Cada ejecución del agente debe producir un log completo y consultable:

```bash
# Redireccionar stdout y stderr a log por fichero
claude -p "$PROMPT" \
  --allowedTools "Read,Edit,Bash(npm test)" \
  > "logs/${TASK_ID}.stdout.log" \
  2> "logs/${TASK_ID}.stderr.log"

# Registrar metadatos
echo "{
  \"task_id\": \"$TASK_ID\",
  \"file\": \"$FILE\",
  \"start_time\": \"$(date -Iseconds)\",
  \"exit_code\": $?,
  \"prompt_hash\": \"$(echo "$PROMPT" | md5sum | cut -d' ' -f1)\"
}" >> "logs/metadata.jsonl"
```

El log debe contener suficiente información para:
- Reproducir el problema
- Entender qué hizo el agente antes de fallar
- Identificar si el fallo es del prompt, del modelo o del entorno

### Paso 2: Checkpoints entre pasos

Para tareas largas o batchs, implementar checkpoints que permitan resumir desde el último paso exitoso:

```bash
#!/bin/bash
# batch-with-checkpoints.sh

CHECKPOINT_FILE="logs/checkpoint.txt"
INPUT_FILE="files-to-process.txt"

# Si hay checkpoint, continuar desde ahí
if [ -f "$CHECKPOINT_FILE" ]; then
  LAST_DONE=$(tail -1 "$CHECKPOINT_FILE")
  echo "Reanudando desde: $LAST_DONE"
  REMAINING=$(sed -n "/$LAST_DONE/,\$p" "$INPUT_FILE" | tail -n +2)
else
  REMAINING=$(cat "$INPUT_FILE")
fi

echo "$REMAINING" | while read -r FILE; do
  claude -p "Procesa $FILE..." --max-turns 10

  if [ $? -eq 0 ]; then
    echo "$FILE" >> "$CHECKPOINT_FILE"
  else
    echo "FALLO en $FILE — deteniendo para revisión"
    exit 1
  fi
done
```

Beneficios de los checkpoints:
- Si el proceso se interrumpe (ej: CI timeout), puedes reanudarlo sin reprocesar todo
- Sabes exactamente cuáles ya se procesaron correctamente
- Reduces costes al no repetir trabajo exitoso

### Paso 3: Alertas para intervención humana

No todos los fallos se resuelven con retry. Algunos requieren una decisión humana:

```bash
# Alerta por Slack/email cuando un fallo crítico requiere atención
notify_failure() {
  local TASK_ID=$1
  local ERROR_MSG=$2

  curl -X POST "$SLACK_WEBHOOK_URL" \
    -H "Content-Type: application/json" \
    -d "{
      \"text\": \"Fallo en batch processing\",
      \"blocks\": [{
        \"type\": \"section\",
        \"text\": {
          \"type\": \"mrkdwn\",
          \"text\": \"*Task:* $TASK_ID\n*Error:* $ERROR_MSG\n*Logs:* logs/${TASK_ID}.log\"
        }
      }]
    }"
}
```

**Cuándo alertar**:
- Más del 20% de las tareas de un batch fallan
- Un fallo de seguridad detectado
- Presupuesto global excedido
- Fallo en checkpoint que impide continuar

### Paso 4: Post-Processing

Después de que el batch o pipeline termine, ejecutar verificaciones globales:

```bash
#!/bin/bash
# post-processing.sh — Verificar resultado global del batch

echo "=== Post-Processing ==="

# 1. Linter sobre todo el código modificado
echo "Ejecutando linter..."
npm run lint 2>&1 | tee logs/post-lint.log
LINT_EXIT=$?

# 2. Tests completos
echo "Ejecutando tests..."
npm test 2>&1 | tee logs/post-tests.log
TEST_EXIT=$?

# 3. Resumen
echo ""
echo "=== RESULTADO FINAL ==="
echo "Lint: $([ $LINT_EXIT -eq 0 ] && echo 'PASS' || echo 'FAIL')"
echo "Tests: $([ $TEST_EXIT -eq 0 ] && echo 'PASS' || echo 'FAIL')"

if [ $LINT_EXIT -ne 0 ] || [ $TEST_EXIT -ne 0 ]; then
  echo "Se requiere revisión manual antes de merge."
  exit 1
fi
```

---

## Resumen: Checklist de Resiliencia

| Aspecto | Pregunta de verificación |
|---------|--------------------------|
| Logging | ¿Cada tarea produce un log completo y consultable? |
| Checkpoints | ¿Se puede reanudar desde el último paso exitoso? |
| Alertas | ¿Los fallos críticos notifican a un humano? |
| Post-processing | ¿Se ejecutan linter y tests sobre el resultado global? |
| Retry | ¿Los reintentos tienen más presupuesto y menos paralelismo? |
| Límites | ¿Hay `--max-turns` y `--max-budget-usd` en todas las ejecuciones? |
