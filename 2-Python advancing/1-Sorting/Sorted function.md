# Функция sorted()

Функция `sorted()` возвращает новый отсортированный список в порядке возрастания, составленный из элементов итерирующегося объекта.

Синтаксис функции:

```python
sorted(iterable[, key][, reverse]) -> list
```

 - `iterable` - объект, поддерживающий итерирование, элементы которого требуется упорядочить. Обязательный параметр функции.
 - `key=None` - именованный опциональный параметр, по умолчанию равный *`None`*. Ключ определяет то, что нужно сравнивать между элементами.
 - `reverse=False` - именованный опциональный параметр, по умолчанию равный *`False`*. При значении *`True`*, производится сортировка в обратно порядке. По умолчанию сортировка производится по возрастанию.

## Простые примеры сортировок

Воспользуемся функцией для создания нового отсортированного списка из значений неотсортированного кортежа:

```python
>>> t = (7, 6, 4, 5, 3, 1, 2, 9, 8)
>>> sorted_list = sorted(t)
>>> sorted_list
[1, 2, 3, 4, 5, 6, 7, 8, 9]
```

Попробуем применить сортировку к строке:

```python
>>> sorted_string = sorted('mwkaien')
>>> sorted_string
['a', 'e', 'i', 'k', 'm', 'n', 'w']
```

Процедура сортировки для списков (`lists`) такая же, но сами списки имеют собственный метод `sort()`. Метод очень похож на рассматриваемую функция, он также может принимать ключ для сортировки, мы можем передать ему параметр `reverse`, но данный метод изменяет список, к которому он был применен. Если ваша задача отсортировать список и передать его дальше, например, но не изменять его самого, то лучше воспользоваться функцией `sorted()`. Чтобы убедиться в том, что метод `sort()` не возвращает новый список, а изменяет тот, которому он был применен, рассмотрим пример ниже:

```python
>>> l = [7, 6, 4, 5, 3, 1, 2, 9, 8]  # Исходный список
>>> sorted(l)
[1, 2, 3, 4, 5, 6, 7, 8, 9]  # Новый отсортированный список
>>> l
[7, 6, 4, 5, 3, 1, 2, 9, 8]  # Исходный список остался нетронутым
>>> l.sort()                 # Метод не возвращает новый список, а изменяет существующий
>>> l
[1, 2, 3, 4, 5, 6, 7, 8, 9]  # Наш список изменен (отсортирован)
```

Сортировка словарей:

```python
>>> d = {'a': 3, 'c': 1, 'b': 2}
>>> d
{'a': 3, 'c': 1, 'b': 2}
>>> sorted(d)
['a', 'b', 'c']                 # Возвращается список ключей словаря
>>> sorted(d.items())           # Значение данного метода смотрите в статье про словари
[('a', 3), ('b', 2), ('c', 1)]  # Возвращается список пар ключ-значение
```

## Сортировка с применением ключей функцией

Ключ - это вызываемый объект, например, это может быть функция, которая применяется к каждому элементу последовательности и возвращает значение, по которому производят сравнение.

Для примера возьмем кортеж, состоящий из имен. Попробуем отсортировать его без использования дополнительных параметров.

```python
>>> names = ('Jake', 'Fillip', 'Anderson', 'Dake', 'Hillory')
>>> sorted(names)
['Anderson', 'Dake', 'Fillip', 'Hillory', 'Jake']
```

Как мы видим, мы произвели сортировку имен строго в алфавитном порядке. 

Давайте попробуем отсортировать список имен по другому параметру, например, по длине имен. Пусть в начале списка будут самые короткие имена, а в конце - самые длинные. 

```python
>>> sorted(names, key=len)
['Jake', 'Dake', 'Fillip', 'Hillory', 'Anderson']
```

В качестве ключа мы использовали функцию `len()`, которая возвращает длину итерабельного объекта.

> Заметим, что мы передаем в качестве ключа саму функцию, а не вызываем ее в том же месте. Поэтому правильна запись `len`, а не `len()`.

## Сортировка сложных последовательностей

Последовательность, которую мы хотим отсортировать может быть вложенной или содержать другие сложные объекты, например, как классы.

Для примера составим список самых сильных программистов из списка имеющихся. Каждый программист (элемент последовательности) имеет три параметра: имя, общий уровень навыков в его сфере деятельности и возраст:

```python
>>> coders = [
...     ('Dinesh Chugtai', 9.5, 27),
...     ('Bertram Gilfoyle', 9.5, 28),
...     ('Richard Hendricks', 10, 25)
... ]
>>> sorted(coders, key=lambda coder: coder[1], reverse=True)
[('Richard Hendricks', 10, 25), ('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28)]
```

Прекрасно. В качестве второго параметра для сравнения (если рейтинги программистов равны) можно выполнить сортировку по возрасту:

```python
>>> sorted(coders, key=lambda coder: (coder[1], coder[2]), reverse=True)
[('Richard Hendricks', 10, 25), ('Bertram Gilfoyle', 9.5, 28), ('Dinesh Chugtai', 9.5, 27)]
```

Мы рассмотрели случай с вложенными последовательностями. Допустим, что теперь каждый программист представлен в виде экземпляра класса, а не кортежем из определенных свойств:

```python
>>> class Coder:
...     def __init__(self, name, rating, age):
...             self.name = name
...             self.rating = rating
...             self.age = age
...     def __repr__(self):
...             return repr((self.name, self.rating, self.age))
...
>>> coders = [
...     Coder('Dinesh Chugtai', 9.5, 27),
...     Coder('Bertram Gilfoyle', 9.5, 28),
...     Coder('Richard Hendricks', 10, 25),
... ]
>>> coders
[('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28), ('Richard Hendricks', 10, 25)]
```

> Метод \_\_repr\_\_ позволяет представлять экземпляры в виде ('Dinesh Chugtai', 9.5, 27), a не в виде <__main__.Coder object at 0x10a2d3790>. То есть метод определяет характер репрезентации объекта внутри программы.

Сортировка теперь будет выглядеть более читабельной:

```python
>>> sorted(coders, key=lambda coder: (coder.rating, coder.age), reverse=True)
[('Richard Hendricks', 10, 25), ('Bertram Gilfoyle', 9.5, 28), ('Dinesh Chugtai', 9.5, 27)]
```

Но вся суть осталась прежней.

## Использование модуля 'operator'

Стандартный модуль `operator` имеет функции `itemgetter()`, `attrgetter()` и `methodcaller()`. Выполним сортировку с помощью этого модуля.

```python
>>> from operator import attrgetter
>>> sorted(coders, key=attrgetter('rating', 'age'), reverse=True)
[('Richard Hendricks', 10, 25), ('Bertram Gilfoyle', 9.5, 28), ('Dinesh Chugtai', 9.5, 27)]
```

Как мы видим, теперь нет необходимости в `lambda` функции.

Данный подход улучшает скорость выполнения программы и делает код более читабельным.

## Стабильность сортировки

Если при сортировки присутствуют одинаковые ключи (параметры), по которым их сортируют, то функция сортировки гарантированно сохраняет прежний порядок следования таких элементов.

Рассмотрим это на примере:

```python
>>> b = [
...     ('A', 10),
...     ('B', 3),
...     ('A', 5),
...     ('B', 6),
... ]
>>> sorted(b, key=itemgetter(0))
[('A', 10), ('A', 5), ('B', 3), ('B', 6)]
```

Как мы видим, сначала идут кортежи с первыми элементами `A`, а после них кортежи с первыми элементами `B`. Список был отсортирован по первым элементам кортежей, которые находились в списке `b`. Для одинаковых элементов, с точки зрения алгоритма сортировки, была сохранена их первоначальная последовательность. Так `('A', 10)` был впереди `('A', 5)`, `('B', 3)` был впереди `('B', 6)`. Эта последовательность сохранилась после сортировки. Это и есть стабильность сортировочного алгоритма.

Стабильность позволяет производить последовательные более сложные сортировки.

## Сортировка по нескольким параметрам

В примерах выше мы уже производили сортировку программистов не по одному, а по двум параметрам: рейтингу и возрасту.

Заметим, что мы провели сортировку по убыванию рейтинга и возрасту, где рейтинг был главным (первым) критерием для сравнения. Теперь попробуем  выполнить подобное сравнение, но возраст должен возрастать, а не убывать.

```python
>>> sorted(coders, key=lambda coder: (coder.rating, -coder.age), reverse=True)
[('Richard Hendricks', 10, 25), ('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28)]
```

Мы просто поставили знак минус `-` перед `coder.age`. Ничего сложно. Без параметра `reverse` тоже можно обойтись.

```python
>>> sorted(coders, key=lambda coder: (-coder.rating, coder.age))
[('Richard Hendricks', 10, 25), ('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28)]
```

В некоторых ситуациях может быть более красиво расписать данную сортировку на две строки, вызывая последовательно функцию сортировки:

```python
>>> coders_sorted = sorted(coders, key=lambda coder: -coder.rating)
>>> coders_sorted
[('Richard Hendricks', 10, 25), ('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28)]
>>> sorted(coders_sorted, key=lambda coder: coder.age)
[('Richard Hendricks', 10, 25), ('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28)]
```

Также в таком подходе легче применять функции модуля `operator`:

```python
>>> from operator import attrgetter
>>> coders_sorted = sorted(coders, key=attrgetter('rating'), reverse=True)
>>> coders_sorted
[('Richard Hendricks', 10, 25), ('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28)]
>>> sorted(coders_sorted, key=attrgetter('age'))
[('Richard Hendricks', 10, 25), ('Dinesh Chugtai', 9.5, 27), ('Bertram Gilfoyle', 9.5, 28)]
```