# Модуль operator

Модуль *operator* предоставляет множество функций, которые соответствуют собственный операторам Python. Для примера функция `operator.mod(a, b)` является эквивалентом выражения `a % b`. Многие функции имеют по 2 имени - стандартное название функции и название с двумя знаками нижнего подчеркивания с каждой стороны (для обратной совместимости). Имена без нижних подчеркиваний являются более предпочтительными для чистоты кода.

Для понимания того, чем этот модуль может быть полезен (что не очевидно на первый взгляд) и в каких ситуациях его можно использовать, смотрите раздел [Использование модуля на практике](#использование-модуля-на-практике), который расположен ниже.

## Категории функций

Функции разбиты на следующие категории:
- Объектное сравнение
- Логические операции
- Математические операции
- Операции с последовательностями

## Соответствие операторов функциям

| Operation | Syntax | Function |
| --------- | ------ | -------- |
| Addition | `a + b` | `add(a, b)` |
| Concatenation | `seq1 + seq2` | `concat(seq1, seq2)` |
| Containment Test | `obj in seq` | `contains(seq, obj)` |
| Division | `a / b` | `truediv(a, b)` |
| Division | `a // b` | `floordiv(a, b)` |
| Bitwise And | `a & b` | `and_(a, b)` |
| Bitwise Exclusive Or | `a ^ b` | `xor(a, b)` |
| Bitwise Inversion | `~ a` | `invert(a)` |
| Bitwise Or | `a | b` | `or_(a, b)` |
| Exponentiation | `a ** b` | `pow(a, b)` |
| Identity | `a is b` | `is_(a, b)` |
| Identity | `a is not b` | `is_not(a, b)` |
| Indexed Assignment | `obj[k] = v` | `setitem(obj, k, v)` |
| Indexed Deletion | `del obj[k]` | `delitem(obj, k)` |
| Indexing | `obj[k]` | `getitem(obj, k)` |
| Left Shift | `a << b` | `lshift(a, b)` |
| Modulo | `a % b` | `mod(a, b)` |
| Multiplication | `a * b` | `mul(a, b)` |
| Matrix Multiplication | `a @ b` | `matmul(a, b)` |
| Negation (Arithmetic) | `- a` | `neg(a)` |
| Negation (Logical) | `not a` | `not_(a)` |
| Positive | `+ a` | `pos(a)` |
| Right Shift | `a >> b` | `rshift(a, b)` |
| Slice Assignment | `seq[i:j] = values` | `setitem(seq, slice(i, j), values)` |
| Slice Deletion | `del seq[i:j]` | `delitem(seq, slice(i, j))` |
| Slicing | `seq[i:j]` | `getitem(seq, slice(i, j))` |
| String Formatting | `s % obj` | `mod(s, obj)` |
| Subtraction | `a - b` | `sub(a, b)` |
| Truth Test | `obj` | `truth(obj)` |
| Ordering | `a < b` | `lt(a, b)` |
| Ordering | `a <= b` | `le(a, b)` |
| Equality | `a == b` | `eq(a, b)` |
| Difference | `a != b` | `ne(a, b)` |
| Ordering | `a >= b` | `ge(a, b)` |
| Ordering | `a > b` | `gt(a, b)` |

## Использование модуля на практике

Одним из самых частых случаев применения модуля *operator* является использование его функций для операций сравнения при сортировке элементов. Для сортировки используется функция `sorted(seq, key)` или метод списков `sort(key)`, которые для сравнения могут получать параметр `key` - ключ для сравнения элементов. Этот ключ - функция, которая применяется к каждому элементу. Поэтому вместо того, чтобы создавать собственные функции, мы можем использовать уже готовые из модуля *operator*: 

```python
# Отсортируем список по первым элементам кортежей
>>> l = [(3, 5), (1, 6), (2, 9)]
>>> sorted(l, key=lambda x: x[0])
[(1, 6), (2, 9), (3, 5)]

# Или можем использовать функцию operator.itemgetter()
>>> import operator
>>> sorted(l, key=operator.itemgetter(0))
[(1, 6), (2, 9), (3, 5)]
```

Почему стоит использовать функции модуля *operator* вместо собственных? Вот несколько причин:
1. Функции модуля *operator* могут работать быстрее.
2. Код становиться более понятным и читабельным. Разработчику не нужно искать в документации раздел о том, что делаем функция `itemgetter` или `add`, это уже ясно из названия.
3. Функции модуля *operator* можно передавать между процессами, a `lambda` - нет.

По поводу пункта  №2. Допустим мы хотим получить максимальное значение и его индекс в списке. Приведем пример алгоритма без и с функцией `itemgetter`:

```python
def get_max_val_idx():
    lst = [1, 7, 3, 5, 6]
    max_val = max(lst)
    print max_val
    # 7
    max_idx = lst.index(max_val)
    print max_idx
    # 1

    # simplify it by use operator
    index, value = max(enumerate(lst), key=operator.itemgetter(1))
    print index, value
    # 1 7
```

Также можно привести следующий пример для сравнения:

```python
>>> import operator
>>> a = [2, 3, 4, 5]
>>> reduce(lambda x, y: x + y, a)
14
>>> reduce(operator.add, a)
14
```

По поводу пункта №3. Приведем пример, в котором замена выражения `itemgetter` на выражение `lambda` приведет к выражению `PicklingError`

```python
from operator import itemgetter

def sort_by_key(sequence, key):
    return sorted(sequence, key=key)

if __name__ == "__main__":
    from multiprocessing import Pool

    items = [([(1,2),(4,1)], itemgetter(1)),
             ([(5,3),(2,7)], itemgetter(0))]

    with Pool(5) as p:
        result = p.starmap(sort_by_key, items)
    print(result)
```

## Заключение 

Модуль *operator* имеет множество функций, которые соответствуют операторам в Python. Модуль полезен, когда его функции нужно передать в качестве параметра какой-то функции, например для функции сортировки в качестве ключа и похожих функций.
