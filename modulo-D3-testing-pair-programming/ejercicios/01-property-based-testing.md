# Ejercicio 1: Property-Based Testing para un Módulo de Validación

## Objetivo

Generar property-based tests para un módulo de validación usando un agente de IA, y comparar los resultados con tests unitarios tradicionales para entender las diferencias en cobertura y detección de bugs.

---

## Contexto

Tienes el siguiente módulo de validación que procesa datos de usuario:

```python
# src/validators.py
import re
from datetime import date

def validate_email(email: str) -> bool:
    """Valida formato de email."""
    pattern = r'^[a-zA-Z0-9._%+-]+@[a-zA-Z0-9.-]+\.[a-zA-Z]{2,}$'
    return bool(re.match(pattern, email))

def validate_age(birth_date: str) -> int:
    """Calcula edad a partir de fecha de nacimiento (YYYY-MM-DD). Retorna edad o -1 si inválido."""
    try:
        born = date.fromisoformat(birth_date)
        today = date.today()
        age = today.year - born.year - ((today.month, today.day) < (born.month, born.day))
        return age if 0 <= age <= 150 else -1
    except (ValueError, TypeError):
        return -1

def normalize_phone(phone: str) -> str:
    """Normaliza un número de teléfono eliminando espacios, guiones y paréntesis."""
    cleaned = re.sub(r'[\s\-\(\)\.]', '', phone)
    if not cleaned.startswith('+'):
        cleaned = '+34' + cleaned  # Default: prefijo España
    return cleaned

def validate_username(username: str) -> tuple[bool, str]:
    """Valida username. Retorna (válido, mensaje)."""
    if len(username) < 3:
        return False, "Username debe tener al menos 3 caracteres"
    if len(username) > 30:
        return False, "Username no puede exceder 30 caracteres"
    if not re.match(r'^[a-zA-Z0-9_]+$', username):
        return False, "Username solo puede contener letras, números y guión bajo"
    return True, "OK"
```

---

## Parte 1: Tests Unitarios Tradicionales (10 min)

Pide al agente que genere tests unitarios tradicionales para las 4 funciones:

```text
Genera tests unitarios con pytest para src/validators.py. 
Incluye casos positivos, negativos y de borde para cada función.
Usa el estilo convencional: un assert por caso concreto.
```

Guarda el resultado en `tests/test_validators_unit.py`.

**Pregunta para reflexión**: Revisa los tests generados. ¿Cuántos casos concretos hay? ¿Crees que cubren todos los edge cases posibles?

---

## Parte 2: Property-Based Tests (15 min)

Ahora pide al agente que genere property-based tests para las mismas funciones:

```text
Genera property-based tests con Hypothesis para src/validators.py.
No uses ejemplos concretos. Define propiedades que siempre deben cumplirse:

Para validate_email:
- Propiedad de formato: cualquier email válido contiene exactamente un @
- Propiedad de idempotencia: validar dos veces produce el mismo resultado

Para validate_age:
- Propiedad de rango: el resultado siempre es -1 o un entero entre 0 y 150
- Propiedad de consistencia: fechas futuras siempre retornan -1

Para normalize_phone:
- Propiedad de formato: el resultado siempre empieza con +
- Propiedad de idempotencia: normalizar un teléfono ya normalizado no lo cambia

Para validate_username:
- Propiedad de longitud: usernames de 3-30 chars alfanuméricos siempre son válidos
- Propiedad de consistencia: el booleano y el mensaje son coherentes

Incluye strategies personalizadas cuando sea necesario.
```

Guarda el resultado en `tests/test_validators_property.py`.

---

## Parte 3: Comparación (10 min)

Ejecuta ambas suites de tests y compara:

```bash
# Ejecutar tests unitarios con coverage
pytest tests/test_validators_unit.py --cov=src/validators -v

# Ejecutar property-based tests con coverage
pytest tests/test_validators_property.py --cov=src/validators -v
```

### Tabla de comparación

Completa esta tabla con tus resultados:

| Aspecto | Tests unitarios | Property-based tests |
|---------|----------------|----------------------|
| Número de tests | | |
| Inputs probados (total) | | |
| Coverage (%) | | |
| Bugs encontrados | | |
| Tiempo de ejecución | | |

---

## Entregable

Un documento breve (máximo 1 página) con:

1. La tabla de comparación completada
2. ¿Encontraron los property-based tests algún edge case que los unitarios no cubrían?
3. ¿En qué situaciones preferirías cada enfoque?
4. Reflexión: ¿Cambiaría tu estrategia de testing después de este ejercicio?

---

## Criterios de Evaluación

- Los property-based tests definen propiedades reales, no ejemplos disfrazados
- La comparación incluye datos concretos (no opiniones genéricas)
- Se identifican al menos 2 diferencias significativas entre ambos enfoques
- La reflexión demuestra comprensión de cuándo usar cada técnica
