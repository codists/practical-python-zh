[目录](../Contents.md) \| [上一节 (5.1 再谈字典)](01_Dicts_revisited.md) \| [下一节 (6 生成器)](../06_Generators/00_Overview.md)

# 5.2 类和封装Classes and Encapsulation

When writing classes, it is common to try and encapsulate internal details.
This section introduces a few Python programming idioms for this including
private variables and properties.

创建类时，通常会将类的内部实现进行封装。本节介绍有关封装的习惯用法（包括私有变量和私有属性）。

### Public vs Private

One of the primary roles of a class is to encapsulate data and internal
implementation details of an object.  However, a class also defines a
*public* interface that the outside world is supposed to use to
manipulate the object.  This distinction between implementation
details and the public interface is important.

虽然对属性和对象的内部实现细节进行封装是类的主要用途之一，但是，类也定义了一些公有接口（public interface），外界应该使用这个接口来操作对象。实现细节与公有接口之间的区别很重要。

### A Problem问题

In Python, almost everything about classes and objects is *open*.

* You can easily inspect object internals.
* You can change things at will.
* There is no strong notion of access-control (i.e., private class members)

That is an issue when you are trying to isolate details of the *internal implementation*.

在 Python 中，几乎一切都是类并且对象是对外开放（open）的。

* 可以轻松地查看对象的内部实现细节。
* 可以随意地修改。
* 没有访问控制的概念（例如：私有类成员）。

当尝试对内部实现细节进行隔离的时候，这是一个问题。

### Python 封装Encapsulation

Python relies on programming conventions to indicate the intended use
of something.  These conventions are based on naming.  There is a
general attitude that it is up to the programmer to observe the rules
as opposed to having the language enforce them.

Python 依赖编程约定来指示某些东西的用途。这就是命名约定。有一种普遍的态度是，程序员应该遵守规则，而不是让语言来强制执行这些约定。

### Private Attributes私有属性

以下划线  `_` 开头的任何属性被认为是私有属性（private）。

```python
class Person(object):
    def __init__(self, name):
        self._name = 0
```

As mentioned earlier, this is only a programming style. You can still
access and change it.

如前所述，这这是一种编程风格。你仍然可以对这些私有属性进行修改。

```python
>>> p = Person('Guido')
>>> p._name
'Guido'
>>> p._name = 'Dave'
>>>
```

As a general rule, any name with a leading `_` is considered internal implementation
whether it's a variable, a function, or a module name.  If you find yourself using such
names directly, you're probably doing something wrong. Look for higher level functionality.

一般来说，一个以下划线  `_`  开头的名字被认为是内部实现，无论该名字是变量名、函数名还是模块名。如果你发现自己使用这些名字，那么你可能在做一些错误的事情。应该寻找更高级的函数。

### Simple Attributes简单属性

Consider the following class.

考虑下面这个类：

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
```

A surprising feature is that you can set the attributes
to any value at all:

这里有一个让人惊讶的特性：你可以给属性设置任何值：

```python
>>> s = Stock('IBM', 50, 91.1)
>>> s.shares = 100
>>> s.shares = "hundred"
>>> s.shares = [1, 0, 0]
>>>
```

You might look at that and think you want some extra checks.

你可能会想要对此进行检查（译注：例如 `shares ` 表示的是股份数目，值应该是整数。当将一个字符串赋值给 `shares ` 时是不合理的，应该触发一个  `ypeError` 异常）：

```python
s.shares = '50'     # Raise a TypeError, this is a string
```

How would you do it?

这时候你会怎么做？

### Managed Attributes托管属性

One approach: introduce accessor methods.

方法一：引进访问方法（accessor methods）。

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
	    self.set_shares(shares)
	    self.price = price

    # Function that layers the "get" operation
    def get_shares(self):
        return self._shares

    # Function that layers the "set" operation
    def set_shares(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected an int')
        self._shares = value
```

Too bad that this breaks all of our existing code. `s.shares = 50`
becomes `s.set_shares(50)`

糟糕的是，这破坏了我们的已有代码。例如：之前是通过 `s.shares = 50` 给 shares 赋值的，那么现在就要改成`s.set_shares(50)` 给 shares 赋值，这很不好。

### Properties

There is an alternative approach to the previous pattern.

对于前面的模式，还有一种替代方法：

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price

    @property
    def shares(self):
        return self._shares

    @shares.setter
    def shares(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected int')
        self._shares = value
```

Normal attribute access now triggers the getter and setter methods
under `@property` and `@shares.setter`.

现在，普通属性（normal attribute）的访问在 `@property` 和 `@shares.setter` 下触发了 getter 方法和 setter 方法。

```python
>>> s = Stock('IBM', 50, 91.1)
>>> s.shares         # Triggers @property
50
>>> s.shares = 75    # Triggers @shares.setter
>>>
```

With this pattern, there are *no changes* needed to the source code.
The new *setter* is also called when there is an assignment within the class,
including inside the `__init__()` method.

使用这种模式，不需要对源代码做任何修改。当在类内（包括在 `__init__()` 方法内）有赋值的时候，直接调用新的 setter：

```python
class Stock:
    def __init__(self, name, shares, price):
        ...
        # This assignment calls the setter below
        self.shares = shares
        ...

    ...
    @shares.setter
    def shares(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected int')
        self._shares = value
```

There is often a confusion between a property and the use of private names.
Although a property internally uses a private name like `_shares`, the rest
of the class (not the property) can continue to use a name like `shares`.

Properties are also useful for computed data attributes.

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price

    @property
    def cost(self):
        return self.shares * self.price
    ...
```

This allows you to drop the extra parantheses, hiding the fact that it's actually a method:

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> s.shares # Instance variable
100
>>> s.cost   # Computed Value
49010.0
>>>
```

### Uniform access

The last example shows how to put a more uniform interface on an object.
If you don't do this, an object might be confusing to use:

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> a = s.cost() # Method
49010.0
>>> b = s.shares # Data attribute
100
>>>
```

Why is the `()` required for the cost, but not for the shares?  A property
can fix this.

### Decorator Syntax

The `@` syntax is known as "decoration".  It specifies a modifier
that's applied to the function definition that immediately follows.

```python
...
@property
def cost(self):
    return self.shares * self.price
```

More details are given in [Section 7](../07_Advanced_Topics/00_Overview).

### `__slots__` Attribute

You can restrict the set of attributes names.

```python
class Stock:
    __slots__ = ('name','_shares','price')
    def __init__(self, name, shares, price):
        self.name = name
        ...
```

It will raise an error for other attributes.

```python
>>> s.price = 385.15
>>> s.prices = 410.2
Traceback (most recent call last):
File "<stdin>", line 1, in ?
AttributeError: 'Stock' object has no attribute 'prices'
```

Although this prevents errors and restricts usage of objects, it's actually used for performance and
makes Python use memory more efficiently.

### Final Comments on Encapsulation

Don't go overboard with private attributes, properties, slots,
etc. They serve a specific purpose and you may see them when reading
other Python code.  However, they are not necessary for most
day-to-day coding.

## Exercises

### Exercise 5.6: Simple Properties

Properties are a useful way to add "computed attributes" to an object.
In `stock.py`, you created an object `Stock`.  Notice that on your
object there is a slight inconsistency in how different kinds of data
are extracted:

```python
>>> from stock import Stock
>>> s = Stock('GOOG', 100, 490.1)
>>> s.shares
100
>>> s.price
490.1
>>> s.cost()
49010.0
>>>
```

Specifically, notice how you have to add the extra () to `cost` because it is a method.

You can get rid of the extra () on `cost()` if you turn it into a property.
Take your `Stock` class and modify it so that the cost calculation works like this:

```python
>>> ================================ RESTART ================================
>>> from stock import Stock
>>> s = Stock('GOOG', 100, 490.1)
>>> s.cost
49010.0
>>>
```

Try calling `s.cost()` as a function and observe that it
doesn't work now that `cost` has been defined as a property.

```python
>>> s.cost()
... fails ...
>>>
```

Making this change will likely break your earlier `pcost.py` program.
You might need to go back and get rid of the `()` on the `cost()` method.

### Exercise 5.7: Properties and Setters

Modify the `shares` attribute so that the value is stored in a
private attribute and that a pair of property functions are used to ensure
that it is always set to an integer value.  Here is an example of the expected
behavior:

```python
>>> ================================ RESTART ================================
>>> from stock import Stock
>>> s = Stock('GOOG',100,490.10)
>>> s.shares = 50
>>> s.shares = 'a lot'
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: expected an integer
>>>
```

### Exercise 5.8: Adding slots

Modify the `Stock` class so that it has a `__slots__` attribute.  Then,
verify that new attributes can't be added:

```python
>>> ================================ RESTART ================================
>>> from stock import Stock
>>> s = Stock('GOOG', 100, 490.10)
>>> s.name
'GOOG'
>>> s.blah = 42
... see what happens ...
>>>
```

When you use `__slots__`, Python uses a more efficient
internal representation of objects.   What happens if you try to
inspect the underlying dictionary of `s` above?

```python
>>> s.__dict__
... see what happens ...
>>>
```

It should be noted that `__slots__` is most commonly used as an
optimization on classes that serve as data structures.  Using slots
will make such programs use far-less memory and run a bit faster.
You should probably avoid `__slots__` on most other classes however.

[Contents](../Contents.md) \| [Previous (5.1 Dictionaries Revisited)](01_Dicts_revisited.md) \| [Next (6 Generators)](../06_Generators/00_Overview.md)
