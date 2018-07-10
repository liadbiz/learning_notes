a namespace is a mapping from names to objects
most container object can be looped by for

### python data model

python中所有的数据都是通过object表示的。每个对象都有一个identity, type, value.

+ identity 一旦创建就确定，用id()函数可以返回其地址（cpython实现）unchangeable
+ type 决定了一个object能进行的操作 type()函数可以返回object的type unchangeable
+ value changeable 可以是mutable和immutable 

numbers,strings, tuple是immutable, list, dict是mutable的。

没有对象会被显式的destroy，什么时候被destroy取决于垃圾回收机制，cpython实现的垃圾回收机制是reference-counting.

### type hierarchy

+ None truth value is false
+ Notimplemented  truth value is true
+ Ellipsis 
+ numbers.Number
+ Sequence finite ordered sets indexed by non-negative numbers
    支持切片[i:j]代表下标在`i<=k<j`之间的元素
+ set types: unordered, finite sets of unique, immutable objects
    + set
    + frozen set
+ mappings
    + dictionary:  finite sets of objects indexed by nearly arbitrary values
+ callable types
    + instance methods
    + user-defined functions
    + generator functions: use __yield__ statement
    + Coroutine functions: use `async def`
    + Asynchronous generator functions use `async def` and `yield` statement
    + built-in functions
    + built-in methods
    + class
    + class instances
+ modules
+ custom class
+ class instances
+ I/O object
+ internal types
    + code objects
    + frame object
    + traceback object
    + slice object
    + static method object
    + class method object

special method names: 可以通过定义特殊名字的方法来实现一些特定的操作，比如运算符重载。

### Basic customization

+ object.__new__: (constructor) [return the instance of the cls] is intended mainly to allow subclasses of immutable types (like int, str, or tuple) to customize instance creation.It is also commonly overridden in custom metaclasses in order to customize class creation.
+ object.__init__: [return the customized instance] call after the **__new__**.
+ object.__del__: del x doesn’t directly call x.__del__() — the former decrements the reference count for x by one, and the latter is only called when x’s reference count reaches zero.
+ object.__repr__: [return a string object] __official__ string representation of an object
+ object.__str__: [return a string object] __unofficial__ string representation of an object
+ object._bytes__: [return a bytes object] byte representation of an object
+ object.__hash__: [return an integer]
+ object.__bool__: [return a bool value]

### Customizing attribute access

+ **__getattr__**
+ **__getattribute__**
+ **__setattr__**
+ **__delattr__**
+ **__dir__** called when dir() function is called on object


### Customizing module attribute access

+ __class__

### Implementing Descriptors

+ **__get__**
+ **__set__**
+ **__delete__**
+ **__set_name__**

### invoking descriptors

what is descriptor: a descriptor is an object attribute with “binding behavior”
[this blog](https://www.smallsurething.com/python-descriptors-made-simple/) tell how to use descritor. and the [tutorial](https://docs.python.org/3/howto/descriptor.html) on python howto is also a good resource.

### __slots__

allow us to explicitly declare data members (like properties) and deny the creation of __dict__ and __weakref__ (unless explicitly declared in __slots__ or available in a parent.)

### mataclass

[this blog](https://blog.ionelmc.ro/2015/02/09/understanding-python-metaclasses/) and [this blog](https://jakevdp.github.io/blog/2012/12/01/a-primer-on-python-metaclasses/) is good reference. and [official document](https://docs.python.org/3/reference/datamodel.html#metaclasses).
[pyconf video](https://www.youtube.com/watch?v=sPiWg5jSoZI&t=90s)
可以用type(name, bases, namespace)的来创建class.

一个class定义的过程分为四步。

+ the appropriate metaclass is determined
+ the class namespace is prepared
+ the class body is executed
+ the class object is created

metaclass用来动态控制类的创建。

实际上metaclass是decorators, class
decorators的扩展，本质上都是为了wrappering/rewriting。

Decorators: functions
Class Decorators: class
metaclass: Class hierarchies

metaclass一个很常见的的应用就是编写orm，在我们用到的orm框架中，用户可以通过class定义model进而进行各种数据库操作而不用使用sql。
很大程度上方便了用户的操作。实际上，metaclass一般是库或框架的编写者才会用到的特性。

多重继承实际上就是搭乐高积木，通过基础的部件拼接成更大的零件。


#### MRO 规则

MRO(method resolution order)
是多类继承的时候访问调用方法时搜索方法所在位置的算法。

super()实际上不一定是调用parent的，可能是MRO链的下一个对象。

### with statement context manager

用来控制initialization 和finalization

### 复合表达式

if, while, for, try, with. function and class也是语法上的复合表达式



