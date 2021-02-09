# Ошибки и исключения

Разделяют два типа ошибок:
- синтаксические ошибки (`Syntax errors`)
- ошибки во время исполнения программы, которые принято называть исключениями (`Exceptions`).

## Syntax errors

Синтаксические ошибки появляются на этапе процесса парсинга кода программы. На этом этапе интерпретатор Python создает из текста программы список последовательно идущих инструкций для дальнейшего выполнения. 

Пример синтаксической ошибки:

```python
>>> print('Hello world!)
  File "<stdin>", line 1
    print('Hello world!)
                       ^
SyntaxError: EOL while scanning string literal
```

В примере выше мы забыли записать закрывающую кавычку, которая обозначает окончание выводимой строки `'Hello world!'`.

## Exceptions

Ошибки на этапе работы программы принято называть исключениями. Это те же ошибки, но могут быть вызваны по различным причинам.

Рассмотрим некоторые ошибки в примере ниже:

```python
>>> 'hello' / 10
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: unsupported operand type(s) for /: 'str' and 'int'

>>> 10 / 0
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
ZeroDivisionError: division by zero
```

В первый раз мы пробовали поделить строку на число и получили исключение `TypeError` с пояснением о том, что операция деления не поддерживается между строкой и числом.

Во второй раз мы пробовали разделить число на ноль. Эта операция вызвала исключение `ZeroDivisionError` с пояснением: деление на ноль.

## Встроенные исключения 

Built-in Exceptions (встроенные исключения) - это довольно большая коллекция уже существующих исключений.

Их немало, для подробного ознакомления с ними рекомендуем статью из официальной документации Python: [Built-in Exceptions](https://docs.python.org/3/library/exceptions.html#bltin-exceptions)

Полный список всех встроенных исключений:

```python
BaseException
 +-- SystemExit
 +-- KeyboardInterrupt
 +-- GeneratorExit
 +-- Exception
      +-- StopIteration
      +-- StopAsyncIteration
      +-- ArithmeticError
      |    +-- FloatingPointError
      |    +-- OverflowError
      |    +-- ZeroDivisionError
      +-- AssertionError
      +-- AttributeError
      +-- BufferError
      +-- EOFError
      +-- ImportError
      |    +-- ModuleNotFoundError
      +-- LookupError
      |    +-- IndexError
      |    +-- KeyError
      +-- MemoryError
      +-- NameError
      |    +-- UnboundLocalError
      +-- OSError
      |    +-- BlockingIOError
      |    +-- ChildProcessError
      |    +-- ConnectionError
      |    |    +-- BrokenPipeError
      |    |    +-- ConnectionAbortedError
      |    |    +-- ConnectionRefusedError
      |    |    +-- ConnectionResetError
      |    +-- FileExistsError
      |    +-- FileNotFoundError
      |    +-- InterruptedError
      |    +-- IsADirectoryError
      |    +-- NotADirectoryError
      |    +-- PermissionError
      |    +-- ProcessLookupError
      |    +-- TimeoutError
      +-- ReferenceError
      +-- RuntimeError
      |    +-- NotImplementedError
      |    +-- RecursionError
      +-- SyntaxError
      |    +-- IndentationError
      |         +-- TabError
      +-- SystemError
      +-- TypeError
      +-- ValueError
      |    +-- UnicodeError
      |         +-- UnicodeDecodeError
      |         +-- UnicodeEncodeError
      |         +-- UnicodeTranslateError
      +-- Warning
           +-- DeprecationWarning
           +-- PendingDeprecationWarning
           +-- RuntimeWarning
           +-- SyntaxWarning
           +-- UserWarning
           +-- FutureWarning
           +-- ImportWarning
           +-- UnicodeWarning
           +-- BytesWarning
           +-- ResourceWarning
```

## Обработка исключений, конструкция Try...Except

Исключения не обязательно приводят к остановке работы программы, так как часть из них можно отлавливать. 

Конструкция `Try...Except` предназначена для отлавливания исключений. Данная конструкция состоит из блоков:

- блок `try` - в нем расположены команды, которые необходимо выполнить. Если при их выполнении появится исключение, то он может пойман и обработан в следующем блоке `except`.
- блок `except` - в данном блоке происходит обработка исключения.
- блок `else` - опциональный блок содержит команды, которые выполнятся, если исключения не появились и блок `try` успешно выполнен.
- блок `finally` - опциональный блок содержит команды, которые выполняются в любом случае, не зависимо от результата работы программы в блоке `try`.

Пример конструкции:

```python
>>> for number in [1, '10', 0]:
...     try:
...             result = 90 / number
...     except TypeError:
...             print('TypeError')
...     except ZeroDivisionError:
...             print('ZeroDivisionError')
...     else:
...             print('Succes!')
...     finally:
...             print('That was number: ', number, end='\n\n')
...
Succes!
That was number:  1

TypeError
That was number:  10

ZeroDivisionError
That was number:  0
```

Для `1` выражение `90 / 1` выполняется успешно, никакого исключения не возникает. Успешно отрабатывает блок `try`, а после него блоки `else` и `finally`.

Для `'10'` выражение `90 / '10'` не может быть выполнено успешно, появляется исключение `TypeError`, так как строки не поддерживают деление. С исключением отрабатывает блок `try`, а после него блок `try TypeError` ловит исключение и выполняет свои команды, после этого выполняется блок `finally`.

Для `0` выражение `90 / 0` не может быть выполнено успешно, появляется исключение `ZeroDivisionError`, так как делить на ноль нельзя. С исключением отрабатывает блок `try`, а после него блок `try ZeroDivisionError` ловит исключение и выполняет свои команды, после этого выполняется блок `finally`.

## Блок Except

Блок `expect` начинается с такого же зарезервированного слова `expect`, после которого следует кортеж исключений, которые должен ловить данный блок. 

```python
try:
    # do something
    pass
except (ValueError, TypeError):
    # do something
    pass
```

Если необходимо поймать только одно указанное исключение, то скобки можно опустить:

```python
try:
    # do something
    pass
except ValueError:
    # do something
    pass
```

Если не передавать кортеж исключений, которые необходимо ловить, то блок будет обрабатывать все исключения:

```python
>>> try:
...     2 / 0
... except:
...     print('There is an exception')
...
There is an exception
```

## Аргументы исключений

Аргументы, передаваемые исключениям, называются аргументами исключений. Звучит слишком очевидно. Для доступа к этим атрибутам можно использовать подобную конструкцию:

```python
>>> try:
...     raise Exception('arg1', 'arg2', 'arg3')
... except Exception as exc:
...     print(exc.args)
...
('arg1', 'arg2', 'arg3')
```

Атрибуты, переданные исключениям, хранятся в свойстве `args` экземпляра исключения.

## Вызов исключений

`raise` - зарезервированное слово языка Python, с помощью которого можно вызвать исключение, записав после него вызываемое исключение.

Пример работы с выражением `raise` мы видели в примере выше. В нем мы вызываем исключение `Exception` и передаем ему некоторые аргументы.

Таким же образом мы можем вызывать и другие исключения.

```python
>>> raise TypeError('Explicity raising')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: Explicity raising
```

Пример применения выражения вызова исключений:

```python
>>> num = -10
>>> if num < 0:
...     raise Exception('The num must be greater 0')
...
Traceback (most recent call last):
  File "<stdin>", line 2, in <module>
Exception: The num must be greater 0
```

## Собственные исключения

Кроме встроенных (стандартных) исключений, можно встетить собственные исключения в различных модулях, библиотеках и фреймворках.

Для создания собственных классов исключений используется наследования от базового класса `Exception`.

Является хорошей практикой создание нового базового класса, который наследуется от класса `Exception`. Все новые исключения должны наследоваться от этого базового класса.

```python
class Error(Exception):
    """Base class for exceptions in this module."""
    pass

class ImportConfigError(Error):
    """Raised when couldn't import configurations."""
    pass

class ExportConfigError(Error):
    """Raised when couldn't export configurations."""
    pass
```