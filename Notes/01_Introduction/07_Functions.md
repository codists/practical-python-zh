[目录](../Contents.md) \| [上一节 (1.6 文件)](06_Files.md) \| [下一节 (2.0 处理数据)](../02_Working_with_data/00_Overview.md)

# 1.7 函数

随着程序开始变大，我们会想要有条理地组织这些程序。本节简要介绍函数、库模块以及带有异常的错误处理。

### 自定义函数

对你要重用的代码使用函数。下面是函数的定义方式：

```python
def sumcount(n):
    '''
    Returns the sum of the first n integers
    '''
    total = 0
    while n > 0:
        total += n
        n -= 1
    return total
```

函数调用：

```python
a = sumcount(100)
```

函数是执行某些任务并返回结果的一系列语句。 `return` 关键字需要显式指定函数的返回值。

### 库函数

Python 带有一个大型的标准库。使用 `import` 访问库模块。示例：

```python
import math
x = math.sqrt(10)

import urllib.request
u = urllib.request.urlopen('http://www.python.org/')
data = u.read()
```

稍后，我们将更详细地介绍库和模块。

### 错误和异常

函数将错误报告为异常。异常会导致函数中止，如果未处理，可能会导致整个程序终止。

在你的 Python 解释器（REPL）中尝试一下：

```python
>>> int('N/A')
Traceback (most recent call last):
File "<stdin>", line 1, in <module>
ValueError: invalid literal for int() with base 10: 'N/A'
>>>
```

出于调试的目的，上面的错误信息描述了发生的情况，错误产生的位置以及回溯。该回溯显示导致失败的其它函数调用。

### 捕获和处理异常

异常可以被捕获并处理。要捕获异常，使用 `try - except` 语句：

```python
for line in f:
    fields = line.split()
    try:
        shares = int(fields[1])
    except ValueError:
        print("Couldn't parse", line)
    ...
```

该名称 `ValueError` 必须与你尝试捕获的错误类型匹配。

通常，根据所执行的操作，很难提前确切地知道可能会发生哪种错误。不管是好是坏，通常会添加在程序意外崩溃后的异常处理（示例：”天哪，我们忘记捕获错误了。我们应该处理错误的。“）。

### 触发异常

要触发异常，请使用 `raise` 语句：

```python
raise RuntimeError('What a kerfuffle')
```

这将导致程序因异常回溯而中止，除非该异常通过 `try-except` 代码块捕获。 

```bash
% python3 foo.py
Traceback (most recent call last):
  File "foo.py", line 21, in <module>
    raise RuntimeError("What a kerfuffle")
RuntimeError: What a kerfuffle
```

## 练习

### 练习 1.29：定义一个函数

尝试定义一个简单的函数：

```python
>>> def greeting(name):
        'Issues a greeting'
        print('Hello', name)

>>> greeting('Guido')
Hello Guido
>>> greeting('Paula')
Hello Paula
>>>
```

如果函数的第一条语句是字符串，那么它被当做文档字符串。尝试输入一个命令来显示该文档字符串，例如 `help(greeting)`。

### 练习 1.30：将脚本转换为函数

把你在 [练习1.27](06_Files.md) 为 `pcost.py` 程序编写的代码放到  `portfolio_cost(filename)` 函数里面。此函数以文件名作为输入，读取文件中的投资组合数据，把投资组合总的费用作为浮点数返回。

要使用你的函数，请修改程序，使其看起来像下面这样：

```python
def portfolio_cost(filename):
    ...
    # Your code here
    ...

cost = portfolio_cost('Data/portfolio.csv')
print('Total cost:', cost)
```

运行程序时，你应该会看到和以前一样的输出。运行程序后，你也可以输入一下命令来交互式地调用函数：

```bash
bash $ python3 -i pcost.py
```

这将允许你从交互模式调用函数：

```python
>>> portfolio_cost('Data/portfolio.csv')
44671.15
>>>
```

能够交互地试验代码对调试和测试非常有用。

### 练习 1.31：错误处理

如果你在缺少某些字段的文件上使用函数，会发生什么情况？

```python
>>> portfolio_cost('Data/missing.csv')
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
    File "pcost.py", line 11, in portfolio_cost
    nshares    = int(fields[1])
ValueError: invalid literal for int() with base 10: ''
>>>
```

在这一点上，你面临一个决定：要使程序正常工作，你可以通过消除错误行（bad lines）来清理原始输入文件，或者修改代码，以某种方式处理错误行。

请修改 `pcost.py` 程序以捕获异常，打印警告信息然后继续处理文件余下部分。

### 练习1.32：使用库函数

Python 带有一个拥有大量有用函数的大型标准库。`csv` 模块是一个在这里可能有用的库。无论何时，每当必须必须使用 CSV 数据文件时，都应使用 `csv` 模块。下面是一个有关 `csv` 模块是如何工作的示例：

```python
>>> import csv
>>> f = open('Data/portfolio.csv')
>>> rows = csv.reader(f)
>>> headers = next(rows)
>>> headers
['name', 'shares', 'price']
>>> for row in rows:
        print(row)

['AA', '100', '32.20']
['IBM', '50', '91.10']
['CAT', '150', '83.44']
['MSFT', '200', '51.23']
['GE', '95', '40.37']
['MSFT', '50', '65.10']
['IBM', '100', '70.44']
>>> f.close()
>>>
```
`csv` 模块有一个非常棒的功能——它处理各种底层细节，例如引号和适当的逗号拆分。在上面的输出中，你会注意到它从第一列的名称（names）中删除了双引号。

修改你的 `pcost.py` 程序，以使用 `csv` 模块进行解析，然后尝试运行前面的示例。

### 练习 1.33：从命令行读取

在 `pcost.py` 程序中，输入文件的名称已经被硬编码到代码中：

```python
# pcost.py

def portfolio_cost(filename):
    ...
    # Your code here
    ...

cost = portfolio_cost('Data/portfolio.csv')
print('Total cost:', cost)
```

虽然用于学习和测试还行，但在实际的程序中，你可能不会这么做。

相反，你可以把文件名作为参数传递给脚本。尝试按以下步骤修改程序的底部：

```python
# pcost.py
import sys

def portfolio_cost(filename):
    ...
    # Your code here
    ...

if len(sys.argv) == 2:
    filename = sys.argv[1]
else:
    filename = 'Data/portfolio.csv'

cost = portfolio_cost(filename)
print('Total cost:', cost)
```

`sys.argv` 是一个列表，该列表包含了在命令行上传递的参数（如果有）。

要运行程序，你需要从终端（terminal）运行 Python。

示例，从 Unix系统中的 bash 运行：

```bash
bash % python3 pcost.py Data/portfolio.csv
Total cost: 44671.15
bash %
```

[目录](../Contents.md) \| [上一节 (1.6 文件)](06_Files.md) \| [下一节 (2.0 处理数据)](../02_Working_with_data/00_Overview.md)