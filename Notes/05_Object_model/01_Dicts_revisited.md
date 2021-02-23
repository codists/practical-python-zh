[目录](../Contents.md) \| [上一节 (4.4 异常)](../04_Classes_objects/04_Defining_exceptions.md) \| [下一节 (5.2 封装)](02_Classes_encapsulation.md)

# 5.1 再探字典Dictionaries Revisited

The Python object system is largely based on an implementation
involving dictionaries.  This section discusses that.

Python 对象系统很大程度上基于字典实现。本节对此进行讨论。

### Dictionaries, Revisited字典

Remember that a dictionary is a collection of named values.

请记住，字典是命名值（named values）的集合。

```python
stock = {
    'name' : 'GOOG',
    'shares' : 100,
    'price' : 490.1
}
```

Dictionaries are commonly used for simple data structures.  However,
they are used for critical parts of the interpreter and may be the
*most important type of data in Python*.

虽然字典常用于简单的数据结构，但是字典对于解释器重要部分是非常有用的，并且是 Python 中最重要的数据类型。

### Dicts and Modules字典和模块

Within a module, a dictionary holds all of the global variables and
functions.

在模块内，字典存储所有的全局变量和函数。

```python
# foo.py

x = 42
def bar():
    ...

def spam():
    ...
```

If you inspect `foo.__dict__` or `globals()`, you'll see the dictionary.

可以通过 `foo.__dict__` 或 `globals()`  查看该字典。

```python
{
    'x' : 42,
    'bar' : <function bar>,
    'spam' : <function spam>
}
```

### Dicts and Objects字典和对象

User defined objects also use dictionaries for both instance data and
classes.  In fact, the entire object system is mostly an extra layer
that's put on top of dictionaries.

用户也可以使用字典定义对象，为实例数据（译注：属性）或者类。事实上，全部的对象系统几乎是字典顶层上的额外层。

A dictionary holds the instance data, `__dict__`.

字典存储实例数据，如 `__dict__`。

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> s.__dict__
{'name' : 'GOOG', 'shares' : 100, 'price': 490.1 }
```

You populate this dict (and instance) when assigning to `self`.

当给 `self` 赋值的时候，你操作的是该字典与实例。

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
```

The instance data, `self.__dict__`, looks like this:

实例数据 `self.__dict__`  看起来像下面这样：

```python
{
    'name': 'GOOG',
    'shares': 100,
    'price': 490.1
}
```

**Each instance gets its own private dictionary.**

**每一个实例都拥有自己的私有字典。**

```python
s = Stock('GOOG', 100, 490.1)     # {'name' : 'GOOG','shares' : 100, 'price': 490.1 }
t = Stock('AAPL', 50, 123.45)     # {'name' : 'AAPL','shares' : 50, 'price': 123.45 }
```

If you created 100 instances of some class, there are 100 dictionaries
sitting around holding data.

如果你创建了某个类的 100 个实例，那么就会有 100 个存储数据的字典。

### Class Members类成员

A separate dictionary also holds the methods.

一个单独的字典也存储方法：

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price

    def cost(self):
        return self.shares * self.price

    def sell(self, nshares):
        self.shares -= nshares
```

The dictionary is in `Stock.__dict__`.

使用 `Stock.__dict__` 可以查看该字典：

```python
{
    'cost': <function>,
    'sell': <function>,
    '__init__': <function>
}
```

### Instances and Classes实例和类

Instances and classes are linked together.  The `__class__` attribute
refers back to the class.

实例和类是连在一起的。`__class__` 属性指向类。

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> s.__dict__
{ 'name': 'GOOG', 'shares': 100, 'price': 490.1 }
>>> s.__class__
<class '__main__.Stock'>
>>>
```

The instance dictionary holds data unique to each instance, whereas
the class dictionary holds data collectively shared by *all*
instances.

实例字典存储的数据对每个实例而言是唯一的。但是，类字典存储的数据被所有的实例共享。

### Attribute Access属性访问

When you work with objects, you access data and methods using the `.` operator.

当使用对象的时候，使用 `.`  操作符访问数据和方法。

```python
x = obj.name          # Getting
obj.name = value      # Setting
del obj.name          # Deleting
```

These operations are directly tied to the dictionaries sitting underneath the covers.

这些操作直接与字典绑定到一起。

### Modifying Instances修改实例

Operations that modify an object update the underlying dictionary.

修改对象会更新底层的字典。

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> s.__dict__
{ 'name':'GOOG', 'shares': 100, 'price': 490.1 }
>>> s.shares = 50       # Setting
>>> s.date = '6/7/2007' # Setting
>>> s.__dict__
{ 'name': 'GOOG', 'shares': 50, 'price': 490.1, 'date': '6/7/2007' }
>>> del s.shares        # Deleting
>>> s.__dict__
{ 'name': 'GOOG', 'price': 490.1, 'date': '6/7/2007' }
>>>
```

### Reading Attributes读取属性

Suppose you read an attribute on an instance.

假设你读取一个实例的属性：

```python
x = obj.name
```

The attribute may exist in two places:

该属性可能位于两个地方：

* 局部实例字典Local instance dictionary.
* 类字典Class dictionary.

Both dictionaries must be checked.  First, check in local `__dict__`.
If not found, look in `__dict__` of class through `__class__`.

两种字典都会被检查到。首先，检查局部的 `__dict__`。如果没有找到，通过 `__class__` 查找类的  `__dict__`。

```python
>>> s = Stock(...)
>>> s.name
'GOOG'
>>> s.cost()
49010.0
>>>
```

This lookup scheme is how the members of a *class* get shared by all instances.

类就是通过这样的查找模式，类成员被所有实例共享。

### How inheritance works继承是如何工作的

Classes may inherit from other classes.

类可以继承自其它的类：

```python
class A(B, C):
    ...
```

The base classes are stored in a tuple in each class.

在每个类中，基类存储在一个元组里面。

```python
>>> A.__bases__
(<class '__main__.B'>, <class '__main__.C'>)
>>>
```

This provides a link to parent classes.

这提供指向父类的链接。

### Reading Attributes with Inheritance使用多继承读取属性

Logically, the process of finding an attribute is as follows. First,
check in local `__dict__`.  If not found, look in `__dict__` of the
class.  If not found in class, look in the base classes through
`__bases__`.   However, there are some subtle aspects of this discussed next.

逻辑上，查找属性的过程如下所示：首先，检查局部的 `__dict__`。如果没有找到，检查类的 `__dict__`。如果在类中还是没有找到，通过 `__bases__` 在基类中查找。这里面有一些小细节，我们接下来再讨论。

### Reading Attributes with Single Inheritance使用单继承读取属性

In inheritance hierarchies, attributes are found by walking up the
inheritance tree in order.

在继承层级结构中，通过按顺序遍历继承树找到属性。

```python
class A: pass
class B(A): pass
class C(A): pass
class D(B): pass
class E(D): pass
```
With single inheritance, there is single path to the top.
You stop with the first match.

在单继承中，因为到达上层父类只有一条路径，所以当第一个匹配的时候即可停止。

### Method Resolution Order or MRO方法解析顺序（MRO）

Python precomputes an inheritance chain and stores it in the *MRO* attribute on the class.
You can view it.

Python 预先计算继承链并将其存储到类的 *MRO* 属性中。你可以通过 __mro__ 属性查看：

```python
>>> E.__mro__
(<class '__main__.E'>, <class '__main__.D'>,
 <class '__main__.B'>, <class '__main__.A'>,
 <type 'object'>)
>>>
```

This chain is called the **Method Resolution Order**.  To find an
attribute, Python walks the MRO in order. The first match wins.

该继承链称为 **方法解析顺序（Method Resolution Order）**.要查找一个属性，Python 按顺序遍历 MRO，第一个找到既是（译注：关于MRO更多信息，可查看PEP 253，或者查看：https://www.python.org/download/releases/2.3/mro/）。

### MRO in Multiple Inheritance多继承中的MRO

With multiple inheritance, there is no single path to the top.
Let's take a look at an example.

使用多继承，到达上层父类的路径有很多条，请看示例：

```python
class A: pass
class B: pass
class C(A, B): pass
class D(B): pass
class E(C, D): pass
```

What happens when you access an attribute?

当访问属性的时候会发生什么？

```python
e = E()
e.attr
```

An attribute search process is carried out, but what is the order? That's a problem.

按照什么顺序执行属性查找？这是个问题。

Python uses *cooperative multiple inheritance* which obeys some rules
about class ordering.

Python 采用*协作多重继承（cooperative multiple inheritance）*，协作多继承遵守的类排序规则如下：

* 总是在检查父类之前检查子类
* 父类（如果有多个）总是按照列出的顺序检查

* Children are always checked before parents
* Parents (if multiple) are always checked in the order listed.

The MRO is computed by sorting all of the classes in a hierarchy
according to those rules.

根据该规则， 通过按层级结构对所有的类进行排序，然后计算出 MRO。

```python
>>> E.__mro__
(
  <class 'E'>,
  <class 'C'>,
  <class 'A'>,
  <class 'D'>,
  <class 'B'>,
  <class 'object'>)
>>>
```

The underlying algorithm is called the "C3 Linearization Algorithm."
The precise details aren't important as long as you remember that a
class hierarchy obeys the same ordering rules you might follow if your
house was on fire and you had to evacuate--children first, followed by
parents.

底层算法称为“C3线性化算法（C3 Linearization Algorithm）”，精确的细节不重要，只要记住类层级结构遵守的排序规则如下：如果你家房子着火了，你必须得先撤走孩子，然后才是父母。

### An Odd Code Reuse (Involving Multiple Inheritance)奇怪的代码重用（涉及多继承）

Consider two completely unrelated objects:

考虑以下两个完全不相关的对象：

```python
class Dog:
    def noise(self):
        return 'Bark'

    def chase(self):
        return 'Chasing!'

class LoudDog(Dog):
    def noise(self):
        # Code commonality with LoudBike (below)
        return super().noise().upper()
```

And

```python
class Bike:
    def noise(self):
        return 'On Your Left'

    def pedal(self):
        return 'Pedaling!'

class LoudBike(Bike):
    def noise(self):
        # Code commonality with LoudDog (above)
        return super().noise().upper()
```

There is a code commonality in the implementation of `LoudDog.noise()` and
`LoudBike.noise()`.  In fact, the code is exactly the same.  Naturally,
code like that is bound to attract software engineers.

`LoudDog.noise()` 方法和`LoudBike.noise()` 方法中有一些通用的代码。事实上，这些通用的代码是完全一样的。当然，这样的代码一定会吸引工程师。

### The "Mixin" Pattern "Mixin" 模式

The *Mixin* pattern is a class with a fragment of code.

*Mixin* 模式（pattern）是包含一部分代码片段的类。

```python
class Loud:
    def noise(self):
        return super().noise().upper()
```

This class is not usable in isolation.
It mixes with other classes via inheritance.

该类在单独的环境中不可用。通过继承和其它类混合。

```python
class LoudDog(Loud, Dog):
    pass

class LoudBike(Loud, Bike):
    pass
```

Miraculously, loudness was now implemented just once and reused
in two completely unrelated classes.  This sort of trick is one
of the primary uses of multiple inheritance in Python.

神奇地，loudness 只实现了一次，却在两个完全不相关的类中使用。这种技巧是 Python 多继承的主要用途之一。

### Why `super()`为什么使用 `super()`

Always use `super()` when overriding methods.

当要覆盖一个方法的时候，总是使用 `super()` 函数。

```python
class Loud:
    def noise(self):
        return super().noise().upper()
```

`super()` delegates to the *next class* on the MRO.

`super()` 函数代表 MRO 中的*下一个类*。

The tricky bit is that you don't know what it is.  You especially don't
know what it is if multiple inheritance is being used.

麻烦的是你不知道它是什么，尤其是使用多继承的时候。

### Some Cautions注意事项

Multiple inheritance is a powerful tool. Remember that with power
comes responsibility.  Frameworks / libraries sometimes use it for
advanced features involving composition of components.  Now, forget
that you saw that.

多继承是一种强大的机制。使用这种强大的机制时请牢记“权利越大，责任越大”。有时候，框架或者库使用多继承来实现一些高级特性，如组件组合。

## 练习

In Section 4, you defined a class `Stock` that represented a holding of stock.
In this exercise, we will use that class.  Restart the interpreter and make a
few instances:

在第 4 节中，定义了一个表示股票持有信息的类 `Stock`。在本节练习中，我们将使用这个类。请重新启动解释器并创建一些  `Stock` 类的实例：

```python
>>> ================================ RESTART ================================
>>> from stock import Stock
>>> goog = Stock('GOOG',100,490.10)
>>> ibm  = Stock('IBM',50, 91.23)
>>>
```

### 练习 5.1：实例表示Representation of Instances

At the interactive shell, inspect the underlying dictionaries of the
two instances you created:

在交互式 shell 中，检查 `goog` 和 `ibm` 两个实例的底层字典：

```python
>>> goog.__dict__
... look at the output ...
>>> ibm.__dict__
... look at the output ...
>>>
```

### 练习 5.2：修改实例属性 Modification of Instance Data

Try setting a new attribute on one of the above instances:

尝试给实例添加一个新的属性。

```python
>>> goog.date = '6/11/2007'
>>> goog.__dict__
... look at output ...
>>> ibm.__dict__
... look at output ...
>>>
```

In the above output, you'll notice that the `goog` instance has a
attribute `date` whereas the `ibm` instance does not.  It is important
to note that Python really doesn't place any restrictions on
attributes.  For example, the attributes of an instance are not
limited to those set up in the `__init__()` method.

在上述输出中，你会发现  `goog` 实例添加了一个 `date` 属性，但是  `ibm`  实例没有。需要注意的是，实际上 Python 对实例属性没有设置任何限制。例如，实例属性对 `__init__()`  方法中的设置没有任何的限制。

Instead of setting an attribute, try placing a new value directly into
the `__dict__` object:

尝试直接添加一个新的值到字典中：

```python
>>> goog.__dict__['time'] = '9:45am'
>>> goog.time
'9:45am'
>>>
```

Here, you really notice the fact that an instance is just a layer on
top of a dictionary.  Note: it should be emphasized that direct
manipulation of the dictionary is uncommon--you should always write
your code to use the (.) syntax.

在这里，你真的发现实例仅仅是字典的顶层。注意：没必要强调对字典直接操作是不常见的——你应该总是使用语法 (.)  编写代码。

### 练习 5.3：类的角色The role of classes

The definitions that make up a class definition are shared by all
instances of that class.  Notice, that all instances have a link back
to their associated class:

构成类定义的所有定义被所有类的所有实例所共享。注意，所有的实例都有一个链接，指向它们的关联类：

```python
>>> goog.__class__
... look at output ...
>>> ibm.__class__
... look at output ...
>>>
```

Try calling a method on the instances:

尝试在实例上调用方法：

```python
>>> goog.cost()
49010.0
>>> ibm.cost()
4561.5
>>>
```

Notice that the name 'cost' is not defined in either `goog.__dict__`
or `ibm.__dict__`.  Instead, it is being supplied by the class
dictionary.  Try this:

名字 'cost'  即不在 `goog.__dict__`  中定义，也不在 `ibm.__dict__`中定义。相反，而是又类字典提供的。请尝试以下代码：

```python
>>> Stock.__dict__['cost']
... look at output ...
>>>
```

Try calling the `cost()` method directly through the dictionary:

尝试直接通过字典条用  `cost()`  方法：

```python
>>> Stock.__dict__['cost'](goog)
49010.0
>>> Stock.__dict__['cost'](ibm)
4561.5
>>>
```

Notice how you are calling the function defined in the class
definition and how the `self` argument gets the instance.

你是如何调用类定义中的函数，那么 `self` 就是怎么调用实例的。

Try adding a new attribute to the `Stock` class:

尝试添加一个新的属性到 `Stock` 类：

```python
>>> Stock.foo = 42
>>>
```

Notice how this new attribute now shows up on all of the instances:

该新属性会出现在所有实例中：

```python
>>> goog.foo
42
>>> ibm.foo
42
>>>
```

However, notice that it is not part of the instance dictionary:

但是，这并不是实例字典的一部分。

```python
>>> goog.__dict__
... look at output and notice there is no 'foo' attribute ...
>>>
```

The reason you can access the `foo` attribute on instances is that
Python always checks the class dictionary if it can't find something
on the instance itself.

你可以访问 `foo`  属性的原因是：当 Python 在实例字典中查找不到某个属性时，那么它就会到类字典中查找。

Note: This part of the exercise illustrates something known as a class
variable.  Suppose, for instance, you have a class like this:

注意：本部分阐明了什么是类变量。假设你有这样一个类：

```python
class Foo(object):
     a = 13                  # Class variable
     def __init__(self,b):
         self.b = b          # Instance variable
```

In this class, the variable `a`, assigned in the body of the
class itself, is a "class variable."  It is shared by all of the
instances that get created.  For example:

在 Foo 类中，因为变量 `a` 在类体（body of the class）中被赋值，所以 `a`  是“类变量（class variable）”。变量 `a` 可以被 Foo 类的所有实例所共享。示例：

```python
>>> f = Foo(10)
>>> g = Foo(20)
>>> f.a          # Inspect the class variable (same for both instances)
13
>>> g.a
13
>>> f.b          # Inspect the instance variable (differs)
10
>>> g.b
20
>>> Foo.a = 42   # Change the value of the class variable
>>> f.a
42
>>> g.a
42
>>>
```

### 练习 5.4：绑定方法Bound methods

A subtle feature of Python is that invoking a method actually involves
two steps and something known as a bound method.   For example:

 Python 的一个小特性是：调用方法实际上涉及两个步骤以及一个称为绑定方法的东西。示例：

```python
>>> s = goog.sell
>>> s
<bound method Stock.sell of Stock('GOOG', 100, 490.1)>
>>> s(25)
>>> goog.shares
75
>>>
```

Bound methods actually contain all of the pieces needed to call a
method.  For instance, they keep a record of the function implementing
the method:

实际上，绑定方法包含所有需要调用的方法。例如，它们记录了实现方法的函数：

```python
>>> s.__func__
<function sell at 0x10049af50>
>>>
```

This is the same value as found in the `Stock` dictionary.

这与在  `Stock`  字典中找到的值是一样的：

```python
>>> Stock.__dict__['sell']
<function sell at 0x10049af50>
>>>
```

Bound methods also record the instance, which is the `self`
argument.

绑定方法的  `self` 参数用于记录实例：

```python
>>> s.__self__
Stock('GOOG',75,490.1)
>>>
```

When you invoke the function using `()` all of the pieces come
together.  For example, calling `s(25)` actually does this:

你可以使用 `()` 一起执行所有的函数。例如，调用 `s(25)` 实际是进行如下操作：

```python
>>> s.__func__(s.__self__, 25)    # Same as s(25)
>>> goog.shares
50
>>>
```

### 练习 5.5：继承Inheritance

Make a new class that inherits from `Stock`.

创建一个继承自 `Stock` 的类：

```
>>> class NewStock(Stock):
        def yow(self):
            print('Yow!')

>>> n = NewStock('ACME', 50, 123.45)
>>> n.cost()
6172.50
>>> n.yow()
Yow!
>>>
```

Inheritance is implemented by extending the search process for attributes.
The `__bases__` attribute has a tuple of the immediate parents:

继承是通过扩展属性的搜索过程实现的。`__bases__` 属性是一个包含直接父类的元组。

```python
>>> NewStock.__bases__
(<class 'stock.Stock'>,)
>>>
```

The `__mro__` attribute has a tuple of all parents, in the order that
they will be searched for attributes.

`__mro__`  属性是一个包含所有父类的元组，父类按搜索顺序排列。

```python
>>> NewStock.__mro__
(<class '__main__.NewStock'>, <class 'stock.Stock'>, <class 'object'>)
>>>
```

Here's how the `cost()` method of instance `n` above would be found:

```python
>>> for cls in n.__class__.__mro__:
        if 'cost' in cls.__dict__:
            break

>>> cls
<class '__main__.Stock'>
>>> cls.__dict__['cost']
<function cost at 0x101aed598>
>>>
```

[目录](../Contents.md) \| [上一节 (4.4 异常)](../04_Classes_objects/04_Defining_exceptions.md) \| [下一节 (5.2 封装)](02_Classes_encapsulation.md)