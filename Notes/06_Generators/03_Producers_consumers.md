[目录](../Contents.md) \| [上一节 (6.2 自定义迭代)](02_Customizing_iteration.md) \| [下一节 (6.4 生成器表达式)](04_More_generators.md)

# 6.3 生产者，消费者和管道

生成器在设置各种生产者/消费者问题（producer/consumer problems）和数据流管道（pipeline）中非常有用。本节将对此进行讨论。

### 生产者消费者问题

生成器与各种形式的 *生产者消费者* 问题密切相关。

```python
# Producer
def follow(f):
    ...
    while True:
        ...
        yield line        # Produces value in `line` below
        ...

# Consumer
for line in follow(f):    # Consumes value from `yield` above
    ...
```

`yield` 语句生成给  `for` 语句消费的值。

### 生成器管道

你可以使用生成器的这方面特性来设置进程管道（类似于 Unix 管道（pipe））。

*producer* &rarr; *processing* &rarr; *processing* &rarr; *consumer*

进程管道包括初始的数据生产者、中间的处理阶段、最后的消费者。

**producer** &rarr; *processing* &rarr; *processing* &rarr; *consumer*

```python
def producer():
    ...
    yield item
    ...
```

通常情况下，生产者是一个生成器，尽管也可以是其它的序列列表。`yield` 将数据输入管道。

*producer* &rarr; *processing* &rarr; *processing* &rarr; **consumer**

```python
def consumer(s):
    for item in s:
        ...
```

消费者是一个 for 循环，获取数据（译注：items）并对数据执行某些操作。

*producer* &rarr; **processing** &rarr; **processing** &rarr; *consumer*

```python
def processing(s):
    for item in s:
        ...
        yield newitem
        ...
```

中间的处理阶段同时消费和生产数据。它们可能修改数据流，也可能筛选数据流（丢弃数据）。

*producer* &rarr; *processing* &rarr; *processing* &rarr; *consumer*

```python
def producer():
    ...
    yield item          # yields the item that is received by the `processing`
    ...

def processing(s):
    for item in s:      # Comes from the `producer`
        ...
        yield newitem   # yields a new item
        ...

def consumer(s):
    for item in s:      # Comes from the `processing`
        ...
```

设置管道的代码如下：

```python
a = producer()
b = processing(a)
c = consumer(b)
```

你会发现数据逐渐地流向不同的函数。

## 练习

对于本练习，`stocksim.py` 程序仍需要在后台运行。并且，你将使用到上一节练习（译注：练习 6.7）编写的  `follow()`  函数。

### 练习 6.8：创建一个简单的管道

让我们来看看管道的思想。请创建下面这个函数：

```python
>>> def filematch(lines, substr):
        for line in lines:
            if substr in line:
                yield line

>>>
```

`filematch()`  函数除了不再打开文件，几乎与上一节练习的第一个生成器示例完全相同——仅仅对作为参数给出的行序列进行操作。现在，请尝试如下操作：

```
>>> from follow import follow
>>> lines = follow('Data/stocklog.csv')
>>> ibm = filematch(lines, 'IBM')
>>> for line in ibm:
        print(line)

... wait for output ...
```

虽然输出可能需要一定时间才会出现，但是，最后你一定会看到包含 IBM 数据的行。

### 练习 6.9：创建一个复杂的管道

通过执行更多操作来进一步理解管道的思想。

```
>>> from follow import follow
>>> import csv
>>> lines = follow('Data/stocklog.csv')
>>> rows = csv.reader(lines)
>>> for row in rows:
        print(row)

['BA', '98.35', '6/11/2007', '09:41.07', '0.16', '98.25', '98.35', '98.31', '158148']
['AA', '39.63', '6/11/2007', '09:41.07', '-0.03', '39.67', '39.63', '39.31', '270224']
['XOM', '82.45', '6/11/2007', '09:41.07', '-0.23', '82.68', '82.64', '82.41', '748062']
['PG', '62.95', '6/11/2007', '09:41.08', '-0.12', '62.80', '62.97', '62.61', '454327']
...
```

这非常有趣。你在这里可以看到， `follow()` 函数的输出被传递到 `csv.reader()`函数，并且，我们现在得到了一系列拆分的行。

### 练习 6.10：创建更多管道组件

让我们把这样的思想扩展到更大的管道中。首先，创建 `ticker.py` 文件，然后在 `ticker.py` 文件里面创建一个函数，像上面一样读取 CSV 文件：

```python
# ticker.py

from follow import follow
import csv

def parse_stock_data(lines):
    rows = csv.reader(lines)
    return rows

if __name__ == '__main__':
    lines = follow('Data/stocklog.csv')
    rows = parse_stock_data(lines)
    for row in rows:
        print(row)
```

接着，创建一个选择特定列的新函数：

```
# ticker.py
...
def select_columns(rows, indices):
    for row in rows:
        yield [row[index] for index in indices]
...
def parse_stock_data(lines):
    rows = csv.reader(lines)
    rows = select_columns(rows, [0, 1, 4])
    return rows
```

再次运行程序，你应该可以看到输出缩小如下：

```
['BA', '98.35', '0.16']
['AA', '39.63', '-0.03']
['XOM', '82.45','-0.23']
['PG', '62.95', '-0.12']
...
```

再接着，创建一个生成器函数以转换数据类型并构建字典。示例：

```python
# ticker.py
...

def convert_types(rows, types):
    for row in rows:
        yield [func(val) for func, val in zip(types, row)]

def make_dicts(rows, headers):
    for row in rows:
        yield dict(zip(headers, row))
...
def parse_stock_data(lines):
    rows = csv.reader(lines)
    rows = select_columns(rows, [0, 1, 4])
    rows = convert_types(rows, [str, float, float])
    rows = make_dicts(rows, ['name', 'price', 'change'])
    return rows
...
```

再次运行程序，你应该能够看到像下面这样的字典流：

```
{ 'name':'BA', 'price':98.35, 'change':0.16 }
{ 'name':'AA', 'price':39.63, 'change':-0.03 }
{ 'name':'XOM', 'price':82.45, 'change': -0.23 }
{ 'name':'PG', 'price':62.95, 'change':-0.12 }
...
```

### 练习 6.11：筛选数据

创建一个筛选数据的函数。示例：

```python
# ticker.py
...

def filter_symbols(rows, names):
    for row in rows:
        if row['name'] in names:
            yield row
```

使用该函数可以筛选出投资组合中的股票：

```python
import report
portfolio = report.read_portfolio('Data/portfolio.csv')
rows = parse_stock_data(follow('Data/stocklog.csv'))
rows = filter_symbols(rows, portfolio)
for row in rows:
    print(row)
```

### 练习 6.12：整合所有的代码

请在 `ticker.py` 文件中编写函数 `ticker(portfile, logfile, fmt)` ，该函数根据给定的投资组合、日志文件和表格格式创建实时的股票报价器。示例：

```python
>>> from ticker import ticker
>>> ticker('Data/portfolio.csv', 'Data/stocklog.csv', 'txt')
      Name      Price     Change
---------- ---------- ----------
        GE      37.14      -0.18
      MSFT      29.96      -0.09
       CAT      78.03      -0.49
        AA      39.34      -0.32
...

>>> ticker('Data/portfolio.csv', 'Data/stocklog.csv', 'csv')
Name,Price,Change
IBM,102.79,-0.28
CAT,78.04,-0.48
AA,39.35,-0.31
CAT,78.05,-0.47
...
```

### 讨论

心得体会：你可以创建各种生成器函数，并把它们链接在一起执行涉及数据流的管道处理。另外，你可以创建一个函数，把一系列的管道阶段打包到一个单独的函数中调用（例如 `parse_stock_data()` 函数）。

[目录](../Contents.md) \| [上一节 (6.2 自定义迭代)](02_Customizing_iteration.md) \| [下一节 (6.4 生成器表达式)](04_More_generators.md)