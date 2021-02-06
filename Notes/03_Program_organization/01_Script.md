[目录](../Contents.md) \| [上一节 (2.7 对象模型)](../02_Working_with_data/07_Objects.md) \| [下一节 (3.2 深入函数)](02_More_functions.md)

# 3.1 脚本

在该部分，我们将深入研究编写 Python 脚本的惯例。

### 什么是脚本？

脚本就是运行和终止一系列语句的程序。

```python
# program.py

statement1
statement2
statement3
...
```

到目前为止，我们主要在编写脚本。

### 问题

如果您编写一个有用的脚本，它的特性和功能将会增加。您可能想要将其应用于相关的问题。随着时间的推移，它可能会成为一个关键的应用程序。如果您不注意的话，它可能会变成一团乱麻。因此，让我们有条理的组织程序吧。

### 定义变量

名称必须在使用之前定义。

```python
def square(x):
    return x*x

a = 42
b = a + 2     # Requires that `a` is defined

z = square(b) # Requires `square` and `b` to be defined
```

**顺序很重要。**

几乎总是把变量和函数的定义放到顶部附近。

### 定义函数

把所有与单个*任务*相关的代码都放到一个地方是个好主意。可以使用函数实现：

```python
def read_prices(filename):
    prices = {}
    with open(filename) as f:
        f_csv = csv.reader(f)
        for row in f_csv:
            prices[row[0]] = float(row[1])
    return prices
```

函数也可以简化重复的操作。

```python
oldprices = read_prices('oldprices.csv')
newprices = read_prices('newprices.csv')
```

### 什么是函数？

函数是命名的语句序列。

```python
def funcname(args):
  statement
  statement
  ...
  return result
```

*任何*  Python 语句都可以在函数内部使用。

```python
def foo():
    import math
    print(math.sqrt(2))
    help(math)
```

Python 中没有*特殊*语句（这使它很容易记住）。

### 函数定义

可以按任何顺序*定义*函数。

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

在程序执行期间，函数必须在实际使用之前（调用）定义。

```python
foo(3)        # foo must be defined already
```

在文体上，函数以自底向上的方式定义可能更常见。

### 自底向上的风格

函数被当做构建块。较小/较简单的块优先。

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

后面的函数基于前面的函数构建。再次说明，这仅仅是一种风格问题。在上面程序中唯一重要的事情是 `spam(42)`  的调用是在最后一步。

### 函数设计

理想情况下，函数应该是一个*黑盒*。它们应该仅对输入进行操作，并避免全局变量和奇怪的副作用。首要目标：模块化和可预测性。

### 文档字符串

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

一个好的文档字符串实践是写一句简短的话总结该函数做什么。如果需要更多的信息，请包含一个简短的带有更详细的参数说明的使用示例，

### 类型注解

也可以添加可选的类型提示到函数定义中。

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

提示在操作上什么也不做。它们纯粹是信息性的。但是，集成开发工具，代码检查器，以及其它工具可能会使用它来执行更多的操作。

## 练习

在第 2 节中，编写了一个名为 `report.py `的程序，该程序可以打印出显示股票投资组合绩效的报告。此程序包含一些函数。例如：

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

但是，程序的有些部分仅执行一系列的脚本计算。这些代码出现在程序结尾处。例如：

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

在本练习中，我们使用函数来对该程序进行有条理的组织，使程序更健壮。

### 练习 3.1：将程序构造为函数的集合

请修改  `report.py`  程序，以便所有主要操作（包括计算和输出）都由一组函数执行。特别地：

* 创建打印报告的函数 `print_report(report)`。
* 修改程序的最后一部分，使其仅是一系列函数调用，而无需进行其它运算。

### 练习 3.2：为程序执行创建一个顶层函数

把程序的最后一部分打包到单个函数 `portfolio_report(portfolio_filename, prices_filename)` 中。让程序运行，以便下面的函数调用像之前一样创建报告。

```python
portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
```

在最终版本中，程序只不过是一系列函数定义，最后是对单个函数`portfolio_report()` 的调用（它执行程序中涉及的所有步骤）。

通过将程序转换为单个函数，在不同的输入后可以很轻松地运行它。例如，在运行程序后以交互方式尝试这些语句：

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

### 说明

Python 使在有一系列语句的文件中编写相对无结构的脚本变得很轻松。总体来说，无论何时，尽可能地利用函数通常总是更好的选择。在某些时候，脚本会不断增加，并且我们希望它更有组织。另外，一个鲜为人知的事实是，如果使用函数，Python 的运行会更快一些。

[目录](../Contents.md) \| [上一节 (2.7 对象模型)](../02_Working_with_data/07_Objects.md) \| [下一节 (3.2 深入函数)](02_More_functions.md)