[目录](../Contents.md) \| [上一节 (2.5 collections模块)](05_Collections.md) \| [下一节 (2.7 对象模型)](07_Objects.md)

# 2.6 列表推导式

一个常见的任务是处理列表中的项。本节介绍列表推导式，完成此任务的强大工具。

### 创建新列表

列表推导式通过将操作应用于序列的每一个元素来创建新列表。

```python
>>> a = [1, 2, 3, 4, 5]
>>> b = [2*x for x in a ]
>>> b
[2, 4, 6, 8, 10]
>>>
```

再如:

```python
>>> names = ['Elwood', 'Jake']
>>> a = [name.lower() for name in names]
>>> a
['elwood', 'jake']
>>>
```

列表推导式的一般语法是：`[ <expression> for <variable_name> in <sequence> ]`。

### 过滤

也可以在列表推导式中对元素进行过滤。

```python
>>> a = [1, -5, 4, 2, -2, 10]
>>> b = [2*x for x in a if x > 0 ]
>>> b
[2, 8, 4, 20]
>>>
```

### 用例

列表推导式超级有用。例如，可以收集特定字典字段的值：

```python
stocknames = [s['name'] for s in stocks]
```

在序列上执行类数据库查询：

```python
a = [s for s in stocks if s['price'] > 100 and s['shares'] > 50 ]
```

也可以把列表推导式与序列缩减合并在一起。

```python
cost = sum([s['shares']*s['price'] for s in stocks])
```

### 一般语法

```code
[ <expression> for <variable_name> in <sequence> if <condition>]
```

上面语法的含义：

```python
result = []
for variable_name in sequence:
    if condition:
        result.append(expression)
```

### 历史题外话

列表推导式来自于数学（集合构建符号）。

```code
a = [ x * x for x in s if x > 0 ] # Python

a = { x^2 | x ∈ s, x > 0 }         # Math
```

这在其它几种语言中也实现了，虽然大部分的程序员可能已经想不起他们的数学课了。所以，可以将其视为很酷的列表快捷方式。

## 练习

首先运行 `report.py` 程序，以便能够在交互模式下中加载股票投资组合。

```bash
bash % python3 -i report.py
```

现在，在 Python 交互提示符下，输入语句以执行下述操作。这些操作对投资组合数据执行各类缩减，转换和查找。

### 练习 2.19：列表推导式

尝试一些简单的列表推导式来熟悉语法：

```python
>>> nums = [1,2,3,4]
>>> squares = [ x * x for x in nums ]
>>> squares
[1, 4, 9, 16]
>>> twice = [ 2 * x for x in nums if x > 2 ]
>>> twice
[6, 8]
>>>
```

请注意列表推导式是如何通过适当转换或过滤的数据创建一个新列表的。

### 练习 2.20：序列缩减

使用单个 Python 语句计算投资组合的总价。

```python
>>> portfolio = read_portfolio('Data/portfolio.csv')
>>> cost = sum([ s['shares'] * s['price'] for s in portfolio ])
>>> cost
44671.15
>>>
```

完成后，展示如何使用单个语句计算投资组合的当前值。

```python
>>> value = sum([ s['shares'] * prices[s['name']] for s in portfolio ])
>>> value
28686.1
>>>
```

上面的两个操作都是映射缩减的列子。列表推导式将操作映射到整个列表。

```python
>>> [ s['shares'] * s['price'] for s in portfolio ]
[3220.0000000000005, 4555.0, 12516.0, 10246.0, 3835.1499999999996, 3254.9999999999995, 7044.0]
>>>
```

然后，`sum()` 函数对所有结果进行缩减。

```python
>>> sum(_)
44671.15
>>>
```

有了这些知识，您现在就可以准备成立一家大数据创业公司了。

### 练习 2.21：数据查询

请尝试以下各种数据查询示例。

首选是创建一个列表，存储持有 100 股以上的股票投资组合。

```python
>>> more100 = [ s for s in portfolio if s['shares'] > 100 ]
>>> more100
[{'price': 83.44, 'name': 'CAT', 'shares': 150}, {'price': 51.23, 'name': 'MSFT', 'shares': 200}]
>>>
```

持有 MSFT 和 IBM 股票的所有投资组合。

```python
>>> msftibm = [ s for s in portfolio if s['name'] in {'MSFT','IBM'} ]
>>> msftibm
[{'price': 91.1, 'name': 'IBM', 'shares': 50}, {'price': 51.23, 'name': 'MSFT', 'shares': 200},
  {'price': 65.1, 'name': 'MSFT', 'shares': 50}, {'price': 70.44, 'name': 'IBM', 'shares': 100}]
>>>
```

持有总价超过 $10000 的所有股票投资组合。

```python
>>> cost10k = [ s for s in portfolio if s['shares'] * s['price'] > 10000 ]
>>> cost10k
[{'price': 83.44, 'name': 'CAT', 'shares': 150}, {'price': 51.23, 'name': 'MSFT', 'shares': 200}]
>>>
```

### 练习 2.22：数据提取

展示如何构建元组`(name, shares)` 列表，名称（`name` ）和 股数（`shares`）从股票投资组合（`portfolio`）中获取。

```python
>>> name_shares =[ (s['name'], s['shares']) for s in portfolio ]
>>> name_shares
[('AA', 100), ('IBM', 50), ('CAT', 150), ('MSFT', 200), ('GE', 95), ('MSFT', 50), ('IBM', 100)]
>>>
```

如果将方括号（`[`,`]`）更改为花括号（`{`, `}`），那么将得到集合推导式。这会得到独一无二的的或无重复的值。

例如，这将确定集合中的股票名称是独一无二的。

```python
>>> names = { s['name'] for s in portfolio }
>>> names
{ 'AA', 'GE', 'IBM', 'MSFT', 'CAT' }
>>>
```

如果指定键值对（`key:value`），则可以构建一个字典。例如，构建一个将股票名称映射到持有的股票数量的字典。

```python
>>> holdings = { name: 0 for name in names }
>>> holdings
{'AA': 0, 'GE': 0, 'IBM': 0, 'MSFT': 0, 'CAT': 0}
>>>
```

后面的特性就是众所皆知的字典推导式。让我们将其表格化：

```python
>>> for s in portfolio:
        holdings[s['name']] += s['shares']

>>> holdings
{ 'AA': 100, 'GE': 95, 'IBM': 150, 'MSFT':250, 'CAT': 150 }
>>>
```

请尝试以下示例，该示例将 `prices` 字典过滤出仅在 portfolio 中出现的名称（name），

```python
>>> portfolio_prices = { name: prices[name] for name in names }
>>> portfolio_prices
{'AA': 9.22, 'GE': 13.48, 'IBM': 106.28, 'MSFT': 20.89, 'CAT': 35.46}
>>>
```

### 练习 2.23: 从 CSV 文件提取数据

在各类数据处理中，知道如何将列表，集合，字典推导式联合使用会非常有用。这里有一个示例，展示如何从 CSV 文件中提取所选择的列。

首先，从 CSV 文件读取一行标题信息：

```python
>>> import csv
>>> f = open('Data/portfoliodate.csv')
>>> rows = csv.reader(f)
>>> headers = next(rows)
>>> headers
['name', 'date', 'time', 'shares', 'price']
>>>
```

接着，定义一个变量列出实际需要的列：

```python
>>> select = ['name', 'shares', 'price']
>>>
```

现在，在 CSV 源文件中找到以上各列的索引。

```python
>>> indices = [ headers.index(colname) for colname in select ]
>>> indices
[0, 3, 4]
>>>
```

最后，使用字典推导式读取数据的一行并把其转换为字典。

```python
>>> row = next(rows)
>>> record = { colname: row[index] for colname, index in zip(select, indices) }   # dict-comprehension
>>> record
{'price': '32.20', 'name': 'AA', 'shares': '100'}
>>>
```

如果您对前面的操作感到满意，那么请读取文件的剩余部分：

```python
>>> portfolio = [ { colname: row[index] for colname, index in zip(select, indices) } for row in rows ]
>>> portfolio
[{'price': '91.10', 'name': 'IBM', 'shares': '50'}, {'price': '83.44', 'name': 'CAT', 'shares': '150'},
  {'price': '51.23', 'name': 'MSFT', 'shares': '200'}, {'price': '40.37', 'name': 'GE', 'shares': '95'},
  {'price': '65.10', 'name': 'MSFT', 'shares': '50'}, {'price': '70.44', 'name': 'IBM', 'shares': '100'}]
>>>
```

天啊，已经把 `read_portfolio()` 函数简化为单个语句了。

### 说明

列表推导式在 Python 中常用作转换，过滤和收集数据的有效方法。由于语法的原因，请不要走极端——应该让每个列表推导式尽可能简单。可以将事情分解为多个步骤。例如，不清楚你会不会把最后一个例子强加给毫不知情的同事。

也就是说，知道如何快速处理数据是一项非常有用的技能。在很多情况下，可能必须解决某种一次性的问题，包括数据导入，导出，提取等。成为列表推导式的大师可以大大减少设计方案所花费的时间。另外，不要忘记  `collections` 模块。

[目录](../Contents.md) \| [上一节 (2.5 collections模块)](05_Collections.md) \| [下一节 (2.7 对象模型)](07_Objects.md)