[目录](../Contents.md) \| [上一节 (7.4 装饰器)](04_Function_decorators.md) \| [下一节 (8 测试和调试)](../08_Testing_debugging/00_Overview.md)

# 7.5 装饰方法

本节讨论一些与方法定义结合使用的内置装饰器。

### 预定义的装饰器

在类定义中，有许多预定义的装饰器用于指定特殊类型的方法。

```python
class Foo:
    def bar(self,a):
        ...

    @staticmethod
    def spam(a):
        ...

    @classmethod
    def grok(cls,a):
        ...

    @property
    def name(self):
        ...
```

让我们逐个查看吧。

### 静态方法

`@staticmethod` 用于定义所谓的静态类方法（ *static* class method，来自于 C++/Java）。静态方法是一个函数，这个函数是类的一部分，但不是在实例上进行操作。

```python
class Foo(object):
    @staticmethod
    def bar(x):
        print('x =', x)

>>> Foo.bar(2) 
x=2
>>>
```

静态方法有时用于实现类的内部支持代码，例如，用于帮助管理已创建的实例（内存管理，系统资源，持久化，锁等等）。有时也用于某些设计模式（这里暂不讨论）。

### 类方法

`@classmethod` 用于定义类方法（class methods）。类方法是一种将 *类* 对象而不是实例作为第一个参数的方法。

```python
class Foo:
    def bar(self):
        print(self)

    @classmethod
    def spam(cls):
        print(cls)

>>> f = Foo()
>>> f.bar()
<__main__.Foo object at 0x971690>   # The instance `f`
>>> Foo.spam()
<class '__main__.Foo'>              # The class `Foo`
>>>
```

类方法常用作定义替代构造函数（constructor）的工具。

```python
import time
class Date:
    def __init__(self,year,month,day):
        self.year = year
        self.month = month
        self.day = day

    @classmethod
    def today(cls):
        # Notice how the class is passed as an argument
        tm = time.localtime()
        # And used to create a new instance
        return cls(tm.tm_year, tm.tm_mon, tm.tm_mday)

d = Date.today()
```

类方法可以和继承等特性一起使用以解决棘手的问题。

```python
class Date:
    ...
    @classmethod
    def today(cls):
        # Gets the correct class (e.g. `NewDate`)
        tm = time.localtime()
        return cls(tm.tm_year, tm.tm_mon, tm.tm_mday)

class NewDate(Date):
    ...

d = NewDate.today()
```

## 练习

### 练习 7.11：实践中的类方法

在 `report.py` 和 `portfolio.py` 文件中， `Portfolio` 类的创建稍微有点混乱。例如，`report.py`  程序具有如下代码：

```python
def read_portfolio(filename, **opts):
    '''
    Read a stock portfolio file into a list of dictionaries with keys
    name, shares, and price.
    '''
    with open(filename) as lines:
        portdicts = fileparse.parse_csv(lines,
                                        select=['name','shares','price'],
                                        types=[str,int,float],
                                        **opts)

    portfolio = [ Stock(**d) for d in portdicts ]
    return Portfolio(portfolio)
```

且 `portfolio.py` 文件中定义的  `Portfolio` 具有一个奇怪的初始化：

```python
class Portfolio:
    def __init__(self, holdings):
        self.holdings = holdings
    ...
```

坦白说，因为代码分散在各文件中，所以责任链稍微有点混乱。如果 `Portfolio` 类应该包含 `Stock` 类的实例列表，那么你应该修改该类以使其更清晰。示例：

```python
# portfolio.py

import stock

class Portfolio:
    def __init__(self):
        self.holdings = []

    def append(self, holding):
        if not isinstance(holding, stock.Stock):
            raise TypeError('Expected a Stock instance')
        self.holdings.append(holding)
    ...
```

如果想要从 CSV 文件中读取投资组合数据，那么你也许应该为此创建一个类方法：

```python
# portfolio.py

import fileparse
import stock

class Portfolio:
    def __init__(self):
        self.holdings = []

    def append(self, holding):
        if not isinstance(holding, stock.Stock):
            raise TypeError('Expected a Stock instance')
        self.holdings.append(holding)

    @classmethod
    def from_csv(cls, lines, **opts):
        self = cls()
        portdicts = fileparse.parse_csv(lines,
                                        select=['name','shares','price'],
                                        types=[str,int,float],
                                        **opts)

        for d in portdicts:
            self.append(stock.Stock(**d))

        return self
```

要使用新的 Portfolio 类，你可以这样编写代码：

```
>>> from portfolio import Portfolio
>>> with open('Data/portfolio.csv') as lines:
...     port = Portfolio.from_csv(lines)
...
>>>
```

请对 `Portfolio` 类进行修改，然后修改 `report.py` 的代码以使用类方法。

[目录](../Contents.md) \| [上一节 (7.4 装饰器)](04_Function_decorators.md) \| [下一节 (8 测试和调试)](../08_Testing_debugging/00_Overview.md)