# Ejercicio 4: Legacy Rescue — Módulo de Procesamiento CSV

## Objetivo

Aplicar el workflow de 4 pasos de la teoría 04 a un módulo legacy real: entender, proteger con tests, implementar feature y opcionalmente refactorizar.

**Tiempo máximo: 60 minutos**

---

## Preparación

Crea el archivo `csv-reporter.js` con este código legacy (sin tipos, sin tests):

```javascript
// csv-reporter.js — legacy module, original author left 3 years ago
var fs = require('fs');

function processCSV(filepath, opts) {
  var data = fs.readFileSync(filepath, 'utf8');
  var lines = data.split('\n');
  var headers = lines[0].split(',');
  var results = [];
  for (var i = 1; i < lines.length; i++) {
    if (lines[i].trim() === '') continue;
    var vals = lines[i].split(',');
    var obj = {};
    for (var j = 0; j < headers.length; j++) {
      obj[headers[j].trim()] = vals[j] ? vals[j].trim() : '';
    }
    results.push(obj);
  }
  if (opts && opts.sort) {
    results.sort(function(a, b) {
      var va = a[opts.sort], vb = b[opts.sort];
      if (!isNaN(va) && !isNaN(vb)) return Number(va) - Number(vb);
      return va < vb ? -1 : va > vb ? 1 : 0;
    });
  }
  var report = '=== REPORT ===\n';
  report += 'Total records: ' + results.length + '\n';
  report += 'Columns: ' + headers.join(', ') + '\n\n';
  var numCols = [];
  headers.forEach(function(h) {
    var col = h.trim();
    var allNums = results.every(function(r) { return !isNaN(r[col]) && r[col] !== ''; });
    if (allNums) numCols.push(col);
  });
  numCols.forEach(function(col) {
    var sum = 0, min = Infinity, max = -Infinity;
    results.forEach(function(r) {
      var v = Number(r[col]);
      sum += v; if (v < min) min = v; if (v > max) max = v;
    });
    report += col + ': sum=' + sum + ', avg=' + (sum / results.length).toFixed(2);
    report += ', min=' + min + ', max=' + max + '\n';
  });
  if (opts && opts.output) { fs.writeFileSync(opts.output, report); }
  return { records: results, report: report, stats: { total: results.length, numericColumns: numCols } };
}
module.exports = { processCSV: processCSV };
```

Crea tambien `sample-data.csv` con columnas: `name,department,salary,years` y 5-7 filas de datos realistas.

---

## Tu misión

1. **Entender (10 min)**: pide al agente que explique inputs/outputs, side effects y edge cases. No modifiques nada
2. **Tests sin cambiar codigo (20 min)**: cubrir happy path, sort, output a archivo, lineas vacias, CSV sin datos. Todos pasan con el codigo original intacto
3. **Feature: filtrado por fechas (20 min)**: nueva opcion `opts.dateFilter` con `column`, `from`, `to`. Filtra registros por rango de strings ISO, ANTES del sort y estadisticas. TDD: tests primero
4. **Refactor opcional (10 min)**: `var` a `const/let`, arrow functions, extraer funciones. Tests verdes

---

## Criterios de Evaluación

| Criterio | Peso | Excelente | Insuficiente |
|----------|------|-----------|--------------|
| Tests del comportamiento original | 30% | 7+ tests incluyendo edge cases | Menos de 4 tests |
| Feature de filtrado | 35% | Funciona con TDD, no rompe nada | No funciona o rompe tests |
| Proceso seguido | 25% | Entender -> Tests -> Feature -> Refactor | Modifico codigo antes de tests |
| Refactor (bonus) | 10% | Codigo legible, tests pasan | Rompe tests |

---

## Reflexión post-ejercicio

1. ¿Los tests del paso 2 te dieron confianza para modificar el codigo?
2. ¿Encontraste algun bug en el codigo original mientras escribias tests?
3. ¿Habrias abordado este modulo de forma diferente sin IA?
