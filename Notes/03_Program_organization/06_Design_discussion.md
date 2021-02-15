[目录](../Contents.md) \| [上一节 (3.5 主模块)](05_Main_module.md) \| [下一节 (4 类)](../04_Classes_objects/00_Overview.md)

# 3.6 设计讨论

本节，我们重新考虑之前所做的设计决策。

### 文件名与可迭代对象

考虑以下两个返回相同输出的程序。

```python
# Provide a filename
def read_data(filename):
    records = []
    with open(filename) as f:
        for line in f:
            ...
            records.append(r)
    return records

d = read_data('file.csv')
```

```python
# Provide lines
def read_data(lines):
    records = []
    for line in lines:
        ...
        records.append(r)
    return records

with open('file.csv') as f:
    d = read_data(f)
```

* 你更倾向于使用哪个函数？为什么？
* 哪个函数更灵活？

### 鸭子类型（Duck Typing）

在计算机程序设计中，[鸭子类型](https://en.wikipedia.org/wiki/Duck_typing) 用于确定一个对象是否可用于特定目的。这是 [鸭子测试](https://en.wikipedia.org/wiki/Duck_test) 的一种应用。

> 如果它看起来像鸭子、游泳像鸭子、叫声像鸭子，那么它可能就是只鸭子。

上述第二个 `read_data()`  函数接受任何可迭代对象，而不仅是文件行。

```python
def read_data(lines):
    records = []
    for line in lines:
        ...
        records.append(r)
    return records
```

这意味这我们可以使用它处理其它的*行（lines）*。

```python
# A CSV file
lines = open('data.csv')
data = read_data(lines)

# A zipped file
lines = gzip.open('data.csv.gz','rt')
data = read_data(lines)

# The Standard Input
lines = sys.stdin
data = read_data(lines)

# A list of strings
lines = ['ACME,50,91.1','IBM,75,123.45', ... ]
data = read_data(lines)
```

这种设计具有很大的灵活性。

*问题：我们应该拥抱还是反对这种灵活性？*

### 库设计最佳实践

通常，拥抱灵活性可以更好的服务于代码库。不要限制你的选择，灵活性大，带来的威力也大。

## 练习

### 练习 3.17：从文件名到类文件对象

现在，你已经创建了一个包含 `parse_csv()` 函数的 `fileparse.py` 文件。`parse_csv()` 函数像下面这样工作：

```python
>>> import fileparse
>>> portfolio = fileparse.parse_csv('Data/portfolio.csv', types=[str,int,float])
>>>
```

虽然函数接受的是一个文件名，但是，你可以使代码更具灵活性。请求修改函数，以便它可以接受任何类文件或者可迭代对象。例如：

```
>>> import fileparse
>>> import gzip
>>> with gzip.open('Data/portfolio.csv.gz', 'rt') as file:
...      port = fileparse.parse_csv(file, types=[str,int,float])
...
>>> lines = ['name,shares,price', 'AA,100,34.23', 'IBM,50,91.1', 'HPE,75,45.1']
>>> port = fileparse.parse_csv(lines, types=[str,int,float])
>>>
```

在新的代码中，如果像以前一样传递一个文件名会发生什么？

```
>>> port = fileparse.parse_csv('Data/portfolio.csv', types=[str,int,float])
>>> port
... look at output (it should be crazy) ...
>>>
```

正如上面代码显示的那样，这可能带来意想不到的结果，所以，修改的时候需要小心一些。你可以添加安全检查来避免这种情况吗？

### 练习 3.18：修复（fix）现有函数

请修复 `report.py` 文件中的  `read_portfolio()` 和 `read_prices()` 函数。以便它们可以使用修改后的 `parse_csv()` 函数。这应该只涉及较小的修改。之后，`report.py` 和 `pcost.py` 程序应能够像以往一样工作。

[目录](../Contents.md) \| [上一节 (3.5 主模块)](05_Main_module.md) \| [下一节 (4 类)](../04_Classes_objects/00_Overview.md)