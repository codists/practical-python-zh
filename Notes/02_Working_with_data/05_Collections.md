[目录](../Contents.md) \| [上一节 (2.4 序列)](04_Sequences.md) \| [下一节 (2.6 列表推导式)](06_List_comprehension.md)

# 2.5 collections 模块

`collections` 模块为数据处理提供了许多有用的对象。本部分简要介绍其中的一些特性。

### 示例：事物计数

假设要把每只股票的总份额表格化。

```python
portfolio = [
    ('GOOG', 100, 490.1),
    ('IBM', 50, 91.1),
    ('CAT', 150, 83.44),
    ('IBM', 100, 45.23),
    ('GOOG', 75, 572.45),
    ('AA', 50, 23.15)
]
```

此表中有两个 `IBM` 条目，两个 `GOOG` 条目，它们应该以某种方式合并到一起。

### 计数

解决方案：使用 `Counter` 模块。

```python
from collections import Counter
total_shares = Counter()
for name, shares, price in portfolio:
    total_shares[name] += shares

total_shares['IBM']     # 150
```

### 示例：一对多映射

问题：把一个键映射到多个值。

```python
portfolio = [
    ('GOOG', 100, 490.1),
    ('IBM', 50, 91.1),
    ('CAT', 150, 83.44),
    ('IBM', 100, 45.23),
    ('GOOG', 75, 572.45),
    ('AA', 50, 23.15)
]
```

像之前的示例那样，键 `IBM` 应具有两个不同的元组。

解决方案：使用 `defaultdict` 模块。

```python
from collections import defaultdict
holdings = defaultdict(list)
for name, shares, price in portfolio:
    holdings[name].append((shares, price))
holdings['IBM'] # [ (50, 91.1), (100, 45.23) ]
```

`defaultdict`模块确保每次访问键的时候获取到一个默认值。

### 示例：保留历史记录

问题：我们需要最近 N 件事的历史。

解决方案：使用 `deque` 模块。

```python
from collections import deque

history = deque(maxlen=N)
with open(filename) as f:
    for line in f:
        history.append(line)
        ...
```

## 练习

`collections` 可能是最有用的库模块之一，用于解决特殊用途的数据处理问题，例如表格化或者索引化。

在本练习中，我们来看几个简单的例子。首先运行`report.py` ，以便在交互模式下能够加载股票投资组合。

```bash
bash % python3 -i report.py
```

### 练习 2.18：使用 Counter 模块表格化

假设需要将每支股票的份额总数表格化，那么使用 `Counter` 对象会很容易。试试看：

```python
>>> portfolio = read_portfolio('Data/portfolio.csv')
>>> from collections import Counter
>>> holdings = Counter()
>>> for s in portfolio:
        holdings[s['name']] += s['shares']

>>> holdings
Counter({'MSFT': 250, 'IBM': 150, 'CAT': 150, 'AA': 100, 'GE': 95})
>>>
```

仔细观察`portfolio` 中的 `MSFT` 和 `IBM` 的多个条目是如何合并的。

可以像字典一样使用 Counter 模块检索单个值。

```python
>>> holdings['IBM']
150
>>> holdings['MSFT']
250
>>>
```

如果想要对值排名，这样做：

```python
>>> # Get three most held stocks
>>> holdings.most_common(3)
[('MSFT', 250), ('IBM', 150), ('CAT', 150)]
>>>
```

让我们获取另一个股票投资组合并生成一个新的 Counter 对象：

```python
>>> portfolio2 = read_portfolio('Data/portfolio2.csv')
>>> holdings2 = Counter()
>>> for s in portfolio2:
          holdings2[s['name']] += s['shares']

>>> holdings2
Counter({'HPQ': 250, 'GE': 125, 'AA': 50, 'MSFT': 25})
>>>
```

最后，通过一个简单的操作把所有的 holdings 变量合并。

```python
>>> holdings
Counter({'MSFT': 250, 'IBM': 150, 'CAT': 150, 'AA': 100, 'GE': 95})
>>> holdings2
Counter({'HPQ': 250, 'GE': 125, 'AA': 50, 'MSFT': 25})
>>> combined = holdings + holdings2
>>> combined
Counter({'MSFT': 275, 'HPQ': 250, 'GE': 220, 'AA': 150, 'IBM': 150, 'CAT': 150})
>>>
```

这只是对 Counter 功能的一个小尝试，如果发现需要对值进行表格化，那么就应该考虑使用它。

### 说明：collections 模块

`collections` 模块是 Python 所有库中最有用的库模块之一。实际上，我们可以为此做一个拓展教程，但是，现在这样做会分散注意力。从现在开始，把`collections`列为您的睡前读物，以备后用。

[目录](../Contents.md) \| [上一节 (2.4 序列)](04_Sequences.md) \| [下一节 (2.6 列表推导式)](06_List_comprehension.md)