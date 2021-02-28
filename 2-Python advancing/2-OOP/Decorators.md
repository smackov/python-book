# Декораторы

> Все примеры в статье изложены на языке Python 2.X.

Данная статья является достаточно длинной, поэтому если вы торопитесь, то вот что такое декораторы:

```python
def makebold(fn):
    def wrapped():
        return "<b>" + fn() + "</b>"
    return wrapped
 
def makeitalic(fn):
    def wrapped():
        return "<i>" + fn() + "</i>"
    return wrapped
 
@makebold
@makeitalic
def hello():
    return "hello world"
 
print hello() ## returns <b><i>hello world</i></b>
```

Теперь, если этот пример не до конца понятен тебе, то читай длинное объяснение ниже.

## Функции в Python являются объектами

Чтобы понять декораторы, вы должны сначала понять, что функции в Python являются объектами. Это является важным обстоятельством. Давайте посмотрим на простой пример:

```python
def shout(word="yes"):
    return word.capitalize()+"!"
 
print shout()
# outputs : 'Yes!'
 
# As an object, you can assign the function to a variable like any
# other object
 
scream = shout
 
# Notice we don't use parenthesis: we are NOT calling the function, we are
# putting the function "shout" into the variable "scream". It means you can then
# call "shout" from "scream":
 
print scream()
# outputs : 'Yes!'
 
# More than that, it means you can remove the old name 'shout', the function will still
# be accessible from 'scream'
 
del shout
try:
    print shout()
except NameError, e:
    print e
    #outputs: "name 'shout' is not defined"
 
print scream()
# outputs: 'Yes!'
```

Хорошо, вернемся к этому позже. Другим интересным свойством функций в Python является то, что они могут быть определены ... внутри другой функции!

```python
def talk():
 
    # You can define a function on the fly in "talk" ...
    def whisper(word="yes"):
        return word.lower()+"...";
 
    # ... and use it right away!
 
    print whisper()
 
# You call "talk", that defines "whisper" EVERY TIME you call it, then "whisper"
# is called in "talk".
talk()
# outputs:
# "yes..."
 
# But "whisper" DOES NOT EXIST outside "talk":
 
try:
    print whisper()
except NameError, e:
    print e
    #outputs : "name 'whisper' is not defined"*
```

## Замыкания. Ссылки на функции

Вы увидели, что функции являются объектами и поэтому:
- функции могут быть присвоены переменной
- функции могут быть определены внутри других функций.

Хорошо, это значит, что функция может вернуть другую функцию. Давайте посмотрим на это:

```python
def getTalk(type="shout"):
 
    # We define functions on the fly
    def shout(word="yes"):
        return word.capitalize()+"!"
 
    def whisper(word="yes") :
        return word.lower()+"...";
 
    # Then we return one of them
    if type == "shout":
        # We don't use "()", we are NOT calling the function,
        # we are returning the function object
        return shout
    else:
        return whisper
 
# How do you use this strange beast?
 
# Get the function and assign it to a variable
talk = getTalk()
 
# You can see that "talk" is here a function object:
print talk
#outputs : <function shout at 0xb7ea817c>
 
# The object is the one returned by the function:
print talk()
 
# And you can even use it directly if you feel wild:
print getTalk("whisper")()
#outputs : yes...
```

Функция, возвращающая другую функцию, является одним из примеров замыканий. 

Подождите, вот еще что. Если вы можете вернуть функцию, тогда вы можете передать ее в виде параметра:

```python
def doSomethingBefore(func):
    print "I do something before then I call the function you gave me"
    print func()
 
doSomethingBefore(scream)
#outputs:
#I do something before then I call the function you gave me
#Yes!
```

Итак, вы теперь имеете все, чтобы понять декораторы. Как вы видите, декораторы являются обертками. Это значит, что они могут выполнять код перед или после функции, которую они декорируют без необходимости модифицирования самой функции.

## Собственные декораторы

Создадим собственные декораторы:

```python
# A decorator is a function that expects ANOTHER function as parameter
def my_shiny_new_decorator(a_function_to_decorate):
 
    # Inside, the decorator defines a function on the fly: the wrapper.
    # This function is going to be wrapped around the original function
    # so it can execute code before and after it.
    def the_wrapper_around_the_original_function():
 
        # Put here the code you want to be executed BEFORE the original
        # function is called
        print "Before the function runs"
 
        # Call the function here (using parenthesis)
        a_function_to_decorate()
 
        # Put here the code you want to be executed AFTER the original
        # function is called
        print "After the function runs"
 
    # At this point, "a_function_to_decorate" HAS NEVER BEEN EXECUTED.
    # We return the wrapper function we just created.
    # The wrapper contains the function and the code to execute before
    # and after. It's ready to use!
    return the_wrapper_around_the_original_function
 
# Now imagine you create a function you don't want to ever touch again.
def a_stand_alone_function():
    print "I am a stand alone function, don't you dare modify me"
 
a_stand_alone_function()
#outputs: I am a stand alone function, don't you dare modify me
 
# Well, you can decorate it to extends its behavior.
# Just pass it to the decorator, it will wrap it dynamically in
# any code you want and returns you a new function ready to be used:
 
a_stand_alone_function_decorated = my_shiny_new_decorator(a_stand_alone_function)
a_stand_alone_function_decorated()
# outputs:
# Before the function runs
# I am a stand alone function, don't you dare modify me
# After the function runs
```

Теперь, вы, возможно, захотите, чтобы каждый раз, когда вы вызываете функцию `a_stand_alone_function`, функция `a_stand_alone_function_decorated` вызывалась взамен. Это не трудно, давайте сделаем это:

```python
a_stand_alone_function = my_shiny_new_decorator(a_stand_alone_function)
a_stand_alone_function()
# outputs:
# Before the function runs
# I am a stand alone function, don't you dare modify me
# After the function runs
``` 

И значете что? Это все, что делают декораторы!

## Декораторы разоблачены

Рассмотрим последний пример, но используя синтекс декораторов:

```python
@my_shiny_new_decorator
def another_stand_alone_function():
    print "Leave me alone"
 
another_stand_alone_function()
# outputs:
# Before the function runs
# Leave me alone
# After the function runs
```

`@decorator` это только сокращение от:

```python
another_stand_alone_function = my_shiny_new_decorator(another_stand_alone_function)
```

Декораторы являются Python вариантом [decorator design pattern](https://ru.wikipedia.org/wiki/%D0%94%D0%B5%D0%BA%D0%BE%D1%80%D0%B0%D1%82%D0%BE%D1%80_(%D1%88%D0%B0%D0%B1%D0%BB%D0%BE%D0%BD_%D0%BF%D1%80%D0%BE%D0%B5%D0%BA%D1%82%D0%B8%D1%80%D0%BE%D0%B2%D0%B0%D0%BD%D0%B8%D1%8F)). Для упрощения разработки в Python встроено несколько классических шаблонов проектирования, например итераторы.

Также вы можете использовать одновременно несколько декораторов:

```python
def bread(func):
    def wrapper():
        print ""
        func()
        print "< \______/>"
    return wrapper
 
def ingredients(func):
    def wrapper():
        print "#tomatoes#"
        func()
        print "~salad~"
    return wrapper
 
def sandwich(food="--ham--"):
    print food
 
sandwich()
#outputs: --ham--
sandwich = bread(ingredients(sandwich))
sandwich()
#outputs:
#
# #tomatoes#
# --ham--
# ~salad~
#< \______/>
```

Использую ситаксис декораторов в Python:

```python
@bread
@ingredients
def sandwich(food="--ham--"):
    print food
 
sandwich()
#outputs:
#
# #tomatoes#
# --ham--
# ~salad~
#< \______/>
```

Порядок назначения декораторов имеет значение:

```python
@ingredients
@bread
def strange_sandwich(food="--ham--"):
    print food
 
strange_sandwich()
#outputs:
##tomatoes#
#
# --ham--
#< \______/>
# ~salad~
```

Сейчас вы моежете спокойно закончить чтение этой статьи, или можете постараться еще сильнее и увидеть более сложные примеры использования декораторов.

## Передача аргументов в декорируемые функции

Это не черная магия, вы просто должны позволить обертке-функции (декоратору) передачать аргументы:

```python
def a_decorator_passing_arguments(function_to_decorate):
    def a_wrapper_accepting_arguments(arg1, arg2): # arguments arrive from here
        print "I got args! Look:", arg1, arg2
        function_to_decorate(arg1, arg2)
    return a_wrapper_accepting_arguments
 
# Since when you are calling the function returned by the decorator, you are
# calling the wrapper, passing arguments to it, which will let it pass them to
# the decorated function
 
@a_decorator_passing_arguments
def print_full_name(first_name, last_name):
    print "My name is", first_name, last_name
 
print_full_name("Peter", "Venkman")
# outputs:
#I got args! Look: Peter Venkman
#My name is Peter Venkman
```

## Декорирование методов

Что великолепно в Python, что методы и функции одинаковы, кроме того, что методы ожидают, что их первый аргумент должен быть ссылкой на объект (`self`). Это значит, что вы можете построить декоратор для методов тем же самым путем, только не забывайте об аргументе `self`:

```python
def method_friendly_decorator(method_to_decorate):
    def wrapper(self, lie):
        lie = lie - 3 # very friendly, decrease age even more :-)
        return method_to_decorate(self, lie)
    return wrapper
 
 
class Lucy(object):
 
    def __init__(self):
        self.age = 32
 
    @method_friendly_decorator
    def sayYourAge(self, lie):
        print "I am %s, what did you think?" % (self.age + lie)
 
l = Lucy()
l.sayYourAge(-3)
#outputs: I am 26, what did you think?
```

Конечно, если вы делаете более глобальный декоратор и вы желаете использовать его для функций и методов, то нет смысла в их собственных аргументах. Просто используйте `*args`, `**kwargs`:

```python
def a_decorator_passing_arbitrary_arguments(function_to_decorate):
    # The wrapper accepts any arguments
    def a_wrapper_accepting_arbitrary_arguments(*args, **kwargs):
        print "Do I have args?:"
        print args
        print kwargs
        # Then you unpack the arguments here *args, **kwargs
        # If you are not familiar with unpacking, check:
        # http://www.saltycrane.com/blog/2008/01/how-to-use-args-and-kwargs-in-python/
        function_to_decorate(*args, **kwargs)
    return a_wrapper_accepting_arbitrary_arguments
 
@a_decorator_passing_arbitrary_arguments
def function_with_no_argument():
    print "Python is cool, no argument here."
 
function_with_no_argument()
#outputs
# Do I have args?:
# ()
# {}
# Python is cool, no argument here.
 
@a_decorator_passing_arbitrary_arguments
def function_with_arguments(a, b, c):
    print a, b, c
 
function_with_arguments(1,2,3)
# outputs
# Do I have args?:
# (1, 2, 3)
# {}
# 1 2 3
 
@a_decorator_passing_arbitrary_arguments
def function_with_named_arguments(a, b, c, platypus="Why not ?"):
    print "Do %s, %s and %s like platypus? %s" %\
    (a, b, c, platypus)
 
function_with_named_arguments("Bill", "Linus", "Steve", platypus="Indeed!")
# outputs
# Do I have args ? :
# ('Bill', 'Linus', 'Steve')
# {'platypus': 'Indeed!'}
# Do Bill, Linus and Steve like platypus? Indeed!
 
class Mary(object):
 
    def __init__(self):
        self.age = 31
 
    @a_decorator_passing_arbitrary_arguments
    def sayYourAge(self, lie=-3): # You can now add a default value
        print "I am %s, what did you think ?" % (self.age + lie)
 
m = Mary()
m.sayYourAge()
# outputs
# Do I have args?:
# (<__main__ .Mary object at 0xb7d303ac>,)
# {}
# I am 28, what did you think?
```

## Передача аргументов декоратору

Отлично, а что бы вы сказали о передаче аргументов самому декоратору? Это немного запутано, потому что декоратор должен принимать функцию в качестве аргумента, и поэтому вы не можете передавать аргументы декорированной функции непосредственно декоратору.

Прежде чем спешить с решением, напишем небольшое напоминание:

```python
# Decorators are ORDINARY functions
def my_decorator(func):
    print "I am a ordinary function"
    def wrapper():
        print "I am function returned by the decorator"
        func()
    return wrapper
 
# Therefor, you can call it without any "@"
 
def lazy_function():
    print "zzzzzzzz"
 
decorated_function = my_decorator(lazy_function)
#ouputs: I am a ordinary function
 
# It ouputs "I am a ordinary function", because that's just what you do:
# calling a function. Nothing magic.
 
@my_decorator
def lazy_function():
    print "zzzzzzzz"
 
#ouputs: I am a ordinary function
```

Итак, когда вы используете `@my_decorator`, вы говорите - нужно вызвать функцию с названием `my_decorator`. Итак, название декоратора соответствует названию функции, которая реализует его.

```python
def decorator_maker():
 
    print "I make decorators! I am executed only once: "+\
          "when you make me create a decorator."
 
    def my_decorator(func):
 
        print "I am a decorator! I am executed only when you decorate a function."
 
        def wrapped():
            print ("I am the wrapper arround the decorated function. "
                  "I am called when you call the decorated function. "
                  "As the wrapper, I return the RESULT of the decorated function.")
            return func()
 
        print "As the decorator, I return the wrapped function."
 
        return wrapped
 
    print "As a decorator maker, I return a decorator"
    return my_decorator
 
# Let's create a decorator. It's just a new function after all.
new_decorator = decorator_maker()
# ouputs:
# I make decorators! I am executed only once: when you make me create a decorator.
# As a decorator maker, I return a decorator
 
# Then we decorate the function
 
def decorated_function():
    print "I am the decorated function."
 
decorated_function = new_decorator(decorated_function)
# ouputs:
# I am a decorator! I am executed only when you decorate a function.
# As the decorator, I return the wrapped function
 
# Let's call the function:
decorated_function()
# ouputs:
# I am the wrapper arround the decorated function. I am called when you call the decorated function.
# As the wrapper, I return the RESULT of the decorated function.
# I am the decorated function.
```

Давайте повторим тоже самое, только теперь не будем использовать промежутоные переменные:

```python
def decorated_function():
    print "I am the decorated function."
decorated_function = decorator_maker()(decorated_function)
# ouputs:
# I make decorators! I am executed only once: when you make me create a decorator.
# As a decorator maker, I return a decorator
# I am a decorator! I am executed only when you decorate a function.
# As the decorator, I return the wrapped function.
 
# Finally:
decorated_function()
# ouputs:
# I am the wrapper arround the decorated function. I am called when you call the decorated function.
# As the wrapper, I return the RESULT of the decorated function.
# I am the decorated function.
```

Давайте повторим пример, но сделаем его короче:

```python
@decorator_maker()
def decorated_function():
    print "I am the decorated function."
# ouputs:
# I make decorators! I am executed only once: when you make me create a decorator.
# As a decorator maker, I return a decorator
# I am a decorator! I am executed only when you decorate a function.
# As the decorator, I return the wrapped function.
 
# Eventually:
decorated_function()
# ouputs:
# I am the wrapper arround the decorated function. I am called when you call the decorated function.
# As the wrapper, I return the RESULT of the decorated function.
# I am the decorated function.

```

Как вы видите, мы использовали вызов функции с синтаксисом декаратора `@`. Итак, вернемся к декораторам с аргументами. Если мы можем использовать функции для генерации декоратора на лету, мы можем передавать аргументы этой функции, верно?

```python
def decorator_maker_with_arguments(decorator_arg1, decorator_arg2):
 
    print "I make decorators! And I accept arguments:", decorator_arg1, decorator_arg2
 
    def my_decorator(func):
        # The ability to pass arguments here is a gift from closures.
        # If you are not comfortable with closures, you can assume it's ok,
        # or read: http://stackoverflow.com/questions/13857/can-you-explain-closures-as-they-relate-to-python
        print "I am the decorator. Somehow you passed me arguments:", decorator_arg1, decorator_arg2
 
        # Don't confuse decorator arguments and function arguments!
        def wrapped(function_arg1, function_arg2) :
            print ("I am the wrapper arround the decorated function.\n"
                  "I can access all the variables\n"
                  "\t- from the decorator: {0} {1}\n"
                  "\t- from the function call: {2} {3}\n"
                  "Then I can pass them to the decorated function"
                  .format(decorator_arg1, decorator_arg2,
                          function_arg1, function_arg2))
            return func(function_arg1, function_arg2)
 
        return wrapped
 
    return my_decorator
 
@decorator_maker_with_arguments("Leonard", "Sheldon")
def decorated_function_with_arguments(function_arg1, function_arg2):
    print ("I am the decorated function and only knows about my arguments: {0}"
           " {1}".format(function_arg1, function_arg2))
 
decorated_function_with_arguments("Rajesh", "Howard")
# outputs:
# I make decorators! And I accept arguments: Leonard Sheldon
# I am the decorator. Somehow you passed me arguments: Leonard Sheldon
# I am the wrapper arround the decorated function.
# I can access all the variables
#   - from the decorator: Leonard Sheldon
#   - from the function call: Rajesh Howard
# Then I can pass them to the decorated function
# I am the decorated function and only knows about my arguments: Rajesh Howard
```

Итак, мы создали собственный декоратор, принимающий аргументы.

Как видите, вы можете передавать аргументы декоратору, как и любую функцию, используя этот трюк. Вы даже можете использовать `*args`, `**kwargs`, если хотите. Но помните, что декораторы вызываются только один раз. Просто когда Python импортирует скрипт. После этого вы не можете динамически устанавливать аргументы. Когда вы выполняете `import x`, функция уже оформлена, поэтому вы не можете изменить что-нибудь.

## Заключение

Сам Python предоставляет несколько декораторов: `property`, `staticmethod` и другие. Фреймворк Django использует декораторы для управления кэшированием, разрешениями на просмотр и для других целей. Это действительно большая тема.

Данная статья является переводом англоязычной статьи [yeleman.com/what-are-python-decorators/](http://web.archive.org/web/20130927190523/http://yeleman.com:80/what-are-python-decorators/)
