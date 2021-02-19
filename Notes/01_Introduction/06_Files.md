[目录](../Contents.md) \| [上一节(1.5 列表)](05_Lists.md) \| [下一节 (1.7 函数)](07_Functions.md)

# 1.6 文件管理

大多数的程序需要从某处读取输入。本节讨论文件访问。

### 文件输入和输出

打开一个文件：

```python
f = open('foo.txt', 'rt')     # Open for reading (text)
g = open('bar.txt', 'wt')     # Open for writing (text)
```

读取所有的数据：

```python
data = f.read()

# Read only up to 'maxbytes' bytes
data = f.read([maxbytes])
```

写入一些文本：

```python
g.write('some text')
```

当你完成操作后，关闭文件：

```python
f.close()
g.close()
```

文件应该被正确地关闭，这是很容易忘记的一个步骤。因此，首选方法是像下面这样使用 `with` 语句：

```python
with open(filename, 'rt') as file:
    # Use the file `file`
    ...
    # No need to close explicitly
...statements
```

当控制流离开缩进的代码块时，这将会自动关闭文件。

### 读取文件数据的习惯用法

以字符串的形式一次性读取整个文件：

```python
with open('foo.txt', 'rt') as file:
    data = file.read()
    # `data` is a string with all the text in `foo.txt`
```

通过迭代逐行读取文件：

```python
with open(filename, 'rt') as file:
    for line in file:
        # Process the line
```

### 写入文件的习惯用法

写入字符串数据：

```python
with open('outfile', 'wt') as out:
    out.write('Hello World\n')
    ...
```

重定向 print() 函数：

```python
with open('outfile', 'wt') as out:
    print('Hello World', file=out)
    ...
```

## 练习

本练习依赖于 `Data/portfolio.csv` 文件。该文件由一行一行的关于股票投资组合的信息构成。假定你是在  `practical-python/Work/` 目录下进行操作。如果你不确定自己所在的目录，你可以通过执行以下操作找出 Python 运行的目录：

```python
>>> import os
>>> os.getcwd()
'/Users/beazley/Desktop/practical-python/Work' # Output vary
>>>
```

### 练习 1.26: 文件预览

首先，尝试以字符串的形式一次性读取整个文件：

```python
>>> with open('Data/portfolio.csv', 'rt') as f:
        data = f.read()

>>> data
'name,shares,price\n"AA",100,32.20\n"IBM",50,91.10\n"CAT",150,83.44\n"MSFT",200,51.23\n"GE",95,40.37\n"MSFT",50,65.10\n"IBM",100,70.44\n'
>>> print(data)
name,shares,price
"AA",100,32.20
"IBM",50,91.10
"CAT",150,83.44
"MSFT",200,51.23
"GE",95,40.37
"MSFT",50,65.10
"IBM",100,70.44
>>>
```

在上面的示例中，应注意 Python 有两种输出模式。在第一种模式下，你在提示符后输入 `data`，Python 向你展示包含引号和转义码的原始字符串。当你输入 `print(data)`时，你获得真正的字符串的格式化输出。

一次性读取文件很简单，但这通常不是最恰当的读取文件方式——尤其是当文件碰巧很大或者文件包含要一次性处理的文本行时。

要逐行读取文件，可以像下面这样使用 for 循环：

```python
>>> with open('Data/portfolio.csv', 'rt') as f:
        for line in f:
            print(line, end='')

name,shares,price
"AA",100,32.20
"IBM",50,91.10
...
>>>
```

当你像上面展示的那样使用此代码时，它会读取文件的每一行，直到到达文件末尾。在文件末尾，循环会停止。

在某些特定的情况下，你可能想要手动的读取或者跳过某一行文本（例如，可能你想跳过列标题的第一行）：

```python
>>> f = open('Data/portfolio.csv', 'rt')
>>> headers = next(f)
>>> headers
'name,shares,price\n'
>>> for line in f:
    print(line, end='')

"AA",100,32.20
"IBM",50,91.10
...
>>> f.close()
>>>
```

`next()` 函数返回文件中的下一行文本。如果你反复调用该函数，将会获得连续的行。但是，如你所知，`for` 循环已经使用 `next()` 函数获取数据了。所以，通常情况下，除非你像上面展示的那样试图显式跳过或者读取一行，否则，不要直接调用 `next()` 函数。

一旦读取文件的各行，你就可以开始执行更多的操作，例如拆分。

示例，尝试以下操作：

```python
>>> f = open('Data/portfolio.csv', 'rt')
>>> headers = next(f).split(',')
>>> headers
['name', 'shares', 'price\n']
>>> for line in f:
    row = line.split(',')
    print(row)

['"AA"', '100', '32.20\n']
['"IBM"', '50', '91.10\n']
...
>>> f.close()
```

注意：在这些示例中，因为没有使用 `with` 语句，所以 `f.close()`  被显式的调用。

### 练习 1.27: 读取数据文件

现在你已经知道如何读取文件，让我们编写一个程序来执行简单的计算。

`portfolio.csv` 文件里面的列对应股票的名称，股票的数量以及单支股票持有的购买价格。编写一个名为 `pcost.py`  的程序来打开这个文件，读取所有的行并且计算在所有的股票投资组合中花费多少钱来购买这些股份。

提示：要将字符串转为整数，使用 `int()` 函数。要将字符串转为浮点数，使用 `float()` 函数。

你的程序应该打印如下输出：

```bash
Total cost 44671.15
```

### 练习 1.28: 其它类型的“文件"

如果你想读取非文本文件（如 gzip 压缩的数据文件）怎么办？虽然内建 `open()` 函数在这里帮不了你了，但是 Python 提供的 `gzip` 模块可以读取 gzip 文件。

试试看：

```python
>>> import gzip
>>> with gzip.open('Data/portfolio.csv.gz', 'rt') as f:
    for line in f:
        print(line, end='')

... look at the output ...
>>>
```

注意：此处包含 `'rt'` 文件模式至关重要。如果你忘记使用它，你将会得到字节字符串而不是通常的文本字符串。

### 说明:  我们不应该为此使用 Pandas 吗?

数据科学家很快指出，诸如 [Pandas](https://pandas.pydata.org) 这样的库已经具有读取 CSV 文件的函数，为什么不使用 Pandas 呢?的确如此——而且效果也很好。但是，这不是一门有关学习 Pandas 的课程，读取文件是一个比读取 CSV 文件更普遍的问题。我们使用 CSV 文件做例子的主要原因是：它是大多数编码人员熟悉的格式，并且相对易于直接使用——在读取 CSV 文件的过程中，阐明了许多 Python 特性。所以，当你回到工作中，请务必使用 Pandas。但是，在本课程剩下部分，我们将会继续使用标准的 Python 函数。

[目录](../Contents.md) \| [上一节(1.5 列表)](05_Lists.md) \| [下一节 (1.7 函数)](07_Functions.md)