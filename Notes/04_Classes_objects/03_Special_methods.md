[目录](../Contents.md) \| [上一节 (4.2 继承)](02_Inheritance.md) \| [下一节 (4.4 异常)](04_Defining_exceptions.md)

# 4.3 特殊方法

可以通过特殊方法（或者称为"魔术"方法（magic method））自定义 Python 行为的各部分。本节介绍特殊方法的思想。此外，还将讨论动态属性访问和绑定方法。

### 简介

类可以定义特殊方法。特殊方法对于 Python 解释器而言具有特殊的意义。特殊方法总是以双下划线  `__`  开头和结尾，例如 `__init__`。

```python
class Stock(object):
    def __init__(self):
        ...
    def __repr__(self):
        ...
```

虽然有很多特殊方法，但是我们只研究几个具体的例子。

### 字符串转换的特殊方法

对象有两种字符串表示形式。

```python
>>> from datetime import date
>>> d = date(2012, 12, 21)
>>> print(d)
2012-12-21
>>> d
datetime.date(2012, 12, 21)
>>>
```

`str()` 函数用于创建格式良好的、可打印的输出：

```python
>>> str(d)
'2012-12-21'
>>>
```

`repr()` 函数用于创建详细的、面向程序员的输出。

```python
>>> repr(d)
'datetime.date(2012, 12, 21)'
>>>
```

`str()` 和 `repr()` 函数都是使用类中定义的特殊方法生成要显示的字符串。

```python
class Date(object):
    def __init__(self, year, month, day):
        self.year = year
        self.month = month
        self.day = day

    # Used with `str()`
    def __str__(self):
        return f'{self.year}-{self.month}-{self.day}'

    # Used with `repr()`
    def __repr__(self):
        return f'Date({self.year},{self.month},{self.day})'
```

*注意：按照惯例，`__repr__()` 返回一个字符串，当该字符串被传递给 `eval()` 函数，将会重新创建底层对象（译注：`eval(repr(obj)) == obj`）。如果不行，则使用某种易于阅读的表现形式。* 

### 数学操作的特殊方法

数学运算符涉及的特殊方法如下：

```python
a + b       a.__add__(b)
a - b       a.__sub__(b)
a * b       a.__mul__(b)
a / b       a.__truediv__(b)
a // b      a.__floordiv__(b)
a % b       a.__mod__(b)
a << b      a.__lshift__(b)
a >> b      a.__rshift__(b)
a & b       a.__and__(b)
a | b       a.__or__(b)
a ^ b       a.__xor__(b)
a ** b      a.__pow__(b)
-a          a.__neg__()
~a          a.__invert__()
abs(a)      a.__abs__()
```

### 元素访问的特殊方法

这些是实现容器的特殊方法：

```python
len(x)      x.__len__()
x[a]        x.__getitem__(a)
x[a] = v    x.__setitem__(a,v)
del x[a]    x.__delitem__(a)
```

你可以在类中使用这些特殊方法。

```python
class Sequence:
    def __len__(self):
        ...
    def __getitem__(self,a):
        ...
    def __setitem__(self,a,v):
        ...
    def __delitem__(self,a):
        ...
```

### 方法调用

调用方法有两个步骤。

​	1、查找：`.` 运算符

​	2、方法调用： `()` 运算符

```python
>>> s = Stock('GOOG',100,490.10)
>>> c = s.cost  # Lookup
>>> c
<bound method Stock.cost of <Stock object at 0x590d0>>
>>> c()         # Method call
49010.0
>>>
```

### 绑定方法

尚未被函数调用运算符 `()`  调用的方法称为绑定方法（ 译注：bound method，如果直译应该译作“绑定的方法”，但是，就像“类方法”一样，可以省略“的”这个字，译为“绑定方法”，绑定在这里不是动词，而应理解为形容词“绑定的”）。它对自己生成的实例进行操作：

```python
>>> s = Stock('GOOG', 100, 490.10)
>>> s
<Stock object at 0x590d0>
>>> c = s.cost
>>> c
<bound method Stock.cost of <Stock object at 0x590d0>>
>>> c()
49010.0
>>>
```

如果使用绑定方法时有些大意，那么容易导致错误。示例：

```python
>>> s = Stock('GOOG', 100, 490.10)
>>> print('Cost : %0.2f' % s.cost)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: float argument required
>>>
```

或者：

```python
f = open(filename, 'w')
...
f.close     # Oops, Didn't do anything at all. `f` still open.
```

在这两种情形中，错误都是由忘记尾部括号引起的。例如：`s.cost()` or `f.close()`。

### 属性访问

还有一种访问、操作和管理属性的替代方法。

```python
getattr(obj, 'name')          # Same as obj.name
setattr(obj, 'name', value)   # Same as obj.name = value
delattr(obj, 'name')          # Same as del obj.name
hasattr(obj, 'name')          # Tests if attribute exists
```

示例：

```python
if hasattr(obj, 'x'):
    x = getattr(obj, 'x'):
else:
    x = None
```

*注意： `getattr()` 函数的默认参数非常有用。*

```python
x = getattr(obj, 'x', None)
```

## 练习

### 练习 4.9：更好的输出

请修改 `stock.py` 文件中定义的  `Stock` 对象，以便 `__repr__()` 方法生成更有用的输出。示例：

```python
>>> goog = Stock('GOOG', 100, 490.1)
>>> goog
Stock('GOOG', 100, 490.1)
>>>
```

修改完成后，请查看读取股票投资组合时会发生什么，以及生成什么样的结果。示例：

```
>>> import report
>>> portfolio = report.read_portfolio('Data/portfolio.csv')
>>> portfolio
... see what the output is ...
>>>
```

### 练习 4.10：使用 getattr() 的例子

`getattr()` 是读取属性的另一种机制。可以使用它编写极其灵活的代码。首先，请尝试以下示例：

```python
>>> import stock
>>> s = stock.Stock('GOOG', 100, 490.1)
>>> columns = ['name', 'shares']
>>> for colname in columns:
        print(colname, '=', getattr(s, colname))

name = GOOG
shares = 100
>>>
```

仔细观察会发现输出数据完全由 `columns`  变量中列出的属性名决定。

在 `tableformat.py` 文件中，使用该思想将其扩展为通用函数  `print_table()`，`print_table()`打印一个表格，显示用户指定的任意对象的属性。与早期的 `print_report()` 函数一样，`print_table()` 方法还应接受一个 `TableFormatter` 实例来控制输出格式。它们的工作方式如下：

```python
>>> import report
>>> portfolio = report.read_portfolio('Data/portfolio.csv')
>>> from tableformat import create_formatter, print_table
>>> formatter = create_formatter('txt')
>>> print_table(portfolio, ['name','shares'], formatter)
      name     shares
---------- ----------
        AA        100
       IBM         50
       CAT        150
      MSFT        200
        GE         95
      MSFT         50
       IBM        100

>>> print_table(portfolio, ['name','shares','price'], formatter)
      name     shares      price
---------- ---------- ----------
        AA        100       32.2
       IBM         50       91.1
       CAT        150      83.44
      MSFT        200      51.23
        GE         95      40.37
      MSFT         50       65.1
       IBM        100      70.44
>>>
```

[目录](../Contents.md) \| [上一节 (4.2 继承)](02_Inheritance.md) \| [下一节 (4.4 异常)](04_Defining_exceptions.md)

