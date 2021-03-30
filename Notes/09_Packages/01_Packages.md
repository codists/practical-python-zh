[目录](../Contents.md) \| [上一节 (8.3 调试)](../08_Testing_debugging/03_Debugging.md) \| [下一节 (9.2 第三方包)](02_Third_party.md)

# 9.1 包

如果编写一个较大的程序，我们并不真的想在顶层将其组织为一个个独立文件的大型集合。本节对包（package）进行介绍。

### 模块

任何一个 Python 源文件称为一个模块（module）。

```python
# foo.py
def grok(a):
    ...
def spam(b):
    ...
```

一条 `import` 语句加载并*执行* 一个模块。

```python
# program.py
import foo

a = foo.grok(2)
b = foo.spam('Hello')
...
```

### 包 vs 模块

对于较大的代码集合，通常将模块组织到包中。

```code
# From this
pcost.py
report.py
fileparse.py

# To this
porty/
    __init__.py
    pcost.py
    report.py
    fileparse.py
```

首先，选择一个名字并用该名字创建顶级目录。如上述的 `porty` （显然，第一步最重要的是选择名字）。

接着，添加 `__init__.py` 文件到该目录中。`__init__.py` 文件可以是一个空文件。

最后，把源文件放到该目录中。

### 使用包

包用作导入的命名空间。

这意味着现在有了多级导入。

```python
import porty.report
port = porty.report.read_portfolio('port.csv')
```

导入语句还有其它变体：

```python
from porty import report
port = report.read_portfolio('portfolio.csv')

from porty.report import read_portfolio
port = read_portfolio('portfolio.csv')
```

### 两个问题

这种方法存在两个主要的问题：

* 同一包内不同文件之间的导入无效。
* 包中的主脚本无效。

因此，基本上一切导入都是无效的，但是，除此之外，程序还是可以工作的。

### 问题：导入

现在，在导入的时候，同一包内的不同文件之间的导入必须包含包名。请记住这个结构：

```code
porty/
    __init__.py
    pcost.py
    report.py
    fileparse.py
```

根据上述规则（同一包内的不同文件之间的导入必须包含包名）修改后的导入示例：

```python
# report.py
from porty import fileparse

def read_portfolio(filename):
    return fileparse.parse_csv(...)
```

所有的导入都是绝对的，而不是相对的。

```python
# report.py
import fileparse    # BREAKS. fileparse not found

...
```

### 相对导入

除了使用包名直接导入，还可以使用使用 `.` 引用当前的包。

```python
# report.py
from . import fileparse

def read_portfolio(filename):
    return fileparse.parse_csv(...)
```

语法:

```python
from . import modname
```

使用上述语法使得重命名包变得容易。

### 问题：主脚本

将包内的子模块作为主脚本运行是会导致程序中断：

```bash
bash $ python porty/pcost.py # BREAKS
...
```

*原因：你正在运行单个脚本，而 Python 不知道包的其余部分（`sys.path` 是错误的）。*

所有的导入都会中断。要想解决这个问题，需要以不同的方式运行程序，可以使用 `-m` 选项。

```bash
bash $ python -m porty.pcost # WORKS
...
```

### `__init__.py` 文件

该文件的主要目的是将模块组织在一起。

例如：

```python
# porty/__init__.py
from .pcost import portfolio_cost
from .report import portfolio_report
```

这使得导入的时候名字出现在顶层。

```python
from porty import portfolio_cost
portfolio_cost('portfolio.csv')
```

而不是使用多级导入：

```python
from porty import pcost
pcost.portfolio_cost('portfolio.csv')
```

### 脚本的另一种解决方案

如前所述，需要使用 `-m package.module` 运行包内的脚本。

```bash
bash % python3 -m porty.pcost portfolio.csv
```

还有另一种选择：编写一个新的顶级脚本。

```python
#!/usr/bin/env python3
# pcost.py
import porty.pcost
import sys
porty.pcost.main(sys.argv)
```

脚本位于包外面。目录结构如下：

```
pcost.py       # top-level-script
porty/         # package directory
    __init__.py
    pcost.py
    ...
```

### 应用结构

代码组织和文件结构是应用程序可维护性的关键。

对于 Python 而言，没有“放之四海而皆准”的方法，但是一个适用于多种问题的结构就是这样：

```code
porty-app/
  README.txt
  script.py         # SCRIPT
  porty/
    # LIBRARY CODE
    __init__.py
    pcost.py
    report.py
    fileparse.py
```

顶级 `porty-app` 目录是所有其他内容的容器——这些内容包括文档，顶级脚本，用例等。

同样，顶级脚本（如果有）需要放置在代码包之外（包的上一层）。

```python
#!/usr/bin/env python3
# porty-app/script.py
import sys
import porty

porty.report.main(sys.argv)
```

## 练习

此时，我们有了一个包含多个程序的目录：

```
pcost.py          # computes portfolio cost
report.py         # Makes a report
ticker.py         # Produce a real-time stock ticker
```

同时，还有许多具有各种功能的支持模块：

```
stock.py          # Stock class
portfolio.py      # Portfolio class
fileparse.py      # CSV parsing
tableformat.py    # Formatted tables
follow.py         # Follow a log file
typedproperty.py  # Typed class properties
```

在本次练习中，我们将整理这些代码并将它们放如一个通用包中。

### 练习 9.1：创建一个简单的包

请创建一个名为 `porty` 的目录并将上述所有的 Python 文件放如其中。另外，在 `porty` 目录中创建一个空的 `__init__.py` 文件。最后，文件目录看起来像这样：

```
porty/
    __init__.py
    fileparse.py
    follow.py
    pcost.py
    portfolio.py
    report.py
    stock.py
    tableformat.py
    ticker.py
    typedproperty.py
```

请将 `porty` 目录中的 `__pycache__` 目录移除。该目录包含了之前预编译的 Python 模块。我们想重新开始。

尝试导入包中的几个模块：

```python
>>> import porty.report
>>> import porty.pcost
>>> import porty.ticker
```

如果这些导入失败，请进入到合适的文件中解决模块导入问题，使其能够包括相对导入。例如，`import fileparse` 语句可以像下面这样进行修改：

```
# report.py
from . import fileparse
...
```

如果有类似于 `from fileparse import parse_csv` 这样的语句，请像下面这样修改代码：

```
# report.py
from .fileparse import parse_csv
...
```

### 练习 9.2：创建应用目录

对应用而言，将所有代码放到“包”中通常是不够的。有时，支持文件，文档，脚本等文件需要放到 `porty/`  目录之外。

请创建一个名为 `porty-app` 的新目录。然后将我们在练习 9.1 中创建的 `porty` 目录移动到 `porty-app` 目录中。接着，复制测试文件 `Data/portfolio.csv` 和 `Data/prices.csv` 到 `porty-app` 目录。另外，在 `porty-app` 目录下创建一个 `README.txt` 文件，该文件包含一些有关自己的信息。现在，代码的组织结构像下面这样：

```
porty-app/
    portfolio.csv
    prices.csv
    README.txt
    porty/
        __init__.py
        fileparse.py
        follow.py
        pcost.py
        portfolio.py
        report.py
        stock.py
        tableformat.py
        ticker.py
        typedproperty.py
```

要运行代码，需要确保你现在正在顶级目录 `porty-app/` 下。例如，从终端运行：

```python
shell % cd porty-app
shell % python3
>>> import porty.report
>>>
```

尝试将之前的脚本作为主程序运行：

```python
shell % cd porty-app
shell % python3 -m porty.report portfolio.csv prices.csv txt
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84

shell %
```

### 练习 9.3：顶级脚本

使用 `python -m` 命令通常有点怪异。可能需要编写一个顶级脚本来处理奇怪的包。请创建一个生成上述报告的脚本 `print-report.py`：

```python
#!/usr/bin/env python3
# print-report.py
import sys
from porty.report import main
main(sys.argv)
```

然后把脚本  `print-report.py `放到顶级目录 `porty-app/` 中。并确保可以在 `porty-app/` 目录下运行它：

```
shell % cd porty-app
shell % python3 print-report.py portfolio.csv prices.csv txt
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84

shell %
```

最后，代码的组织结构应该下面这样：

```
porty-app/
    portfolio.csv
    prices.csv
    print-report.py
    README.txt
    porty/
        __init__.py
        fileparse.py
        follow.py
        pcost.py
        portfolio.py
        report.py
        stock.py
        tableformat.py
        ticker.py
        typedproperty.py
```

[目录](../Contents.md) \| [上一节 (8.3 调试)](../08_Testing_debugging/03_Debugging.md) \| [下一节 (9.2 第三方包)](02_Third_party.md)