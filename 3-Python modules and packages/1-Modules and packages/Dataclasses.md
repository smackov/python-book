# Dataclasses

Данный модуль предоставляет декоратор и функции для автоматического добавления специальный методов, таких как `__init__()` и `__repr__()`, в классы, созданные пользователем. Приведем пример:

```python
from dataclasses import dataclass

@dataclass
class Movie:
    year: int
    title: str
    rating: float = 5.0
```

Данный модуль использует Type hints. Это тоже самое, что и пример ниже:

```python
class Movie:
    year: int
    title: str
    rating: float = 5.0

    def __init__(self, year: int, title: str, rating: float = 5.0):
        self.rating = rating
        self.year = year
        self.title = title

    def __repr__(self) -> str:
        return (
            'Movie(rating={self.rating!r}, '
            f'year={self.year!r}, title={self.title!r})')

    def __eq__(self, other) -> bool:
        if not isinstance(other, Movie):
            return NotImplemented
        return (
            (self.rating, self.year, self.title) ==
            (other.rating, other.year, other.title))
```

Мы видим, что при использовании модуля можно упростить создания подобных классов.

## Зачем нужен данный модуль

Классы дынных - это по сути обычные классы, которые предназначены для хранения состояния и не содержат много логики. Каждый раз, когда вы создаете класс, который в основном состоит из атрибутов, вы создаете класс данных.

Модуль `dataclasses` облегчает процесс создания таких классов. Он может автоматически создавать многие полезные и важные методы: `__init__()`, `__repr__()`, `__eq__()`,`__order__()`, `__hash__()`, `__lt__()`,  `__le__()`, `__gt__()`, `__ge__()`.

# Декоратор @dataclass

```python
@dataclasses.dataclass(*, init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False)
```

По умолчанию `dataclass()` используется как обычный декоратор без параметров. Эти три примера эквивалентны:

```python
@dataclass
class C:
    ...

@dataclass()
class C:
    ...

@dataclass(init=True, repr=True, eq=True, order=False, unsafe_hash=False, frozen=False)
class C:
   ...
```

Параметры для `dataclass()`:

- `init`: Если true (по умолчанию), `__init__()` метод будет сгенерирован. Если в классе уже определен `__init__()`, этот параметр игнорируется.

- `repr`: Если true (по умолчанию), `__repr__()` метод будет сгенерирован, который будет возвращать строку, содержащей имя класса и имя каждого поля в том порядке, в котором они определены в классе. Если в классе уже определен `__repr__()`, этот параметр игнорируется.

- `eq`: Если true (по умолчанию), `__eq__()` метод будет сгенерирован. Этот метод сравнивает класс по порядку, как если бы он был кортежем его полей. Оба экземпляра в сравнении должны быть одного типа. Если в классе уже определен `__eq__()`, этот параметр игнорируется.

- `order`: Если true (по умолчанию False), `__lt__()`, `__le__()`, `__gt__()`, и `__ge__()` методы будут генерироваться. Они по порядку сравнивают класс, как если бы он был кортежем его полей. Оба экземпляра в сравнении должны быть одного типа. Если параметр order равен true и eq false, появится `ValueError` исключение.
