[目录](../Contents.md) \| [上一节 (2.1 数据类型)](01_Datatypes.md) \| [下一节 (2.3 格式化)](03_Formatting.md)

# 2.2 容器

本节讨论列表（list），字典（dict）和集合（set）。

### 概述

通常，程序必须处理许多对象。

* 股票的投资组合
* 股票价格表

这里有三种主要的选择（译注：数据结构）可以使用：

* 列表。有序的数据。
* 字典。无序的数据。
* 集合。互异且无序的数据。

### 把列表当作容器

当数据顺序很重要时，请使用列表。记住，列表可以存储任何类型的对象。例如，包含元组的列表：

```python
portfolio = [
    ('GOOG', 100, 490.1),
    ('IBM', 50, 91.3),
    ('CAT', 150, 83.44)
]

portfolio[0]            # ('GOOG', 100, 490.1)
portfolio[2]            # ('CAT', 150, 83.44)
```

### 列表构建

从零开始构建列表。

```python
records = []  # Initial empty list

# Use .append() to add more items
records.append(('GOOG', 100, 490.10))
records.append(('IBM', 50, 91.3))
...
```

从文件读取记录的示例：

```python
records = []  # Initial empty list

with open('Data/portfolio.csv', 'rt') as f:
    next(f) # Skip header
    for line in f:
        row = line.split(',')
        records.append((row[0], int(row[1]), float(row[2])))
```

### 把字典当作容器

如果要快速随机查找（通过键名），那么字典很有用。例如，股票价格字典：

```python
prices = {
   'GOOG': 513.25,
   'CAT': 87.22,
   'IBM': 93.37,
   'MSFT': 44.12
}
```

以下是一些简单的查找：

```python
>>> prices['IBM']
93.37
>>> prices['GOOG']
513.25
>>>
```

### 字典构建

从零开始构建字典的示例：

```python
prices = {} # Initial empty dict

# Insert new items
prices['GOOG'] = 513.25
prices['CAT'] = 87.22
prices['IBM'] = 93.37
```

从文件内容填充字典的示例：

```python
prices = {} # Initial empty dict

with open('Data/prices.csv', 'rt') as f:
    for line in f:
        row = line.split(',')
        prices[row[0]] = float(row[1])
```

注意：如果是在 `Data/prices.csv` 文件上尝试此操作，会发现几乎可以正常工作——但是，在末尾有一个空行导致程序崩溃了。需要找出一些方法来修改代码以解决此问题（参见练习 2.6）。

### 字典查找

测试键是否存在：

```python
if key in d:
    # YES
else:
    # NO
```

可以查找可能不存在的值，并在值不存在的情况下提供默认值。

```python
name = d.get(key, default)
```

示例：

```python
>>> prices.get('IBM', 0.0)
93.37
>>> prices.get('SCOX', 0.0)
0.0
>>>
```

### 组合键

在 Ｐython 中，几乎任何类型的值都可以用作字典的键。字典的键必须是不可变类型。例如，元组：

```python
holidays = {
  (1, 1) : 'New Years',
  (3, 14) : 'Pi day',
  (9, 13) : "Programmer's day",
}
```

然后访问：

```python
>>> holidays[3, 14]
'Pi day'
>>>
```

*列表，集合或者其它字典都不能用作字典的键，因为列表和字典（译注：集合也是使用哈希技术实现的）是可变的。*

### 集合

集合是互异且无序的数据。

```python
tech_stocks = { 'IBM','AAPL','MSFT' }
# Alternative syntax
tech_stocks = set(['IBM', 'AAPL', 'MSFT'])
```

集合对于成员关系测试很有用。

```python
>>> tech_stocks
set(['AAPL', 'IBM', 'MSFT'])
>>> 'IBM' in tech_stocks
True
>>> 'FB' in tech_stocks
False
>>>
```

集合对于消除重复也很有用。

```python
names = ['IBM', 'AAPL', 'GOOG', 'IBM', 'GOOG', 'YHOO']

unique = set(names)
# unique = set(['IBM', 'AAPL','GOOG','YHOO'])
```

其它集合操作：

```python
names.add('CAT')        # Add an item
names.remove('YHOO')    # Remove an item

s1 | s2                 # Set union
s1 & s2                 # Set intersection
s1 - s2                 # Set difference
```

## 练习

在这些练习中，你开始构建的程序是本课程剩余部分使用的主要程序之一。请在 `Work/report.py` 文件中工作。

### 练习 2.4：包含元组的列表

`Data/portfolio.csv`  文件包含投资组合中的股票列表。在 [练习 1.30](../01_Introduction/07_Functions.md) 中，你编写了一个读取该文件并执行简单计算的 `portfolio_cost(filename)` 函数。

代码看起来应该像下面这样：

```python
# pcost.py

import csv

def portfolio_cost(filename):
    '''Computes the total cost (shares*price) of a portfolio file'''
    total_cost = 0.0

    with open(filename, 'rt') as f:
        rows = csv.reader(f)
        headers = next(rows)
        for row in rows:
            nshares = int(row[1])
            price = float(row[2])
            total_cost += nshares * price
    return total_cost
```

请使用这些代码作为指导，创建一个新文件 `report.py` 。在 `report.py` 文件中，定义 `read_portfolio(filename)`  函数，该函数打开 `Data/portfolio.csv` 文件并将其读入到包含元组的列表中。为此，你需要对上面的代码做一些小修改。

首先，创建一个最初设为空列表的变量，而不是定义 `total_cost = 0`。例如：

```python
portfolio = []
```

接着，把每一行准确地存储到元组中（就像在上次的练习中做的那样），然后把元组追加到列表中，而不是合计总的费用。

```python
for row in rows:
    holding = (row[0], int(row[1]), float(row[2]))
    portfolio.append(holding)
```

最后，返回得到的`portfolio` 列表。

请交互式地试验函数（提醒，要执行此操作，首先需要在解释器运行 `report.py` 程序）。

*提示：当在终端执行文件的时候，请使用 `-i` 参数。*

```python
>>> portfolio = read_portfolio('Data/portfolio.csv')
>>> portfolio
[('AA', 100, 32.2), ('IBM', 50, 91.1), ('CAT', 150, 83.44), ('MSFT', 200, 51.23),
    ('GE', 95, 40.37), ('MSFT', 50, 65.1), ('IBM', 100, 70.44)]
>>>
>>> portfolio[0]
('AA', 100, 32.2)
>>> portfolio[1]
('IBM', 50, 91.1)
>>> portfolio[1][1]
50
>>> total = 0.0
>>> for s in portfolio:
        total += s[1] * s[2]

>>> print(total)
44671.15
>>>
```

创建的包含元组的列表非常类似于二维（2-Ｄ）数组。例如，使用诸如 `portfolio[row][column]` （ `row` 和`column` 是整数）的查找来访问特定的列和行。

也就是说，可以使用像下面这样的语句重写最后的 for 循环：

```python
>>> total = 0.0
>>> for name, shares, price in portfolio:
            total += shares*price

>>> print(total)
44671.15
>>>
```

### 练习 2.5：包含字典的列表

使用字典（而不是元组）修改在练习 2.4 中编写的函数来表示投资组合中的股票。在字典中，使用字段名 "name", "shares" 和 "price" 来表示输入文件中的不同列。

以与练习 2.4 中相同的方式试验这个新的函数。 

```python
>>> portfolio = read_portfolio('Data/portfolio.csv')
>>> portfolio
[{'name': 'AA', 'shares': 100, 'price': 32.2}, {'name': 'IBM', 'shares': 50, 'price': 91.1},
    {'name': 'CAT', 'shares': 150, 'price': 83.44}, {'name': 'MSFT', 'shares': 200, 'price': 51.23},
    {'name': 'GE', 'shares': 95, 'price': 40.37}, {'name': 'MSFT', 'shares': 50, 'price': 65.1},
    {'name': 'IBM', 'shares': 100, 'price': 70.44}]
>>> portfolio[0]
{'name': 'AA', 'shares': 100, 'price': 32.2}
>>> portfolio[1]
{'name': 'IBM', 'shares': 50, 'price': 91.1}
>>> portfolio[1]['shares']
50
>>> total = 0.0
>>> for s in portfolio:
        total += s['shares']*s['price']

>>> print(total)
44671.15
>>>
```

在这里可以看到，每个条目的不同字段是通过键名来访问的，而不是数字类型的列号。这通常是首选方式，因为这样得到的代码在以后易于阅读。

查看大型的字典或者列表可能会很混乱。要使调试的输出变得整洁，可以考虑使用 `pprint()`  函数。

```python
>>> from pprint import pprint
>>> pprint(portfolio)
[{'name': 'AA', 'price': 32.2, 'shares': 100},
    {'name': 'IBM', 'price': 91.1, 'shares': 50},
    {'name': 'CAT', 'price': 83.44, 'shares': 150},
    {'name': 'MSFT', 'price': 51.23, 'shares': 200},
    {'name': 'GE', 'price': 40.37, 'shares': 95},
    {'name': 'MSFT', 'price': 65.1, 'shares': 50},
    {'name': 'IBM', 'price': 70.44, 'shares': 100}]
>>>
```

### 练习 2.6：把字典当作容器

在使用索引而不是数字查找某元素的地方，字典是一种用来跟踪元素的很有用的方式。在 Python shell 中，尝试使用字典：

```python
>>> prices = { }
>>> prices['IBM'] = 92.45
>>> prices['MSFT'] = 45.12
>>> prices
... look at the result ...
>>> prices['IBM']
92.45
>>> prices['AAPL']
... look at the result ...
>>> 'AAPL' in prices
False
>>>
```

该 `Data/prices.csv`  文件包含一系列带有股票价格的行，看起来像下面这样：

```csv
"AA",9.22
"AXP",24.85
"BA",44.85
"BAC",11.27
"C",3.72
...
```

编写 `read_prices(filename)`函数将诸如此类的价格集合读取到字典中，字典的键代表股票的名字，字典的值代表股票的价格。

为此，从空字典开始，并且像上面做的那样开始插入值。但是，现在正在从从文件中读取值。

我们将使用该数据结构快速查找给定名称的股票的价格。

这部分需要一些小技巧。首先，确保像之前做的那样使用 `csv` 模块——无需在这里重复发明轮子。

```python
>>> import csv
>>> f = open('Data/prices.csv', 'r')
>>> rows = csv.reader(f)
>>> for row in rows:
        print(row)


['AA', '9.22']
['AXP', '24.85']
...
[]
>>>
```

另外一个小麻烦是 `Data/prices.csv` 文件可能有一些空行在里面。注意上面数据的最后一行是一个空列表——意味着那一行没有数据。

这有可能导致你的程序因为异常而终止。酌情使用 `try` 和 `except` 语句捕获这些异常。思考：使用 `if` 语句来防范错误的数据是否会更好？

编写完 `read_prices()` 函数，请交互式地测试它并确保其正常工作：

```python
>>> prices = read_prices('Data/prices.csv')
>>> prices['IBM']
106.28
>>> prices['MSFT']
20.89
>>>
```

### 练习 2.7：看看你是否可以退休

通过添加一些计算盈亏的语句到 `report.py` 程序，将所有的工作联系到一起。这些语句应该采用在练习 2.5 中存储股票名称的列表，以及在练习 2.6 中存储股票价格的字典，并计算投资组合的当前值以及盈亏。

[目录](../Contents.md) \| [上一节 (2.1 数据类型)](01_Datatypes.md) \| [下一节 (2.3 格式化)](03_Formatting.md)