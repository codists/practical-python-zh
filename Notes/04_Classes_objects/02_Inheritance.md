[目录](../Contents.md) \| [上一节 (4.1 类)](01_Class.md) \| [下一节 (4.3 特殊方法)](03_Special_methods.md)

# 4.2 继承

继承（inheritance）是编写可扩展程序程序的常用手段。本节对继承的思想（idea）进行探讨。

### 简介

继承用于特殊化现有对象：

```python
class Parent:
    ...

class Child(Parent):
    ...
```

新类 `Child` 称为派生类（derived class）或子类（subclass）。类 `Parent` 称为基类（base class）或超类（superclass）。在子类名后的括号 `()` 中指定基类（`Parent`），`class Child(Parent):`。

### 扩展

使用继承，你可以获取现有的类，并且可以：

* 添加新方法
* 重新定义现有方法
* 向实例添加新属性

最后，你**扩展了现有代码**。

### 示例

假设这是开始的类：

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

你可以通过继承更改 Stock 类的任何部分。

### 添加新方法

```python
class MyStock(Stock):
    def panic(self):
        self.sell(self.shares)
```

（译注：“panic” 在这里表示的是“panic selling”，恐慌性抛售）

使用示例：

```python
>>> s = MyStock('GOOG', 100, 490.1)
>>> s.sell(25)
>>> s.shares
75
>>> s.panic()
>>> s.shares
0
>>>
```

### 重新定义现有方法

```python
class MyStock(Stock):
    def cost(self):
        return 1.25 * self.shares * self.price
```

使用示例：

```python
>>> s = MyStock('GOOG', 100, 490.1)
>>> s.cost()
61262.5
>>>
```

新的 cost() 方法代替了旧的 cost() 方法。其它的方法不受影响。

## 方法覆盖

有时候，一个类既想扩展现有方法，同时又想在新的定义中使用原有的实现。为此，可以使用  `super()` 函数实现（译注：`方法覆盖` 有时也译为 `方法重写`）：

```python
class Stock:
    ...
    def cost(self):
        return self.shares * self.price
    ...

class MyStock(Stock):
    def cost(self):
        # Check the call to `super`
        actual_cost = super().cost()
        return 1.25 * actual_cost
```

使用内置函数 `super()` 调用之前的版本。

注意：在 Python 2 中，语法更加冗余，像下面这样：

```python
actual_cost = super(MyStock, self).cost()
```

### `__init__` 和继承

如果 `__init__` 方法在子类中被重新定义，那么有必要初始化父类。

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price

class MyStock(Stock):
    def __init__(self, name, shares, price, factor):
        # Check the call to `super` and `__init__`
        super().__init__(name, shares, price)
        self.factor = factor

    def cost(self):
        return self.factor * super().cost()
```

你需要使用 `super`  调用父类的 `__init__()` 方法，如前所示，这是调用先前版本的方法。

### 使用继承

有时候，继承用于组织相关的对象。

```python
class Shape:
    ...

class Circle(Shape):
    ...

class Rectangle(Shape):
    ...
```

要组织相关的对象，可以考虑使用逻辑层次结构或者进行分类。然而，一种更常见（更实用）的做法是创建可重用和可扩展的代码。例如，一个框架可能会定义一个基类，并指导你对其进行自定义。

```python
class CustomHandler(TCPHandler):
    def handle_request(self):
        ...
        # Custom processing
```

基类包含了通用代码。你的类继承基类并自定义特殊的部分。

### “is a” 关系

继承建立了一种类型关系。

```python
class Shape:
    ...

class Circle(Shape):
    ...
```

检查对象实例:

```python
>>> c = Circle(4.0)
>>> isinstance(c, Shape)
True
>>>
```

*重要提示：理想情况下，任何使用父类实例能正常工作的代码也能使用子类的实例正常工作。*

### `object` 基类

如果一个类没有父类，那么有时候你会看到它们使用  `object` 作为基类。

```python
class Shape(object):
    ...
```

在 Python 中，`object` 是所有对象的基类。

*注意：在技术上，它不是必需的，但是你通常会看到 `object` 在 Python 2 中被保留。如果省略，类仍然隐式继承自 `object`。* 

### 多重继承

你可以通过在类定义中指定多个基类来实现多重继承。

```python
class Mother:
    ...

class Father:
    ...

class Child(Mother, Father):
    ...
```

`Child` 类继承了两个父类（Mother，Father）的特性。这里有一些相当棘手的细节。除非你知道你正在做什么，否则不要这样做。虽然更多信息会在下一节给到，但是我们不会在本课程中进一步使用多重继承。

## 练习

继承的一个主要用途是：以各种方式编写可扩展和可定制的代码——尤其是在库或框架中。要说明这点，请考虑 `report.py` 程序中的 `print_report()` 函数。它看起来应该像下面这样：

```python
def print_report(reportdata):
    '''
    Print a nicely formated table from a list of (name, shares, price, change) tuples.
    '''
    headers = ('Name','Shares','Price','Change')
    print('%10s %10s %10s %10s' % headers)
    print(('-'*10 + ' ')*len(headers))
    for row in reportdata:
        print('%10s %10d %10.2f %10.2f' % row)
```

当运行 `report.py`  程序，你应该会获得像下面这样的输出：

```
>>> import report
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84
```

### 练习 4.5：扩展性问题

假设你想修改 `print_report()` 函数，以支持各种不同的输出格式，例如纯文本，HTML， CSV，或者 XML。为此，你可以尝试编写一个庞大的函数来实现每一个功能。但是，这样做可能会导致代码非常混乱，无法维护。这是一个使用继承的绝佳机会。

首先，请关注创建表所涉及的步骤。在表的顶部是标题。标题的后面是数据行。让我们使用这些步骤把它们放到各自的类中吧。创建一个名为 `tableformat.py` 的文件，并定义以下类：

```python
# tableformat.py

class TableFormatter:
    def headings(self, headers):
        '''
        Emit the table headings.
        '''
	raise NotImplementedError()

    def row(self, rowdata):
        '''
        Emit a single row of table data.
        '''
	raise NotImplementedError()
```

除了稍后用作定义其它类的设计规范，该类什么也不做。有时候，这样的类被称为“抽象基类”。

请修改 `print_report()`  函数，使其接受一个 `TableFormatter` 对象作为输入，并执行 `TableFormatter` 的方法来生成输出。示例：

```python
# report.py
...

def print_report(reportdata, formatter):
    '''
    Print a nicely formated table from a list of (name, shares, price, change) tuples.
    '''
    formatter.headings(['Name','Shares','Price','Change'])
    for name, shares, price, change in reportdata:
        rowdata = [ name, str(shares), f'{price:0.2f}', f'{change:0.2f}' ]
        formatter.row(rowdata)
```

因为你在 `portfolio_report()`  函数中增加了一个参数，所以你也需要修改  `portfolio_report()`  函数。请修改  `portfolio_report()`  函数，以便像下面这样创建 `TableFormatter`：

```python
# report.py

import tableformat

...
def portfolio_report(portfoliofile, pricefile):
    '''
    Make a stock report given portfolio and price data files.
    '''
    # Read data files
    portfolio = read_portfolio(portfoliofile)
    prices = read_prices(pricefile)

    # Create the report data
    report = make_report_data(portfolio, prices)

    # Print it out
    formatter = tableformat.TableFormatter()
    print_report(report, formatter)
```

运行新代码：

```python
>>> ================================ RESTART ================================
>>> import report
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
... crashes ...
```

程序应该会马上崩溃，并附带一个 `NotImplementedError` 异常。虽然这没有那么令人兴奋，但是结果确实是我们期待的。继续下一步部分。

### 练习 4.6：使用继承生成不同的输出

在 a 部分定义的 `TableFormatter` 类旨在通过继承进行扩展。实际上，这就是整个思想。要说明这点，请像下面这样定义 `TextTableFormatter` 类：

```python
# tableformat.py
...
class TextTableFormatter(TableFormatter):
    '''
    Emit a table in plain-text format
    '''
    def headings(self, headers):
        for h in headers:
            print(f'{h:>10s}', end=' ')
        print()
        print(('-'*10 + ' ')*len(headers))

    def row(self, rowdata):
        for d in rowdata:
            print(f'{d:>10s}', end=' ')
        print()
```

请像下面这样修改 `portfolio_report()` 函数：

```python
# report.py
...
def portfolio_report(portfoliofile, pricefile):
    '''
    Make a stock report given portfolio and price data files.
    '''
    # Read data files
    portfolio = read_portfolio(portfoliofile)
    prices = read_prices(pricefile)

    # Create the report data
    report = make_report_data(portfolio, prices)

    # Print it out
    formatter = tableformat.TextTableFormatter()
    print_report(report, formatter)
```

这应该会生成和之前一样的输出：

```python
>>> ================================ RESTART ================================
>>> import report
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84
>>>
```

但是，让我们更改输出为其它内容。定义一个以 CSV 格式生成输出的 `CSVTableFormatter`。

```python
# tableformat.py
...
class CSVTableFormatter(TableFormatter):
    '''
    Output portfolio data in CSV format.
    '''
    def headings(self, headers):
        print(','.join(headers))

    def row(self, rowdata):
        print(','.join(rowdata))
```

请像下面这样修改主程序：

```python
def portfolio_report(portfoliofile, pricefile):
    '''
    Make a stock report given portfolio and price data files.
    '''
    # Read data files
    portfolio = read_portfolio(portfoliofile)
    prices = read_prices(pricefile)

    # Create the report data
    report = make_report_data(portfolio, prices)

    # Print it out
    formatter = tableformat.CSVTableFormatter()
    print_report(report, formatter)
```

然后，你应该会看到像下面这样的 CSV 输出：

```python
>>> ================================ RESTART ================================
>>> import report
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
Name,Shares,Price,Change
AA,100,9.22,-22.98
IBM,50,106.28,15.18
CAT,150,35.46,-47.98
MSFT,200,20.89,-30.34
GE,95,13.48,-26.89
MSFT,50,20.89,-44.21
IBM,100,106.28,35.84
```

运用类似的思想，定义一个 `HTMLTableFormatter` 类，生成具有以下输出的表格：

```
<tr><th>Name</th><th>Shares</th><th>Price</th><th>Change</th></tr>
<tr><td>AA</td><td>100</td><td>9.22</td><td>-22.98</td></tr>
<tr><td>IBM</td><td>50</td><td>106.28</td><td>15.18</td></tr>
<tr><td>CAT</td><td>150</td><td>35.46</td><td>-47.98</td></tr>
<tr><td>MSFT</td><td>200</td><td>20.89</td><td>-30.34</td></tr>
<tr><td>GE</td><td>95</td><td>13.48</td><td>-26.89</td></tr>
<tr><td>MSFT</td><td>50</td><td>20.89</td><td>-44.21</td></tr>
<tr><td>IBM</td><td>100</td><td>106.28</td><td>35.84</td></tr>
```

请通过修改主程序来测试你的代码。 主程序创建的是 `HTMLTableFormatter` 对象，而不是 `CSVTableFormatter` 对象。

### 练习 4.7：多态

面向对象编程（oop）的一个主要特性是：可以将对象插入程序中，并且不必更改现有代码即可运行。例如，如果你编写了一个预期会使用 `TableFormatter`  对象的程序，那么不管你给它什么类型的 `TableFormatter` ，它都能正常工作。这样的行为有时被称为“多态”。

一个需要指出的潜在问题是：弄清楚如何让用户选择它们想要的格式。像 `TextTableFormatter` 一样直接使用类名通常有点烦人。因此，你应该考虑一些简化的方法。如：你可以在代码中嵌入 `if` 语句：

```python
def portfolio_report(portfoliofile, pricefile, fmt='txt'):
    '''
    Make a stock report given portfolio and price data files.
    '''
    # Read data files
    portfolio = read_portfolio(portfoliofile)
    prices = read_prices(pricefile)

    # Create the report data
    report = make_report_data(portfolio, prices)

    # Print it out
    if fmt == 'txt':
        formatter = tableformat.TextTableFormatter()
    elif fmt == 'csv':
        formatter = tableformat.CSVTableFormatter()
    elif fmt == 'html':
        formatter = tableformat.HTMLTableFormatter()
    else:
        raise RuntimeError(f'Unknown format {fmt}')
    print_report(report, formatter)
```

虽然在此代码中，用户可以指定一个简化的名称（如`'txt'` 或 `'csv'`）来选择格式，但是，像这样在 `portfolio_report()` 函数中使用大量的 `if` 语句真的是最好的思想吗？把这些代码移入其它通用函数中可能更好。

在 `tableformat.py`  文件中，请添加一个名为 `create_formatter(name)` 的函数，该函数允许用户创建给定输出名（如`'txt'`，`'csv'`，或 `'html'`）的格式器（formatter）。请像下面这样修改 `portfolio_report()` 函数：

```python
def portfolio_report(portfoliofile, pricefile, fmt='txt'):
    '''
    Make a stock report given portfolio and price data files.
    '''
    # Read data files
    portfolio = read_portfolio(portfoliofile)
    prices = read_prices(pricefile)

    # Create the report data
    report = make_report_data(portfolio, prices)

    # Print it out
    formatter = tableformat.create_formatter(fmt)
    print_report(report, formatter)
```

尝试使用不同的格式调用该函数，确保它能够正常工作。

### 练习 4.8：汇总

请修改 `report.py` 程序，以便 `portfolio_report()` 函数使用可选参数指定输出格式。示例：

```python
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv', 'txt')
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84
>>>
```

请修改主程序，以便可以在命令行上指定输出格式：

```bash
bash $ python3 report.py Data/portfolio.csv Data/prices.csv csv
Name,Shares,Price,Change
AA,100,9.22,-22.98
IBM,50,106.28,15.18
CAT,150,35.46,-47.98
MSFT,200,20.89,-30.34
GE,95,13.48,-26.89
MSFT,50,20.89,-44.21
IBM,100,106.28,35.84
bash $
```

### 讨论

在库和框架中，编写可扩展程序是继承的最常见用途之一。例如，框架指导你定义一个自己的对象，该对象继承自已提供的基类。然后你可以添加实现各种功能的函数。

另一个更深层次的概念是“拥有抽象的思想”。在练习中，我们定义了*自己的类*，用于格式化表格。你可能会看一下自己的代码，然后告诉自己“我应该只使用格式化库或其它人已经编写的东西！”。不，你应该同时使用自己的类和库。使用自己的类可以降低程序的耦合性，增加程序的灵活性。只要你的程序使用的应用接口来自于自己定义的类，那么，只要你想，你就可以更改程序的内部实现以使其按照你想的那样工作。你可以编写全定制（all-custom）代码，也可以使用第三方包（package）。当发现更好的包时，你可以将一个第三方包替换为另一个包。这并不重要——只要你保留这个接口，应用程序代码都不会中断。这是一种强大的思想，这也是为什么应该使用继承的原因之一。

也就是说，设计面向对象的程序可能会非常困难。想了解更多信息，你可能应该寻找一本有关设计模式主题的书看以下（尽管理解本练习中的内容已经让你以一种实用的方式在使用对象方面走得很远了）。

[目录](../Contents.md) \| [上一节 (4.1 类)](01_Class.md) \| [下一节 (4.3 特殊方法)](03_Special_methods.md)