# Ejercicio 2: Mutation Testing - Descubriendo Gaps en tu Suite de Tests

## Objetivo

Aplicar mutation testing a un módulo con "buena cobertura" para descubrir los gaps que el porcentaje de coverage no revela. Experimentarás la diferencia entre "líneas ejecutadas" y "errores detectados".

---

## Contexto

Tienes un módulo de carrito de compras con sus tests. Los tests tienen 92% de coverage, pero vas a descubrir que hay mutaciones que sobreviven.

### Código del módulo

```python
# src/cart.py
from dataclasses import dataclass
from decimal import Decimal

@dataclass
class CartItem:
    product_id: str
    name: str
    price: Decimal
    quantity: int

class ShoppingCart:
    def __init__(self, tax_rate: Decimal = Decimal("0.21")):
        self.items: list[CartItem] = []
        self.tax_rate = tax_rate
        self.discount_code: str | None = None

    def add_item(self, product_id: str, name: str, price: Decimal, quantity: int = 1):
        if quantity <= 0:
            raise ValueError("Quantity must be positive")
        if price < 0:
            raise ValueError("Price cannot be negative")
        for item in self.items:
            if item.product_id == product_id:
                item.quantity += quantity
                return
        self.items.append(CartItem(product_id, name, price, quantity))

    def remove_item(self, product_id: str):
        self.items = [item for item in self.items if item.product_id != product_id]

    def get_subtotal(self) -> Decimal:
        return sum(item.price * item.quantity for item in self.items, Decimal("0"))

    def apply_discount(self, code: str, percentage: Decimal):
        if percentage < 0 or percentage > 50:
            raise ValueError("Discount must be between 0 and 50%")
        self.discount_code = code
        self._discount_percentage = percentage

    def get_total(self) -> Decimal:
        subtotal = self.get_subtotal()
        if self.discount_code:
            discount = subtotal * self._discount_percentage / 100
            subtotal -= discount
        tax = subtotal * self.tax_rate
        return (subtotal + tax).quantize(Decimal("0.01"))

    def get_item_count(self) -> int:
        return sum(item.quantity for item in self.items)
```

### Tests existentes (92% coverage)

```python
# tests/test_cart.py
from decimal import Decimal
import pytest
from src.cart import ShoppingCart

def test_add_item():
    cart = ShoppingCart()
    cart.add_item("SKU001", "Camiseta", Decimal("19.99"))
    assert len(cart.items) == 1

def test_add_duplicate_item():
    cart = ShoppingCart()
    cart.add_item("SKU001", "Camiseta", Decimal("19.99"), 1)
    cart.add_item("SKU001", "Camiseta", Decimal("19.99"), 2)
    assert cart.items[0].quantity == 3

def test_add_invalid_quantity():
    cart = ShoppingCart()
    with pytest.raises(ValueError):
        cart.add_item("SKU001", "Camiseta", Decimal("19.99"), 0)

def test_add_negative_price():
    cart = ShoppingCart()
    with pytest.raises(ValueError):
        cart.add_item("SKU001", "Camiseta", Decimal("-5"), 1)

def test_remove_item():
    cart = ShoppingCart()
    cart.add_item("SKU001", "Camiseta", Decimal("19.99"))
    cart.remove_item("SKU001")
    assert len(cart.items) == 0

def test_get_subtotal():
    cart = ShoppingCart()
    cart.add_item("SKU001", "Camiseta", Decimal("10"), 2)
    result = cart.get_subtotal()
    assert result is not None  # ¡Este es el problema!

def test_apply_discount():
    cart = ShoppingCart()
    cart.apply_discount("SAVE10", Decimal("10"))
    assert cart.discount_code == "SAVE10"

def test_invalid_discount():
    cart = ShoppingCart()
    with pytest.raises(ValueError):
        cart.apply_discount("BAD", Decimal("60"))

def test_get_total_with_discount():
    cart = ShoppingCart()
    cart.add_item("SKU001", "Producto", Decimal("100"), 1)
    cart.apply_discount("SAVE10", Decimal("10"))
    total = cart.get_total()
    assert total > 0  # ¡Otro test débil!

def test_get_item_count():
    cart = ShoppingCart()
    cart.add_item("SKU001", "A", Decimal("10"), 2)
    cart.add_item("SKU002", "B", Decimal("20"), 3)
    assert cart.get_item_count() == 5
```

---

## Parte 1: Analizar los Tests Existentes (10 min)

Antes de ejecutar mutation testing, revisa los tests manualmente.

**Pregunta**: ¿Puedes identificar al menos 2 tests que pasarían incluso si el código tuviera un bug? Anota cuáles y por qué.

---

## Parte 2: Ejecutar Mutation Testing (10 min)

Pide al agente que ejecute mutation testing:

```text
Instala mutmut y ejecútalo sobre src/cart.py usando los tests de 
tests/test_cart.py. Muéstrame:
1. Cuántos mutantes se generaron
2. Cuántos sobrevivieron
3. El mutation score
4. Los 3 mutantes sobrevivientes más interesantes (los que revelan bugs reales)
```

Si no puedes instalar mutmut, pide al agente que simule el proceso:

```text
Simula un análisis de mutation testing sobre src/cart.py. Genera al menos 
10 mutaciones realistas y para cada una indica si los tests actuales la 
detectarían o no. Muestra el mutation score resultante.
```

---

## Parte 3: Escribir los Tests que Faltan (15 min)

Para cada mutante sobreviviente, pide al agente que escriba el test que lo detectaría:

```text
Para cada mutante sobreviviente del análisis anterior, escribe el test 
mínimo que lo detectaría. Explica qué propiedad del código está validando 
cada nuevo test.
```

---

## Entregable

Un informe breve con:

1. **Coverage original**: 92% -- ¿cuántos mutantes sobrevivieron?
2. **Mutantes sobrevivientes**: lista de los más significativos con explicación
3. **Tests añadidos**: código de los tests nuevos
4. **Mutation score final**: después de añadir los tests
5. **Reflexión**: ¿Coverage del 92% era "buena cobertura"? ¿Qué cambia en tu perspectiva?

---

## Criterios de Evaluación

- Se identifican correctamente los tests débiles (`assert result is not None`, `assert total > 0`)
- Se ejecuta o simula mutation testing con al menos 8 mutantes
- Los tests nuevos validan valores concretos, no solo existencia
- El mutation score final supera el 85%
- La reflexión demuestra comprensión de la diferencia entre coverage y mutation score
