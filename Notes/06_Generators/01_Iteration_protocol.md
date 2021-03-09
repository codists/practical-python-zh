[目录](../Contents.md) \| [上一节 (5.2 封装)](../05_Object_model/02_Classes_encapsulation.md) \| [下一节 (6.2 自定义迭代)](02_Customizing_iteration.md)

# 6.1 迭代协议

本节将探究迭代的底层过程。

### 迭代无处不在

许多对象都支持迭代：

```python
a = 'hello'
for c in a: # Loop over characters in a
    ...

b = { 'name': 'Dave', 'password':'foo'}
for k in b: # Loop over keys in dictionary
    ...

c = [1,2,3,4]
for i in c: # Loop over items in a list/tuple
    ...

f = open('foo.txt')
for x in f: # Loop over lines in a file
    ...
```

### 迭代：协议

考虑以下 `for` 语句：

```python
for x in obj:
    # statements
```

 `for` 语句的背后发生了什么?

```python
_iter = obj.__iter__()        # Get iterator object
while True:
    try:
        x = _iter.__next__()  # Get next item
        # statements ...
    except StopIteration:     # No more items
        break
```

所有可应用于  `for-loop` 的对象都实现了上述底层迭代协议。

示例：手动迭代一个列表。

```python
>>> x = [1,2,3]
>>> it = x.__iter__()
>>> it
<listiterator object at 0x590b0>
>>> it.__next__()
1
>>> it.__next__()
2
>>> it.__next__()
3
>>> it.__next__()
Traceback (most recent call last):
File "<stdin>", line 1, in ? StopIteration
>>>
```

### 支持迭代

如果想要将迭代添加到自己的对象中，那么了解迭代非常有用。例如：自定义容器。

```python
class Portfolio:
    def __init__(self):
        self.holdings = []

    def __iter__(self):
        return self.holdings.__iter__()
    ...

port = Portfolio()
for s in port:
    ...
```

## 练习

### 练习 6.1：迭代演示

创建以下列表：

```python
a = [1,9,4,25,16]
```

请手动迭代该列表：先调用 `__iter__()` 方法获取一个迭代器，然后调用  `__next__()`  方法获取下一个元素。

```python
>>> i = a.__iter__()
>>> i
<listiterator object at 0x64c10>
>>> i.__next__()
1
>>> i.__next__()
9
>>> i.__next__()
4
>>> i.__next__()
25
>>> i.__next__()
16
>>> i.__next__()
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
StopIteration
>>>
```

内置函数 `next()` 是调用迭代器的 `__next__()` 方法的快捷方式。尝试在一个文件对象上使用 `next()` 方法：

```python
>>> f = open('Data/portfolio.csv')
>>> f.__iter__()    # Note: This returns the file itself
<_io.TextIOWrapper name='Data/portfolio.csv' mode='r' encoding='UTF-8'>
>>> next(f)
'name,shares,price\n'
>>> next(f)
'"AA",100,32.20\n'
>>> next(f)
'"IBM",50,91.10\n'
>>>
```

持续调用 `next(f)`，直到文件末尾。观察会发生什么。

### 练习 6.2：支持迭代

有时候，你可能想要使自己的类对象支持迭代——尤其是你的类对象封装了已有的列表或者其它可迭代对象时。请在新的 `portfolio.py` 文件中定义如下类：

```python
# portfolio.py

class Portfolio:

    def __init__(self, holdings):
        self._holdings = holdings

    @property
    def total_cost(self):
        return sum([s.cost for s in self._holdings])

    def tabulate_shares(self):
        from collections import Counter
        total_shares = Counter()
        for s in self._holdings:
            total_shares[s.name] += s.shares
        return total_shares
```

Portfolio 类封装了一个列表，同时拥有一些方法，如： `total_cost` property。请修改 `report.py` 文件中的 `read_portfolio()` 函数，以便 `read_portfolio()` 函数能够像下面这样创建 `Portfolio` 类的实例：

```
# report.py
...

import fileparse
from stock import Stock
from portfolio import Portfolio

def read_portfolio(filename):
    '''
    Read a stock portfolio file into a list of dictionaries with keys
    name, shares, and price.
    '''
    with open(filename) as file:
        portdicts = fileparse.parse_csv(file,
                                        select=['name','shares','price'],
                                        types=[str,int,float])

    portfolio = [ Stock(d['name'], d['shares'], d['price']) for d in portdicts ]
    return Portfolio(portfolio)
...
```

接着运行 `report.py` 程序。你会发现程序运行失败，原因很明显，因为 `Portfolio` 的实例不是可迭代对象。

```python
>>> import report
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
... crashes ...
```

可以通过修改 `Portfolio` 类，使 `Portfolio` 类支持迭代来解决此问题：

```python
class Portfolio:

    def __init__(self, holdings):
        self._holdings = holdings

    def __iter__(self):
        return self._holdings.__iter__()

    @property
    def total_cost(self):
        return sum([s.shares*s.price for s in self._holdings])

    def tabulate_shares(self):
        from collections import Counter
        total_shares = Counter()
        for s in self._holdings:
            total_shares[s.name] += s.shares
        return total_shares
```

修改完成后， `report.py` 程序应该能够再次正常运行。同时，请修改 `pcost.py` 程序，以便能够像下面这样使用新的 `Portfolio` 对象：

```python
# pcost.py

import report

def portfolio_cost(filename):
    '''
    Computes the total cost (shares*price) of a portfolio file
    '''
    portfolio = report.read_portfolio(filename)
    return portfolio.total_cost
...
```

对 `pcost.py` 程序进行测试并确保其能正常工作：

```python
>>> import pcost
>>> pcost.portfolio_cost('Data/portfolio.csv')
44671.15
>>>
```

### 练习 6.3：创建一个更合适的容器

通常，我们创建一个容器类时，不仅希望该类能够迭代，同时也希望该类能够具有一些其它用途。请修改 `Portfolio` 类，使其具有以下这些特殊方法：

```python
class Portfolio:
    def __init__(self, holdings):
        self._holdings = holdings

    def __iter__(self):
        return self._holdings.__iter__()

    def __len__(self):
        return len(self._holdings)

    def __getitem__(self, index):
        return self._holdings[index]

    def __contains__(self, name):
        return any([s.name == name for s in self._holdings])

    @property
    def total_cost(self):
        return sum([s.shares*s.price for s in self._holdings])

    def tabulate_shares(self):
        from collections import Counter
        total_shares = Counter()
        for s in self._holdings:
            total_shares[s.name] += s.shares
        return total_shares
```

现在，使用 `Portfolio` 类进行一些实验：

```
>>> import report
>>> portfolio = report.read_portfolio('Data/portfolio.csv')
>>> len(portfolio)
7
>>> portfolio[0]
Stock('AA', 100, 32.2)
>>> portfolio[1]
Stock('IBM', 50, 91.1)
>>> portfolio[0:3]
[Stock('AA', 100, 32.2), Stock('IBM', 50, 91.1), Stock('CAT', 150, 83.44)]
>>> 'IBM' in portfolio
True
>>> 'AAPL' in portfolio
False
>>>
```

有关上述代码的一个重要发现——通常，如果一段代码和 Python 的其它代码"类似（speaks the common vocabulary of how other parts of Python normally work）"，那么该代码被认为是 “Pythonic” 的。同理，对于容器对象，其重要组成部分应该包括：支持迭代、可以进行索引、对所包含的元素进行判断，以及其它操作等等。

[目录](../Contents.md) \| [上一节 (5.2 封装)](../05_Object_model/02_Classes_encapsulation.md) \| [下一节 (6.2 自定义迭代)](02_Customizing_iteration.md)

