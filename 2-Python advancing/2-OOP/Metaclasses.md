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

```python

```

```python

```

```python

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

