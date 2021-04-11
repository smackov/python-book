# Метаклассы

> Все примеры в статье изложены на языке Python 2.X.

## Классы как объекты

Перед изучением метаклассов, нам необходимо понять что такое классы в Python. Python имеет очень особенную идею о том, что такое классы, позаимствовав часть из них из языка программирования *Smaltalk*.

В большинстве языков, классы являются только частью кода, которые описывают как создавать некоторый объект. Это отчасти правда и для Python:

```python
>>> class ObjectCreator(object):
...       pass
...
>>> my_object = ObjectCreator()
>>> print my_object
< __main__ .ObjectCreator object at 0x8974f2c>
```

Но классы в Python больше, чем просто описание объекта. Классы сами являются объектами.

Как только мы используем слово `class`, Python выполняет это и создает **объект**. Данная инструкция создает в памяти объект с именем `ObjectCreator`:

```python
>>> class ObjectCreator(object):
...       pass
...
```

Данный объект (класс) способен сам создавать другие объекты (свои экземпляры), это потому что он является классом.

Но также он является объектом, поэтому мы можем:
- присвоить его переменной
- скопировать его
- добавить ему атрибуты
- передать его функции, как параметра
- и так далее.

```python
>>> print ObjectCreator # you can print a class because it's an object
< class '__main__.ObjectCreator'>
>>> def echo(o):
...       print o
...
>>> echo(ObjectCreator) # you can pass a class as a parameter
< class '__main__.ObjectCreator'>
>>> print hasattr(ObjectCreator, 'new_attribute')
False
>>> ObjectCreator.new_attribute = 'foo' # you can add attributes to a class
>>> print hasattr(ObjectCreator, 'new_attribute')
True
>>> print ObjectCreator.new_attribute
foo
>>> ObjectCreatorMirror = ObjectCreator # you can assign a class to a variable
>>> print ObjectCreatorMirror.new_attribute
foo
>>> print ObjectCreatorMirror()
< __main__ .ObjectCreator object at 0x8997b4c>
```

## Динамическое создание классов

Так как классы являются объектами, то мы можем создавать из "на лету", как и любой другой объект.

Для начала, мы создаем класс в теле функции:

```python
>>> def choose_class(name):
...     if name == 'foo':
...         class Foo(object):
...             pass
...         return Foo # return the class, not an instance
...     else:
...         class Bar(object):
...             pass
...         return Bar
...
>>> MyClass = choose_class('foo')
>>> print MyClass # the function returns a class, not an instance
< class '__main__.Foo'>
>>> print MyClass() # you can create an object from this class
< __main__ .Foo object at 0x89c6d4c>
```

Но это не является таким динамическим, пока мы все еще должны писать весь класс сами.

С тех пор, как классы являются объектами, они должны быть созданы чем-то.

Когда мы используем слово `class`, Python создает этот объект автоматически. Но как и с другим большинством вещей в Python, это предоставляет нам возможность делать это вручную.

Помните функцию `type`? Она позволяет узнать тип объекта:

```python
>>> print type(1)
< type 'int'>
>>> print type("1")
< type 'str'>
>>> print type(ObjectCreator)
< type 'type'>
>>> print type(ObjectCreator())
< class '__main__.ObjectCreator'>
```

Функция `type` имеет и другие способности. Она также может создавать классы на лету, может получать описание класса как параметр и возвращать класс:

```python
type(name of the class,
    tuple of the parent class (for inheritance, can be empty),
    dictionary containing attributes names and values)
```

Для примера возьмем простой класс `MyShinyClass`, который ничего не делает. Стандартный вариант создания подобного класса:

```python
>>> class MyShinyClass(object):
...       pass
```

Данный класс может быть создан вручную:

```python
>>> MyShinyClass = type('MyShinyClass', (), {}) # returns a class object
>>> print MyShinyClass
< class '__main__.MyShinyClass'>
>>> print MyShinyClass() # create an instance with the class
< __main__ .MyShinyClass object at 0x8997cec>
```

Вы можете заметить, что мы использовали `MyShinyClass` в качестве имени переменной и имени класса. Они могут разными, но нет причины называть из по разному и все усложнять.

Функция `type` может принимать словарь для определения атрибутов класса:

```python
>>> class Foo(object):
...       bar = True
```

Это тоже самое, что и:

```python
>>> Foo = type('Foo', (), {'bar':True})
```

Оба варианта будут работать одинаково:

```python
>>> print Foo
< class '__main__.Foo'>
>>> print Foo.bar
True
>>> f = Foo()
>>> print f
< __main__ .Foo object at 0x8a9b84c>
>>> print f.bar
True
```

Мы можем указать кортеж классов, от которых производить наследование:

```python
>>>   class FooChild(Foo):
...         pass
```

Это тоже самое, что и:

```python
>>> FooChild = type('FooChild', (Foo,), {})
>>> print FooChild
< class '__main__.FooChild'>
>>> print FooChild.bar # bar is inherited from Foo
True
```

Теперь мы хотим добавить методы к нашему классу. Просто определите функцию с собственной сигнатурой и передайте ее в качестве аттрибута:

```python
>>> def echo_bar(self):
...       print self.bar
...
>>> FooChild = type('FooChild', (Foo,), {'echo_bar': echo_bar})
>>> hasattr(Foo, 'echo_bar')
False
>>> hasattr(FooChild, 'echo_bar')
True
>>> my_foo = FooChild()
>>> my_foo.echo_bar()
True
```

Вы уже понимаете, куда мы идем: в Python классы являются объектам, поэтому мы можем создавать классы на лету, динамически.

Это именно то, что делает Python, когда мы используем слово `class`, и это также используется в метаклассах.

## Что такое метаклассы

Метаклассы похожи на "помощников", которые создают классы сами.

Мы определяем классы для того, чтобы создавать объекты, правильно? Но мы узнали, что в Python классы являются объектами.

Метаклассы создают эти объекты. Это классы классов, которые можно пояснить таким образом:

```python
MyClass = MetaClass()
MyObject = MyClass()
```

Также мы увидели, что функция `type` позволяет нам создавать классы:

```python
MyClass = type('MyClass', (), {})
```

Функция `type` сама является метаклассом, которая используется для создания всех классов (невидимо для разработчика).

Почему тогда она написано не с большой буквы, почему не `Type`?

Это важно для постоянства, как с `str` классом, который создает строки, как с `int` классом, который создает integer объекты. `type` является классом, который создает другие классы.

Вы увидите это, проверив аттрибут `__class__` любого объекта.

**Все, абсолютно все в Python является объектом**. Это и числа, и строки, и функции, и классы. Все является объектом. И все из них созданы из классов:

```python
>>> age = 35
>>> age.__class__
< type 'int'>
>>> name = 'bob'
>>> name.__class__
< type 'str'>
>>> def foo(): pass
>>> foo.__class__
< type 'function'>
>>> class Bar(object): pass
>>> b = Bar()
>>> b.__class__
< class '__main__.Bar'>
```

Теперь, что тогда является атрибутом `__class__` атрибута `__class__`?

```python
>>> a.__class__.__class__
< type 'type'>
>>> age.__class__.__class__
< type 'type'>
>>> foo.__class__.__class__
< type 'type'>
>>> b.__class__.__class__
< type 'type'>
```

Это метакласс `type`, который является некоторым персоналом, который создает объекты классов.

Вы можете назвать его *"фабрикой классов"*, если желаете.

`type` является встроенным метаклассом Python, но мы можем создавать свои собственные метаклассы.

## Атрибут metaclass

Мы можем добавить атрибут `__metaclass__` при создании класса.

```python
class Foo(object):
    __metaclass__ = something...
[...]
```

Если мы так сделаем, то Python будет использовать этот метакласс для создания класса `Foo`. Интерпретатор сначала ищет данный атрибут в классе, если он не найдет, то используется метакласс `type` по умолчанию.

Когда мы пишем следующее:

```python
class Foo(Bar):
    pass
```

Python делает следующее:
1. Есть ли там атрибут `__metaclass__` в классе `Foo`. Если он есть, то интерпретатор создает в памяти объект класса с именем `Foo`, используя то, что расположено в атрибуте `__metaclass__`.
2. Если Python не найдет атрибут `__metaclass__`, он будет искать его в родительском классе `Bar` и так далее.
3. Если Python не найдет атрибут `__metaclass__` ни в каком из родительских классов, он будет искать его на уровне **модуля**, делая тоже самое.
4. Если Python не найдет атрибут `__metaclass__` вообще нигде, то он будет использовать `type` по умолчанию.

Теперь важный вопрос - что вы можете положить в `__metaclass__`?

Ответ: что-то, что может создавать классы.

А что может создавать классы? `type` или его подклассы.

## Произвольные метаклассы

Основной целью метакласса является автоматическое изменение класса при его создании.

Мы обычно делаем это для API, где создаем классы в соответствии с текущим контекстом.

Представим странный пример. Допустим мы решили, что все классы в нашем модуле должны иметь атрибуты, написанные в верхнем регистре. Есть множество способов сделать это, но один из них - установить атрибут `__metaclass__` на уровне модуля.

Таким способом, все классы этого модуля будут созданы через этот метакласс, и нам только остается создать метакласс, который создает все атрибуты в верхнем регистре.

К счастью, `__metaclass__` может быть любым вызываемым объектом, это не обязательно должен быть класс.

Итак, начнем с примера, который использует функцию:

```python
# the metaclass will automatically get passed the same argument
# that you usually pass to <code>type</code>
def upper_attr(future_class_name, future_class_parents, future_class_attr):
    """
      Return a class object, with the list of its attribute turned
      into uppercase.
    """
 
    # pick up any attribute that doesn't start with '__'
    attrs = ((name, value) for name, value in future_class_attr.items() if not name.startswith('__'))
    # turn them into uppercase
    uppercase_attr = dict((name.upper(), value) for name, value in attrs)
 
    # let <code>type</code> do the class creation
    return type(future_class_name, future_class_parents, uppercase_attr)
 
__metaclass__ = upper_attr # this will affect all classes in the module
 
class Foo(object):
    # we can define __metaclass__ here instead to affect only this class
    bar = 'bip'
 
print hasattr(Foo, 'bar')
# Out: False
print hasattr(Foo, 'BAR')
# Out: True
 
f = Foo()
print f.BAR
# Out: 'bip'
```

Теперь, давайте сделаем тоже самое, но используя настоящий класс для метакласса:

```python
# remember that <code>type</code> is actually a class like <code>str</code> and <code>int</code>
# so you can inherit from it
class UpperAttrMetaclass(type):
  # __new__ is the method called before __init__
  # it's the method that creates the object and returns it
  # while __init__ just initializes the object passed as parameter
  # you rarely use __new__, except when you want to control how the object
  # is created.
  # here the created object is the class, and we want to customize it
  # so we override __new__
  # you can do some stuff in __init__ too if you wish
  # some advanced use involves overriding __call__ as well, but we won't
  # see this
  def __new__(upperattr_metaclass, future_class_name,
              future_class_parents, future_class_attr):
 
      attrs = ((name, value) for name, value in future_class_attr.items() if not name.startswith('__'))
      uppercase_attr = dict((name.upper(), value) for name, value in attrs)
 
      return type(future_class_name, future_class_parents, uppercase_attr)
```

Но это не настоящий ООП подход. Мы вызываем `type` напрямую и не заменяем родительный метод `__new__`. Так давайте сделаем это:

```python
class UpperAttrMetaclass(type):
 
  def __new__(upperattr_metaclass, future_class_name,
              future_class_parents, future_class_attr):
 
      attrs = ((name, value) for name, value in future_class_attr.items() if not name.startswith('__'))
      uppercase_attr = dict((name.upper(), value) for name, value in attrs)
 
      # reuse the type.__new__ method
      # this is basic OOP, nothing magic in there
      return type.__new__(upperattr_metaclass, future_class_name,
                          future_class_parents, uppercase_attr)
```

Вы могли заметить дополнительный аргумент *upperattr_metaclass*. В этом нет ничего особенного: метод всегда получает текущий экземпляр в качестве первого параметра. Так же, как и параметр *self* для обычных методов.

Конечно, имена, которые мы использоваи сейчас слишком длинные. Более подходящие и компактные имена будут выглядеть следующим образом:

```python
class UpperAttrMetaclass(type):
 
  def __new__(cls, name, bases, dct):
 
      attrs = ((name, value) for name, value in dct.items() if not name.startswith('__'))
      uppercase_attr = dict((name.upper(), value) for name, value in attrs)
 
      return type.__new__(cls, name, bases, uppercase_attr)
```

Мы можем сделать еще яснее, используя функцию `super`, которая вызывает метод одного из родителей:

```python
class UpperAttrMetaclass(type):
 
  def __new__(cls, name, bases, dct):
 
      attrs = ((name, value) for name, value in dct.items() if not name.startswith('__'))
      uppercase_attr = dict((name.upper(), value) for name, value in attrs)
 
      return super(UpperAttrMetaclass, cls).__new__(cls, name, bases, uppercase_attr)

```

Это все. Здесь больше нет ничего особенного в метаклассах.

Причина сложности кода, использующего метаклассы, не в метаклассах, а в том, что мы обычно используем метаклассы, чтобы делать сложные (иногда странные) вещи, полагаясь на самоанализ, манипулированием наследованием и переменные такие как __dict__и т.д.

Конечно, метаклассы особенно полезны для совершения *черной магии* и других сложных вещей. Но сами по себе они не сложные. 

Метаклассы делают следующее:
- перехватывают процесс создания классов
- изменяют классы
- возвращают измененные классы.

## Почему нужно использовать классы для создания метаклассов, а не функции

Так как `__metaclass__` может принимать любой вызываемый объект, почему бы мы используем классы вместо функций? Очевидно, что использование классов более сложно.

Для этого есть несколько причин:
- Намерения ясны. Когда мы используем `UpperAttrMetaclass(type)`, становится очевидным то, что последует за этим.
- Мы можем использвать ООП. Метаклассы могут быть наследованы от других метаклассов, переопределяя родительские методы. Метаклассы также могут использовать другие метаклассы.
- Мы можем структурировать наш код лучшу. Мы никогда не будем использовать метаклассы для чего-нибудь тривиального, как в примерах выше. Обычно это что-то сложное. Возможность собрать несколько методов вместе и поместить их в класс - очень полезно, чтобы код было легче читать.
- Мы моежм использовать методы `__new__`, `__init__` и `__call__`. Они позволяют делать нам различные вещи.

## Когда могут понадобяться метаклассы

Это очень большой вопрос. Обычно они не нужны.

> Метаклассы это глубокая магия, о которой 99% пользователей даже не нужно задумываться. Если вы думаете, нужно ли вам их использовать — вам не нужно (люди, которым они реально нужны, точно знают, зачем они им, и не нуждаются в объяснениях, почему).
>__Гуру Питона *Тим Питерс*__

Одним из главных случаев применения метаклассов является создание API. Стандартным примером является *Django ORM*.

Он позволяет нам определять модели следущюим образом:

```python
class Person(models.Model):
  name = models.CharField(max_length=30)
  age = models.IntegerField()
```

Но если мы сделаем следующее:

```python
guy = Person(name='bob', age='35')
print guy.age
```

Это не вернет нам `IntegerField` объект, а вернет `int`, к тому же может вернуть это напрямую с базы данных.

Это возможно потому что `models.Model` объявляет `__metaclass__` и использует немного магии. Поэтому мы можем объявить класс `Person` весьма легко, но на самом деле он более сложен при создании.

Django делать что-то сложное проще. Он использует метаклассы для пересоздания кода, делая настоящую работу за сценой.

## Метаклассы в Python 3

В Python 3 реализация метаклассов перетерпела изменения:
- Вопервых, переменная `__metaclass__` на уровне модуля больше не используется вообще.
- Вовторых, изменился синтаксис использования металкассов:

```python
class YouClass(metaclass=YourMetaClass):
    pass
```

- Втретьих, метод `__prepare__` делает проще процесс заполнения класса атрибутами, возвращая значение (которое должно быть словарем) в атрибут `__dict__`. Это весьма полезно:

```python
class MyMetaClass(type):
    @classmethod
    def __prepare__(cls, name, baseClasses):
        return {'foo':'bar'}
 
class YourClass(metaclass=MyMetaClass):
    pass
 
print(YourClass.foo)
# this ouputs 'bar'
```

```python

```

```python

```

```python

```

```python

```


> Данная статья является переводом англоязычной статьи [Python metaclasses demystified](http://web.archive.org/web/20130201004931/http://yeleman.com/python-metaclasses-demystified/)

