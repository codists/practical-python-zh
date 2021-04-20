[目录](../Contents.md) \| [上一节 (5.1 再谈字典)](01_Dicts_revisited.md) \| [下一节 (6 生成器)](../06_Generators/00_Overview.md)

# 5.2 类和封装

创建类时，通常会尝试将类的内部细节进行封装。本节介绍 Python 编程中有关封装的习惯用法（包括私有变量和私有属性）。

### Public vs Private

虽然类的主要作用之一是封装对象的属性和内部实现细节。但是，类还定义了外界用来操作该对象的公有接口（public interface）。实现细节与公有接口之间的区别很重要。

### 问题

在 Python 中，几乎所有与类和对象有关的东西都是*开放（open）*的。

* 可以轻松地查看对象的内部细节。
* 可以随意地修改。
* 没有访问控制的概念（例如：私有类成员）。

如何隔离*内部实现*的细节，这是一个问题。

### Python 封装

Python 依赖编程约定来指示某些东西的用途。这就约定基于命名。有一种普遍的态度是，程序员应该遵守规则，而不是让语言来强制执行规则。

### 私有属性

以下划线  `_` 开头的任何属性被认为是私有的（private）。

```python
class Person(object):
    def __init__(self, name):
        self._name = 0
```

如前所述，这这是一种编程风格。你仍然可以对这些私有属性进行访问和修改。

```python
>>> p = Person('Guido')
>>> p._name
'Guido'
>>> p._name = 'Dave'
>>>
```

一般来说，一个以下划线  `_`  开头的名称被认为是内部实现，无论该名称是变量名、函数名还是模块名。如果你发现自己直接使用这些名称，那么你可能在做一些错误的事情。你应该寻找更高级的功能。

### 简单属性

考虑下面这个类：

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
```

这里有一个让人惊讶的特性，你可以给属性设置任何值：

```python
>>> s = Stock('IBM', 50, 91.1)
>>> s.shares = 100
>>> s.shares = "hundred"
>>> s.shares = [1, 0, 0]
>>>
```

你可能会想要对此进行检查（译注：例如 `shares ` 表示的是股份数目，值应该是整数。所以给  `shares ` 赋值时应该对值进行检查。如果检查发现给 `shares ` 赋的值不是整数，那么应该触发一个  `TypeError` 异常）：

```python
s.shares = '50'     # Raise a TypeError, this is a string
```

这时候你会怎么做？

### 托管属性

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

糟糕的是，这破坏了我们的已有代码。例如：之前是通过 `s.shares = 50` 给 `shares ` 赋值的，那么现在就要改成`s.set_shares(50)` 给 `shares ` 赋值，这很不好。

### 特征属性（Properties）

方法二：

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

现在，普通属性（normal attribute）的访问触发了 `@property` 和 `@shares.setter` 下的 getter 方法和 setter 方法。

```python
>>> s = Stock('IBM', 50, 91.1)
>>> s.shares         # Triggers @property
50
>>> s.shares = 75    # Triggers @shares.setter
>>>
```

使用该方法，不需要对源代码做任何修改。在类内（包括在 `__init__()` 方法内）有赋值的时候，直接调用新的 setter：

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

特征属性和私有名称（ private names）的使用之间经常会出现混淆。尽管特征属性内部使用的是私有名称，如 `_shares`。类的其它地方（不是特征属性），仍可以继续使用诸如  `shares` 这样的名称。

特征属性对于计算数据属性也非常有用。

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

这允许你删除 cost 后面的括号，隐藏 cost 是一个方法的事实：

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> s.shares # Instance variable
100
>>> s.cost   # Computed Value
49010.0
>>>
```

### 统一访问

最后一个例子展示了如何在对象上放置一个更加统一的接口。如果不这样做，对象使用起来可能会令人困惑。

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> a = s.cost() # Method
49010.0
>>> b = s.shares # Data attribute
100
>>>
```

为什么 cost 后面需要加上括号 `()`，但是 shares 却不需要？ 特征属性可以解决这个问题。

### 装饰器语法

`@` 语法称为“装饰（decoration）”。它指定了一个修饰符（modifier），应用于紧接其后的函数定义：

```python
...
@property
def cost(self):
    return self.shares * self.price
```

更多细节在 [第 7 节](../07_Advanced_Topics/00_Overview) 中给到。

###  插槽属性（`__slots__`）

你可以使用 `__slots__` 限制属性名称集：

```python
class Stock:
    __slots__ = ('name','_shares','price')
    def __init__(self, name, shares, price):
        self.name = name
        ...
```

使用其它属性时，将会触发错误：

```python
>>> s.price = 385.15
>>> s.prices = 410.2
Traceback (most recent call last):
File "<stdin>", line 1, in ?
AttributeError: 'Stock' object has no attribute 'prices'
```

尽管这样可以防止错误和限制对象的使用，但实际上使用 `__slots__` 是为了提高性能，提高 Python 利用内存的效率。

### 关于封装的最终说明

不要滥用私有属性（private attributes），特征属性（properties），插槽属性（slots）等。它们有特殊的用途，你在阅读其它 Python 代码时可能会看到。但是，对于大多数日常编码而言，它们不是必需的。

## 练习

### 练习 5.6：简单特征属性

使用特征属性是一种非常有用的给对象添加“计算属性”的方式。虽然你在 `stock.py` 文件中创建了 `Stock` 对象，但是请注意，在 `Stock` 对象上 ，对于不同类型的属性，获取方式稍微有点不同。

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

具体来说，`cost` 后面之所以要添加括号，是因为 `cost` 是一个方法。

如果你想去掉 `cost()` 的括号，那么可以把该方法转为一个特征属性。请修改 `Stock` 类，使其像下面这样计算所持有股票的总价:

```python
>>> ================================ RESTART ================================
>>> from stock import Stock
>>> s = Stock('GOOG', 100, 490.1)
>>> s.cost
49010.0
>>>
```

尝试将 `cost`作为方法调用（`s.cost()`），你会发现，现在已经被定义为特征属性的 `cost` 无法作为方法被调用。

```python
>>> s.cost()
... fails ...
>>>
```

这些更改很可能会破坏你之前的 `pcost.py` 程序，所以，你可能需要返回到 `pcost.py` 中去掉 `cost()` 方法后面的括号`()`。

### 练习 5.7：特征属性和 Setters

请修改  `shares` 属性，以便将该值存储在私有属性中，并且使用属性函数（property functions）确保赋给 `shares` 的值总是整数。预期行为示例：

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

### 练习 5.8：添加插槽属性（slots）

请修改 `Stock` 类，以便 `Stock` 类拥有一个  `__slots__` 属性。然后确认无法添加新属性:

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

 使用 `__slots__` 时，Python 使用更高效的对象内部表示。如果你尝试查看实例 `s` 的底层字典会发生什么？

```python
>>> s.__dict__
... see what happens ...
>>>
```

应当指出， `__slots__` 作为数据结构是类中最常用的一种优化。使用插槽属性使程序占用更少的内存，运行更快。但是，在其它大多数类中，你应该尽可能避免使用 `__slots__` 。

[目录](../Contents.md) \| [上一节 (5.1 再谈字典)](01_Dicts_revisited.md) \| [下一节 (6 生成器)](../06_Generators/00_Overview.md)