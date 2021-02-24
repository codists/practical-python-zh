[目录](../Contents.md) \| [上一节 (4.4 异常)](../04_Classes_objects/04_Defining_exceptions.md) \| [下一节 (5.2 封装)](02_Classes_encapsulation.md)

# 5.1 再谈字典

Python 对象系统主要基于字典实现。本节将对此进行讨论。

### 字典

字典是命名值（named values）的集合。

```python
stock = {
    'name' : 'GOOG',
    'shares' : 100,
    'price' : 490.1
}
```

虽然字典常用于简单的数据结构，但是字典也用于解释器的关键部分。字典可能是 *Python 中最重要的数据类型*。

### 字典和模块

在模块内，字典存储所有的全局变量和函数。

```python
# foo.py

x = 42
def bar():
    ...

def spam():
    ...
```

可以通过 `foo.__dict__` 或 `globals()`  查看该字典。

```python
{
    'x' : 42,
    'bar' : <function bar>,
    'spam' : <function spam>
}
```

### 字典和对象

用户定义对象的时候也使用到了实例字典和类字典。事实上，整个对象系统主要是基于字典实现的。

字典存储实例数据，如 `__dict__`：

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> s.__dict__
{'name' : 'GOOG', 'shares' : 100, 'price': 490.1 }
```

当给 `self` 赋值的时候，你将填充该字典（和实例）。

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
```

实例数据 `self.__dict__`  看起来像下面这样：

```python
{
    'name': 'GOOG',
    'shares': 100,
    'price': 490.1
}
```

**每一个实例都拥有自己的私有字典。**

```python
s = Stock('GOOG', 100, 490.1)     # {'name' : 'GOOG','shares' : 100, 'price': 490.1 }
t = Stock('AAPL', 50, 123.45)     # {'name' : 'AAPL','shares' : 50, 'price': 123.45 }
```

如果你创建了某个类的 100 个实例，那么就会有 100 个存储数据的字典。

### 类成员

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

使用 `Stock.__dict__` 可以查看该字典：

```python
{
    'cost': <function>,
    'sell': <function>,
    '__init__': <function>
}
```

### 实例和类

实例和类是链接在一起的。实例通过 `__class__` 属性指向类。

```python
>>> s = Stock('GOOG', 100, 490.1)
>>> s.__dict__
{ 'name': 'GOOG', 'shares': 100, 'price': 490.1 }
>>> s.__class__
<class '__main__.Stock'>
>>>
```

实例字典存储的数据对每个实例而言是唯一的。但是，类字典存储的数据被该类的所有实例共享。

### 属性访问

使用对象时，可以通过 `.`  运算符访问数据和方法。

```python
x = obj.name          # Getting
obj.name = value      # Setting
del obj.name          # Deleting
```

这些操作直接与字典绑定到一起。

### 修改实例

修改对象的操作会更新底层字典：

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

### 读取属性

假设你要读取实例上的属性：

```python
x = obj.name
```

该属性可能位于两个地方：

* 局部实例字典
* 类字典

两种字典都会被检查到。首先，检查局部实例字典 `__dict__`。如果没有找到，通过 `__class__` 查找类字典  `__dict__`。

```python
>>> s = Stock(...)
>>> s.name
'GOOG'
>>> s.cost()
49010.0
>>>
```

通过这样的查找模式，类成员被所有实例共享。

### 继承的工作原理

一个类可能继承自其它类：

```python
class A(B, C):
    ...
```

在每个类中，父类存储在一个元组中：

```python
>>> A.__bases__
(<class '__main__.B'>, <class '__main__.C'>)
>>>
```

子类通过 ` __bases__ ` 属性可以链接到父类。

### 多继承中的属性查找

从逻辑上讲，查找属性的过程如下：首先，检查局部字典 `__dict__`。如果没有找到，检查类字典 `__dict__`。如果在类中还是没有找到，通过 `__bases__` 属性在父类中查找。这里面有一些小细节，我们接下来讨论。

### 单继承中的属性查找

在继承层级结构中，通过按顺序遍历继承树来找到属性。

```python
class A: pass
class B(A): pass
class C(A): pass
class D(B): pass
class E(D): pass
```
在单继承中，因为到达上层父类的路径只有一条，所以当找到第一个匹配的属性时即可停止。

### 方法解析顺序（MRO）

Python 会预先计算继承链并将其存储到类的 *MRO* 属性中。你可以像这样查看：

```python
>>> E.__mro__
(<class '__main__.E'>, <class '__main__.D'>,
 <class '__main__.B'>, <class '__main__.A'>,
 <type 'object'>)
>>>
```

该继承链称为 **方法解析顺序（Method Resolution Order）**。为了找到属性，Python 按顺序遍历 MRO，第一个匹配的属性即是要找的属性。（译注：有关 MRO 的更多信息，请查看 https://www.python.org/download/releases/2.3/mro/）。

### 多继承中的方法解析顺序

使用多继承时，到达上层父类的路径有很多条，请看示例：

```python
class A: pass
class B: pass
class C(A, B): pass
class D(B): pass
class E(C, D): pass
```

访问属性时会发生什么？

```python
e = E()
e.attr
```

会执行属性查找，那么按什么顺序查找呢？这是个问题。

Python 使用的是 *协作多重继承（cooperative multiple inheritance）*，协作多继承遵守的类排序规则如下：

* 总是在检查父类之前检查子类
* 父类（如果有多个）总是按照列出的顺序检查


根据该规则， 通过按层级结构对所有的类进行排序，然后计算出方法解析顺序。

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

底层算法称为“C3线性化算法（C3 Linearization Algorithm）”，确切的细节不重要，只要记住类层级结构遵守的排序规则与你家房子着火后必须撤离时遵守的规则相同：首先是孩子，其次是父母。

### 奇怪的代码重用（涉及多继承）

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

和

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

`LoudDog.noise()` 方法和`LoudBike.noise()` 方法中有一些通用的代码。事实上，这些通用的代码是完全一样的。自然，这样的代码势必会吸引软件工程师。

### "Mixin" 模式

*Mixin* 模式（pattern）是包含一部分代码片段的类。

```python
class Loud:
    def noise(self):
        return super().noise().upper()
```

该类不能单独使用。通过继承和其它类混合使用。

```python
class LoudDog(Loud, Dog):
    pass

class LoudBike(Loud, Bike):
    pass
```

神奇的是，`noise() ` 方法只实现了一次，却在两个完全不相关的类中使用。这种技巧是 Python 多继承的主要用途之一。

### 为什么使用 `super()`

当要覆盖一个方法的时候，总是使用 `super()` 函数。

```python
class Loud:
    def noise(self):
        return super().noise().upper()
```

`super()` 函数代表 MRO 中的*下一个类*（译注：LoudDog 的 MRO 是 `LoudDog>Loud>Dog>object`。因为 Loud 的父类 object 没有定义 noise() 方法，所以 LoudDog 的实例在 Loud 中找不到 noise() 方法。然后  LoudDog 的实例就会到 MRO 中 Loud 的下一个类 Dog 中寻找）。

麻烦的是你不知道它是什么，尤其是使用多继承的时候。

### 注意事项

多继承是一种强大的机制。使用这种强大的机制时请牢记“权利越大，责任越大”。有时候，框架或者库使用多继承来实现一些高级特性，如组件组合。

## 练习

在第 4 节中，定义了一个表示股票持有信息的类 `Stock`。在本节练习中，我们将使用该类。请重新启动解释器并创建一些  `Stock` 类的实例：

```python
>>> ================================ RESTART ================================
>>> from stock import Stock
>>> goog = Stock('GOOG',100,490.10)
>>> ibm  = Stock('IBM',50, 91.23)
>>>
```

### 练习 5.1：实例的表示

在交互式 shell 中，检查 `goog` 和 `ibm` 两个实例的底层字典：

```python
>>> goog.__dict__
... look at the output ...
>>> ibm.__dict__
... look at the output ...
>>>
```

### 练习 5.2：修改实例属性

尝试给上述其中一个实例添加新属性：

```python
>>> goog.date = '6/11/2007'
>>> goog.__dict__
... look at output ...
>>> ibm.__dict__
... look at output ...
>>>
```

在上述输出中，你会发现  `goog` 实例具有 `date` 属性，但是  `ibm`  实例没有。重要的是要注意，Python 对实例属性确实没有任何限制。例如，实例属性不限于 `__init__()`  方法中设置的属性。

尝试直接添加一个新的值到 `__dict__` 对象中：

```python
>>> goog.__dict__['time'] = '9:45am'
>>> goog.time
'9:45am'
>>>
```

在这里，你会发现一个事实，实例仅仅是字典顶部的一层。注意：应该强调的是，直接操作字典并不常见——你应该始终使用语法 (.)  编写代码。

### 练习 5.3：类的角色

类中的定义被类的所有实例所共享。所有的实例都有一个链接，指向它们的关联类：

```python
>>> goog.__class__
... look at output ...
>>> ibm.__class__
... look at output ...
>>>
```

尝试在实例上调用方法：

```python
>>> goog.cost()
49010.0
>>> ibm.cost()
4561.5
>>>
```

名字 'cost'  既不在 `goog.__dict__`  中定义，也不在 `ibm.__dict__`中定义。相反，而是由类字典提供的。请尝试以下代码：

```python
>>> Stock.__dict__['cost']
... look at output ...
>>>
```

尝试直接通过字典调用  `cost()`  方法：

```python
>>> Stock.__dict__['cost'](goog)
49010.0
>>> Stock.__dict__['cost'](ibm)
4561.5
>>>
```

你是如何调用类中定义的函数，那么 `self` 就是怎么调用实例的。

尝试给 `Stock` 类添加新属性：：

```python
>>> Stock.foo = 42
>>>
```

该新属性会出现在所有实例中：

```python
>>> goog.foo
42
>>> ibm.foo
42
>>>
```

但是，`foo` 并不属于实例字典：

```python
>>> goog.__dict__
... look at output and notice there is no 'foo' attribute ...
>>>
```

你可以访问 `foo`  属性的原因是：当 Python 在实例字典中查找不到某个属性时，那么它就会到类字典中查找。

注意：本部分主要阐明什么是类变量。假设你有这样一个类：

```python
class Foo(object):
     a = 13                  # Class variable
     def __init__(self,b):
         self.b = b          # Instance variable
```

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

### 练习 5.4：绑定方法

 Python 有一个微妙的特性：调用方法实际上涉及两个步骤以及一个称为绑定方法的东西。示例：

```python
>>> s = goog.sell
>>> s
<bound method Stock.sell of Stock('GOOG', 100, 490.1)>
>>> s(25)
>>> goog.shares
75
>>>
```

实际上，绑定方法包含调用一个方法的所需的所有内容。例如，它们记录了实现方法的函数：

```python
>>> s.__func__
<function sell at 0x10049af50>
>>>
```

这与在  `Stock`  字典中找到的值是一样的：

```python
>>> Stock.__dict__['sell']
<function sell at 0x10049af50>
>>>
```

绑定方法还记录实例，即 `self`：

```python
>>> s.__self__
Stock('GOOG',75,490.1)
>>>
```

你可以使用 `()` 一起调用所有的函数。例如，调用 `s(25)` 实际是这样做的：

```python
>>> s.__func__(s.__self__, 25)    # Same as s(25)
>>> goog.shares
50
>>>
```

### 练习 5.5：继承

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

通过扩展属性的搜索过程来实现继承。`__bases__` 属性是一个包含直接父类的元组：

```python
>>> NewStock.__bases__
(<class 'stock.Stock'>,)
>>>
```

`__mro__`  属性是一个包含所有父类的元组，父类按查找顺序排列。

```python
>>> NewStock.__mro__
(<class '__main__.NewStock'>, <class 'stock.Stock'>, <class 'object'>)
>>>
```

实例 `n` 是这找到  `cost()` 方法的： 

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