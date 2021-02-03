[目录](../Contents.md) \| [上一节 (2.7 对象模型)](../02_Working_with_data/07_Objects.md) \| [下一节 (3.2 深入函数)](02_More_functions.md)

# 3.1 脚本

In this part we look more closely at the practice of writing Python
scripts.

在该部分，我们将进一步编写 Python 脚本实践。

### What is a Script?什么是脚本？

A *script* is a program that runs a series of statements and stops.

脚本就是运行和终止一系列语句的程序。

```python
# program.py

statement1
statement2
statement3
...
```

We have mostly been writing scripts to this point.

对于这点，我们主要编写脚本。

### A Problem问题

If you write a useful script, it will grow in features and
functionality.  You may want to apply it to other related problems.
Over time, it might become a critical application.  And if you don't
take care, it might turn into a huge tangled mess.  So, let's get
organized.

如果您编写了一个有用的脚本，它将不断增加特性和功能。您可能想要把它应用于其它问题。一段时间后，它可能编程一个非常重要的应用。如果您不注意，它可能变为一团乱麻。所以，让我们有条理的组织程序吧。

### Defining Things定义变量

Names must always be defined before they get used later.

名称必须在使用之前定义。

```python
def square(x):
    return x*x

a = 42
b = a + 2     # Requires that `a` is defined

z = square(b) # Requires `square` and `b` to be defined
```

**The order is important.**
You almost always put the definitions of variables and functions near the top.

**顺序是重要的。**

您几乎总是把变量和函数的定义放到顶部附近。

### Defining Functions定义函数

It is a good idea to put all of the code related to a single *task* all in one place.
Use a function.

把与单个任务相关的所有代码放到一个地方是个非常不错的主意。可以使用函数实现：

```python
def read_prices(filename):
    prices = {}
    with open(filename) as f:
        f_csv = csv.reader(f)
        for row in f_csv:
            prices[row[0]] = float(row[1])
    return prices
```

A function also simplifies repeated operations.

函数也可以简化重复的操作。

```python
oldprices = read_prices('oldprices.csv')
newprices = read_prices('newprices.csv')
```

### What is a Function?什么是函数？

A function is a named sequence of statements.

函数是命名的语句序列。

```python
def funcname(args):
  statement
  statement
  ...
  return result
```

*Any* Python statement can be used inside.

*任何* Python 语句都可以在函数内部使用。

```python
def foo():
    import math
    print(math.sqrt(2))
    help(math)
```

There are no *special* statements in Python (which makes it easy to remember).

在 Python 中没有*特殊*语句（这使其很容易记住）。

### Function Definition函数定义

Functions can be *defined* in any order.

函数可以以任意顺序定义。

```python
def foo(x):
    bar(x)

def bar(x):
    statements

# OR
def bar(x):
    statements

def foo(x):
    bar(x)
```

Functions must only be defined prior to actually being *used* (or called) during program execution.

在程序执行期间，函数必须在实际使用之前（调用）定义。

```python
foo(3)        # foo must be defined already
```

Stylistically, it is probably more common to see functions defined in
a *bottom-up* fashion.

在文体上，函数以自底向上的样式定义很常见。

### Bottom-up Style自底向上风格

Functions are treated as building blocks.
The smaller/simpler blocks go first.

函数被当做构建块。更小的/更简单的块优先。

```python
# myprogram.py
def foo(x):
    ...

def bar(x):
    ...
    foo(x)          # Defined above
    ...

def spam(x):
    ...
    bar(x)          # Defined above
    ...

spam(42)            # Code that uses the functions appears at the end
```

Later functions build upon earlier functions.  Again, this is only
a point of style.  The only thing that matters in the above program
is that the call to `spam(42)` go last.

后面的函数基于前面的函数构建。再次说明，这仅仅是一种风格问题。在上面程序中唯一重要的事情是 `spam(42)`  的调用是在最后一步。

### Function Design函数设计

Ideally, functions should be a *black box*.
They should only operate on passed inputs and avoid global variables
and mysterious side-effects.  Your main goals: *Modularity* and *Predictability*.

理想情况下，函数应该是一个*黑盒*。它们应该在传递的输入上操作，并避免全局变量和奇怪的副作用。首要目标：模块化和可预测性。

### Doc Strings文档字符串

It's good practice to include documentation in the form of a
doc-string.  Doc-strings are strings written immediately after the
name of the function. They feed `help()`, IDEs and other tools.

以文档字符串（doc-string）的形式包含文档是良好的实践。文档字符串是紧接着函数名的字符串。它们用于 `help()` 函数，集成开发环境和其它的工具。

```python
def read_prices(filename):
    '''
    Read prices from a CSV file of name,price data
    '''
    prices = {}
    with open(filename) as f:
        f_csv = csv.reader(f)
        for row in f_csv:
            prices[row[0]] = float(row[1])
    return prices
```

A good practice for doc strings is to write a short one sentence
summary of what the function does.  If more information is needed,
include a short example of usage along with a more detailed
description of the arguments.

一个好的文档字符串实践是编写一句简短有关函数做什么的摘要。如果需要更多的信息，包含一个简短的带有更详细的参数描述的使用示例，

### Type Annotations类型注解

You can also add optional type hints to function definitions.

也可以增加可选的类型提示到函数定义中。

```python
def read_prices(filename: str) -> dict:
    '''
    Read prices from a CSV file of name,price data
    '''
    prices = {}
    with open(filename) as f:
        f_csv = csv.reader(f)
        for row in f_csv:
            prices[row[0]] = float(row[1])
    return prices
```

The hints do nothing operationally. They are purely informational.
However, they may be used by IDEs, code checkers, and other tools
to do more.

提示在操作上什么也不做。它们只是信息。但是，它们可能被集成开发环境，代码检查以及其它工具使用做更多 的事。

## 练习

In section 2, you wrote a program called `report.py` that printed out
a report showing the performance of a stock portfolio.  This program
consisted of some functions. For example:

在第二节中，

```python
# report.py
import csv

def read_portfolio(filename):
    '''
    Read a stock portfolio file into a list of dictionaries with keys
    name, shares, and price.
    '''
    portfolio = []
    with open(filename) as f:
        rows = csv.reader(f)
        headers = next(rows)

        for row in rows:
            record = dict(zip(headers, row))
            stock = {
                'name' : record['name'],
                'shares' : int(record['shares']),
                'price' : float(record['price'])
            }
            portfolio.append(stock)
    return portfolio
...
```

However, there were also portions of the program that just performed a
series of scripted calculations.  This code appeared near the end of
the program. For example:

```python
...

# Output the report

headers = ('Name', 'Shares', 'Price', 'Change')
print('%10s %10s %10s %10s'  % headers)
print(('-' * 10 + ' ') * len(headers))
for row in report:
    print('%10s %10d %10.2f %10.2f' % row)
...
```

In this exercise, we’re going take this program and organize it a
little more strongly around the use of functions.

### Exercise 3.1: Structuring a program as a collection of functions

Modify your `report.py` program so that all major operations,
including calculations and output, are carried out by a collection of
functions. Specifically:

* Create a function `print_report(report)` that prints out the report.
* Change the last part of the program so that it is nothing more than a series of function calls and no other computation.

### Exercise 3.2: Creating a top-level function for program execution

Take the last part of your program and package it into a single
function `portfolio_report(portfolio_filename, prices_filename)`.
Have the function work so that the following function call creates the
report as before:

```python
portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
```

In this final version, your program will be nothing more than a series
of function definitions followed by a single function call to
`portfolio_report()` at the very end (which executes all of the steps
involved in the program).

By turning your program into a single function, it becomes easy to run
it on different inputs.  For example, try these statements
interactively after running your program:

```python
>>> portfolio_report('Data/portfolio2.csv', 'Data/prices.csv')
... look at the output ...
>>> files = ['Data/portfolio.csv', 'Data/portfolio2.csv']
>>> for name in files:
        print(f'{name:-^43s}')
        portfolio_report(name, 'Data/prices.csv')
        print()

... look at the output ...
>>>
```

### Commentary

Python makes it very easy to write relatively unstructured scripting code
where you just have a file with a sequence of statements in it. In the
big picture, it's almost always better to utilize functions whenever
you can.  At some point, that script is going to grow and you'll wish
you had a bit more organization.  Also, a little known fact is that Python
runs a bit faster if you use functions.

[Contents](../Contents.md) \| [Previous (2.7 Object Model)](../02_Working_with_data/07_Objects.md) \| [Next (3.2 More on Functions)](02_More_functions.md)