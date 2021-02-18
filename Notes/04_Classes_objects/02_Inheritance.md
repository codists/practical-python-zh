[目录](../Contents.md) \| [上一节 (4.1 类)](01_Class.md) \| [下一节 (4.3 特殊方法)](03_Special_methods.md)

# 4.2 继承

Inheritance is a commonly used tool for writing extensible programs.
This section explores that idea.

继承（inheritance）是编写可扩展程序程序的常用工具。

本节对继承的思想（idea）进行探讨。

### Introduction简介

Inheritance is used to specialize existing objects:

继承用于将已有对象进行特殊化。

```python
class Parent:
    ...

class Child(Parent):
    ...
```

The new class `Child` is called a derived class or subclass.  The
`Parent` class is known as base class or superclass.  `Parent` is
specified in `()` after the class name, `class Child(Parent):`.

新类 `Child` 称为派生类（derived class）或者子类（subclass）。类 `Parent` 被称为基类（base class）或者超类（superclass）。在子类名后的括号 `()` 中指定基类（`Parent`），`class Child(Parent):`。

### Extending扩展

With inheritance, you are taking an existing class and:

* Adding new methods
* Redefining some of the existing methods
* Adding new attributes to instances

In the end you are **extending existing code**.

使用继承，你可以获取已有的类并：

* 添加新方法
* 重新定义已有方法
* 向实例添加新属性

### Example示例

Suppose that this is your starting class:

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

You can change any part of this via inheritance.

你可以通过继承修改 Stock 类的任何部分。

### Add a new method添加新方法

```python
class MyStock(Stock):
    def panic(self):
        self.sell(self.shares)
```

（译注：“panic” 在这里表示的是“panic selling”，恐慌抛售）

Usage example.

使用示例。

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

### Redefining an existing method重新定义已有方法

```python
class MyStock(Stock):
    def cost(self):
        return 1.25 * self.shares * self.price
```

Usage example.

使用示例。

```python
>>> s = MyStock('GOOG', 100, 490.1)
>>> s.cost()
61262.5
>>>
```

The new method takes the place of the old one. The other methods are unaffected. It's tremendous.

新的 cost() 方法取代了旧的 cost() 方法。其它的方法不受影响。这非常棒。

## Overriding方法覆盖

Sometimes a class extends an existing method, but it wants to use the
original implementation inside the redefinition.  For this, use `super()`:

有时候，一个类既想扩展已有方法，同时又想在新的定义中使用原有的实现。对于这种情况，可以使用  `super()` 函数实现：

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

Use `super()` to call the previous version.

使用 `super()` 函数调用之前的版本。

*Caution: In Python 2, the syntax was more verbose.*

注意：在 Python 2 中，语法稍显冗余，像下面这样：

```python
actual_cost = super(MyStock, self).cost()
```

### `__init__` 和继承

If `__init__` is redefined, it is essential to initialize the parent.

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

You should call the `__init__()` method on the `super` which is the
way to call the previous version as shown previously.

你需要使用 `super`  调用 `__init__()` 方法，如前所示，这是调用先前版本的方法。

### Using Inheritance使用继承

Inheritance is sometimes used to organize related objects.

有时候，继承用于组织相关的对象。

```python
class Shape:
    ...

class Circle(Shape):
    ...

class Rectangle(Shape):
    ...
```

Think of a logical hierarchy or taxonomy.  However, a more common (and
practical) usage is related to making reusable or extensible code.
For example, a framework might define a base class and instruct you
to customize it.

考虑逻辑结构或者分类。更常见的用法是创建可重用，可扩展的代码。例如，一个框架定义了一个基类，并指导你对其进行自定义。

```python
class CustomHandler(TCPHandler):
    def handle_request(self):
        ...
        # Custom processing
```

The base class contains some general purpose code.
Your class inherits and customized specific parts.

基类包含通用目的代码，类继承和自定义特定的部分。

### "is a" relationship “is a” 关系

Inheritance establishes a type relationship.

继承建立了一种类型关系。

```python
class Shape:
    ...

class Circle(Shape):
    ...
```

Check for object instance.

检查对象实例。

```python
>>> c = Circle(4.0)
>>> isinstance(c, Shape)
True
>>>
```

*Important: Ideally, any code that worked with instances of the parent
class will also work with instances of the child class.*

*重要提示：理想情况下，任何使用父类实例能正常工作的代码也能使用子类的实例。*

### `object` 基类

If a class has no parent, you sometimes see `object` used as the base.

如果一个类没有父类，那么有时候你会看到它们使用  `object` 作为基类。

```python
class Shape(object):
    ...
```

`object` is the parent of all objects in Python.

在 Python 中，`object` 是所有对象的基类。

*Note: it's not technically required, but you often see it specified
as a hold-over from it's required use in Python 2. If omitted, the
class still implicitly inherits from `object`.

*注意：虽然这不是技术上的要求，但是你通常会看到它 `object` 在 Python2 中被保留。如果省略，类仍然隐式继承自 `object`。* 

### 多重继承

You can inherit from multiple classes by specifying them in the definition of the class.

你可以通过在类定义中指定多个基类来实现多重继承。

```python
class Mother:
    ...

class Father:
    ...

class Child(Mother, Father):
    ...
```

The class `Child` inherits features from both parents.  There are some
rather tricky details. Don't do it unless you know what you are doing.
Some further information will be given in the next section, but we're not
going to utilize multiple inheritance further in this course.

`Child` 类从两个父类（Mother，Father）中继承特性。这里有一些相当棘手的细节。除非你知道你正在做什么，否则不要这样做。虽然更多信息会在下一节给到，但是我们不会在本课程进一步使用多重继承。

## 练习

A major use of inheritance is in writing code that's meant to be
extended or customized in various ways--especially in libraries or
frameworks. To illustrate, consider the `print_report()` function
in your `report.py` program.  It should look something like this:

继承的一个主要用途是：以多种方式编写可扩展和可定制的代码——尤其是在库或者框架中。要说明这点，考虑 `report.py` 程序中的 `print_report()` 函数。它看起来应该像下面这样：

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

When you run your report program, you should be getting output like this:

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

### 练习 4.5：扩展性问题An Extensibility Problem

Suppose that you wanted to modify the `print_report()` function to
support a variety of different output formats such as plain-text,
HTML, CSV, or XML.  To do this, you could try to write one gigantic
function that did everything.  However, doing so would likely lead to
an unmaintainable mess.  Instead, this is a perfect opportunity to use
inheritance instead.

假设你想修改 `print_report()` 函数，以便支持各种不同的输出格式，例如纯文本，HTML， CSV，或者 XML。为此，你可以尝试编写一个庞大的函数来实现。但是，这样做可能会导致产生无法维护的代码。相反，这是一个使用继承的机会。

To start, focus on the steps that are involved in a creating a table.
At the top of the table is a set of table headers.  After that, rows
of table data appear.  Let's take those steps and put them into
their own class.  Create a file called `tableformat.py` and define the
following class:

首先，请关注表的创建步骤。在表的顶部是标题。标题的后面是数据。让我们使用这些步骤把它们放到它们自己的类中吧。创建一个名为 `tableformat.py` 的文件，并在文件里面定义下面这个类：

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

This class does nothing, but it serves as a kind of design specification for
additional classes that will be defined shortly.  A class like this is
sometimes called an "abstract base class."

除了用作定义其它类的设计规范，该类什么也不做。有时候，像这样的类称为“抽象基类”。

Modify the `print_report()` function so that it accepts a
`TableFormatter` object as input and invokes methods on it to produce
the output.  For example, like this:

请修改 `print_report()`  函数，以便它接受一个 `TableFormatter` 对象作为输入，并执行 `TableFormatter` 的方法来生成输出。例如：

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

Since you added an argument to print_report(), you're going to need to modify the
`portfolio_report()` function as well.  Change it so that it creates a `TableFormatter`
like this:

因为你在 print_report() 函数中添加了参数，所以你也需要修改  `portfolio_report()`  函数。请修改  `portfolio_report()`  函数，以便像下面这样创建 `TableFormatter`：

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

Run this new code:

运行新代码：

```python
>>> ================================ RESTART ================================
>>> import report
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
... crashes ...
```

It should immediately crash with a `NotImplementedError` exception.  That's not
too exciting, but it's exactly what we expected.  Continue to the next part.

程序应该会马上崩溃，并附带一个 `NotImplementedError`。虽然没有那么令人兴奋，但是结果确实是我们期待的。继续下一步部分。

### 练习 4.6：使用继承生成不同的输出 Using Inheritance to Produce Different Output

The `TableFormatter` class you defined in part (a) is meant to be
extended via inheritance.  In fact, that's the whole idea.  To
illustrate, define a class `TextTableFormatter` like this:

在 a 部分定义的 `TableFormatter` 类是通过继承来扩展的。事实上，这是整个思想。示例，像下面这样定义 `TextTableFormatter` 类：

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

Modify the `portfolio_report()` function like this and try it:

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

This should produce the same output as before:

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

However, let's change the output to something else.  Define a new
class `CSVTableFormatter` that produces output in CSV format:

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

Modify your main program as follows:

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

You should now see CSV output like this:

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

Using a similar idea, define a class `HTMLTableFormatter`
that produces a table with the following output:

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

Test your code by modifying the main program to create a
`HTMLTableFormatter` object instead of a
`CSVTableFormatter` object.

### Exercise 4.7: Polymorphism in Action

A major feature of object-oriented programming is that you can
plug an object into a program and it will work without having to
change any of the existing code.  For example, if you wrote a program
that expected to use a `TableFormatter` object, it would work no
matter what kind of `TableFormatter` you actually gave it.  This
behavior is sometimes referred to as "polymorphism."

One potential problem is figuring out how to allow a user to pick out
the formatter that they want.  Direct use of the class names such as
`TextTableFormatter` is often annoying.  Thus, you might consider some
simplified approach.  Perhaps you embed an `if-`statement into the
code like this:

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

In this code, the user specifies a simplified name such as `'txt'` or
`'csv'` to pick a format.  However, is putting a big `if`-statement in
the `portfolio_report()` function like that the best idea?  It might
be better to move that code to a general purpose function somewhere
else.

In the `tableformat.py` file, add a function `create_formatter(name)`
that allows a user to create a formatter given an output name such as
`'txt'`, `'csv'`, or `'html'`.  Modify `portfolio_report()` so that it
looks like this:

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

Try calling the function with different formats to make sure it's working.

### Exercise 4.8: Putting it all together

Modify the `report.py` program so that the `portfolio_report()` function takes
an optional argument specifying the output format. For example:

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

Modify the main program so that a format can be given on the command line:

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

### Discussion

Writing extensible code is one of the most common uses of inheritance
in libraries and frameworks.  For example, a framework might instruct
you to define your own object that inherits from a provided base
class.  You're then told to fill in various methods that implement
various bits of functionality.

Another somewhat deeper concept is the idea of "owning your
abstractions."  In the exercises, we defined *our own class* for
formatting a table.  You may look at your code and tell yourself "I should
just use a formatting library or something that someone else already
made instead!"  No, you should use BOTH your class and a library.
Using your own class promotes loose coupling and is more flexible.
As long as your application uses the programming interface of your class,
you can change the internal implementation to work in any way that you
want.  You can write all-custom code.  You can use someone's third
party package.  You swap out one third-party package for a different
package when you find a better one.  It doesn't matter--none of
your application code will break as long as you preserve keep the
interface.   That's a powerful idea and it's one of the reasons why
you might consider inheritance for something like this.

That said, designing object oriented programs can be extremely
difficult.  For more information, you should probably look for books
on the topic of design patterns (although understanding what happened
in this exercise will take you pretty far in terms of using objects in
a practically useful way).

[目录](../Contents.md) \| [上一节 (4.1 类)](01_Class.md) \| [下一节 (4.3 特殊方法)](03_Special_methods.md)