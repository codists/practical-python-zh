[目录](../Contents.md) \| [上一节 (3.3 错误检查)](03_Error_checking.md) \| [下一节 (3.5 主模块)](05_Main_module.md)

# 3.4 模块

本节介绍模块的概念以及如何使用跨多个文件的函数。

### 模块和导入

任何一个 Python 源文件都是一个模块。

```python
# foo.py
def grok(a):
    ...
def spam(b):
    ...
```

`import` 语句加载并执行一个模块。

```python
# program.py
import foo

a = foo.grok(2)
b = foo.spam('Hello')
...
```

### 命名空间

模块是命名值的集合，有时也称为 *命名空间*。名称是源文件中定义的所有全局变量和函数。导入之后，模块名称用作前缀。因此，称为命名空间。

```python
import foo

a = foo.grok(2)
b = foo.spam('Hello')
...
```

模块名直接绑定到文件名（foo -> foo.py）。

### 全局定义

填充模块命名空间的内容是定义在*全局（global）*作用域中任何内容。考虑定义了相同变量 x 的两个模块。

```python
# foo.py
x = 42
def grok(a):
    ...
```

```python
# bar.py
x = 37
def spam(a):
    ...
```

在本例中，`x` 指向不同的变量。一个是 `foo.x`，另一个是 `bar.x`。不同的模块可以使用相同的名称并且这些名称不会相互冲突。

**模块是隔离的。**

### 把模块当做环境

对于所有定义在模块里面的代码而言，模块构成一个封闭的环境。

```python
# foo.py
x = 42

def grok(a):
    print(x)
```

*全局*变量始终绑定到封闭模块（相同文件），每个源文件都是它自己的小宇宙。

### 模块执行

导入模块时，模块中的所有语句依次*执行（execute）*，直到到达文件末尾。模块命名空间的内容是所有的*全局名称*，这些名称在执行过程结束时仍然被定义。如果有脚本语句在全局作用域中执行任务（如打印，创建文件等），您将看到它们在导入模块时运行。

### `import as` 语句

可以在导入模块时更改其名称：

```python
import math as m
def rectangular(r, theta):
    x = r * m.cos(theta)
    y = r * m.sin(theta)
    return x, y
```

它的作用与普通导入相同，仅仅是重命名模块而已。

### `from import`语句

`from import`语句从模块中选出符号并使它们在局部可访问。

```python
from math import sin, cos

def rectangular(r, theta):
    x = r * cos(theta)
    y = r * sin(theta)
    return x, y
```

这允许使用模块的某些部分，而不必输入模块前缀。对于经常使用的名称，这非常有用。

### 导入说明

有关导入的各种变化不改变模块的工作方式。

```python
import math
# vs
import math as m
# vs
from math import cos, sin
...
```

具体来说，`import` 始终执行整个文件并且模块仍然是隔离的环境。

`import module as` 语句只局部地更改名称。在后台，`from math import cos, sin` 语句仍加载全部的数学模块。当导入完成后，它仅仅将模块中的 `cos` 和 `sin` 名称复制到局部命名空间中。

### 模块加载

每个模块仅加载和执行一次。*注意：重复导入仅返回先前所加载模块的引用*

`sys.modules`  是所有已加载模块的字典。

```python
>>> import sys
>>> sys.modules.keys()
['copy_reg', '__main__', 'site', '__builtin__', 'encodings', 'encodings.encodings', 'posixpath', ...]
>>>
```

**注意**：当修改模块的源代码后，如果重复`import`语句会产生一个常见的困惑。由于模块缓存 `sys.modules`，重复导入总是返回之前加载的模块——即使更改已经发生。将修改后的代码加载到 Python 中最安全的方式是退出然后重启解释器。

### 定位模块

搜索模块时，Python 从路径列表（sys.path）中查询。

```python
>>> import sys
>>> sys.path
[
  '',
  '/usr/local/lib/python36/python36.zip',
  '/usr/local/lib/python36',
  ...
]
```

当前工作目录通常是第一个。

### 模块搜索路径

如前所述，`sys.path` 包含搜索路径。可以根据需要手动调整 。

```python
import sys
sys.path.append('/project/foo/pyfiles')
```

也可以通过环境变量添加搜索路径。

```python
% env PYTHONPATH=/project/foo/pyfiles python3
Python 3.6.0 (default, Feb 3 2017, 05:53:21)
[GCC 4.2.1 Compatible Apple LLVM 8.0.0 (clang-800.0.38)]
>>> import sys
>>> sys.path
['','/project/foo/pyfiles', ...]
```

在大部分情况下，没必要手动调整模块搜索路径。但是，如果尝试导入的 Python 代码位于特殊位置，或者无法从当前工作目录轻松访问，那么就需要手动调整搜索路径了。

## 练习

因为本练习涉及模块，所以确保在适当的环境中运行 Python 至关重要。模块经常给编程新手带来问题，这些问题与当前工作目录相关或者与 Python 路径设置相关。对于本课程，假定您是在 `Work/` 目录下编写所有的代码。为了获得最佳结果，应该确保也是在 `Work/`  目录下运行解释器。否则，需要确保 `practical-python/Work` 已添加到 `sys.path`。

### 练习 3.11：模块导入

在第 3 节中，我们创建了一个通用目标函数 `parse_csv()` 用于解析 CSV 数据文件的内容。

现在，我们来看看如何在其它程序中使用该函数。首先，启动一个新的 shell 窗口，进入到放置所有文件的目录中。我们将要导入它们。

启动 Python 交互模式。

```shell
bash % python3
Python 3.6.1 (v3.6.1:69c0db5050, Mar 21 2017, 01:21:04)
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

当Python 交互模式启动后，尝试导入某些之前编写的程序。应该能看到输出和以前一样。强调一下，导入模块会运行模块中的代码。

```python
>>> import bounce
... watch output ...
>>> import mortgage
... watch output ...
>>> import report
... watch output ...
>>>
```

如果没有代码运行，可能是因为在错误的目录下运行了 Python。现在，尝试导入 `fileparse` 模块并获取有关该模块的帮助。

```python
>>> import fileparse
>>> help(fileparse)
... look at the output ...
>>> dir(fileparse)
... look at the output ...
>>>
```

尝试使用 `fileparse` 模块来读取一些数据：

```python
>>> portfolio = fileparse.parse_csv('Data/portfolio.csv',select=['name','shares','price'], types=[str,int,float])
>>> portfolio
... look at the output ...
>>> pricelist = fileparse.parse_csv('Data/prices.csv',types=[str,float], has_headers=False)
>>> pricelist
... look at the output ...
>>> prices = dict(pricelist)
>>> prices
... look at the output ...
>>> prices['IBM']
106.11
>>>
```

尝试导入一个函数，以便不用再包含模块名：

```python
>>> from fileparse import parse_csv
>>> portfolio = parse_csv('Data/portfolio.csv', select=['name','shares','price'], types=[str,int,float])
>>> portfolio
... look at the output ...
>>>
```

### 练习 3.12：使用库模块

在第 2 节中，编写了 `report.py` 程序用来生成像下面这样的股票报告：

```
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

使用该程序并对其进行修改，以便使用 `fileparse` 模块中的函数完成所有输入文件的处理。为此，将 `fileparse` 作为模块导入，并修改  `read_portfolio()` 和 `read_prices()` 函数以便使用 `parse_csv()` 函数。

在本练习开始时，请使用交互示例作为指南。之后，应该能够获得与之前完全相同的输出。

### 练习 3.14：使用更多的的库导入

在第 1 节中，编写了一个读取股票投资组合和计算费用的程序 `pcost.py`。

```python
>>> import pcost
>>> pcost.portfolio_cost('Data/portfolio.csv')
44671.15
>>>
```

请修改 `pcost.py` 文件，以便它能够使用 `report.read_portfolio()` 函数。

### 说明

当完成练习后，您应该拥有三个程序。包含通用目的函数 `parse_csv()` 的`fileparse.py` 程序。用于生成报告，且包含`read_portfolio()` 和 `read_prices()` 函数的 `report.py` 程序。最后，利用 `report.py` 程序中编写的`read_portfolio()` 函数去计算股票投资组合费用的`pcost.py` 程序，

[目录](../Contents.md) \| [上一节 (3.3 错误检查)](03_Error_checking.md) \| [下一节 (3.5 主模块)](05_Main_module.md)
