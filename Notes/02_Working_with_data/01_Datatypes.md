[目录](../Contents.md) \| [上一节 (1.7 函数)](../01_Introduction/07_Functions.md) \| [下一节 (2.2 容器)](02_Containers.md)

# 2.1 数据类型和数据结构

本节以元组和字典为代表介绍数据结构。

### 原始数据类型

Python 有一些原始数据类型：

* 整数
* 浮点数
* 字符串（文本）

### 空类型

```python
email_address = None
```

`None` 常用作可选值或缺失值的占位符。它在条件语句中计算为 `False`。

```python
if email_address:
    send_email(email_address, msg)
```

### 数据结构

实际的程序具有更复杂的数据。例如，关于股票的持有信息：

```code
100 shares of GOOG at $490.10
```

这是一个包含三个部分的“对象”：

* 股票的名称或符号（"GOOG"，字符串）
* 股份数目（100，整数）
* 价格（490.10，浮点数）

### 元组

元组是分组在一起的值的集合。

示例：

```python
s = ('GOOG', 100, 490.1)
```

有时候会在语法上省略 `()` 。

```python
s = 'GOOG', 100, 490.1
```

特殊情况（0 元组，1 元组）。

```python
t = ()            # An empty tuple
w = ('GOOG', )    # A 1-item tuple
```

元组一般用来表示简单的记录或结构。

通常，它是由多个部分组成的单个对象。这有一个很好的类比：元组就像数据库表中的一行。

元组的内容是有序的（类似于数组）。

```python
s = ('GOOG', 100, 490.1)
name = s[0]                 # 'GOOG'
shares = s[1]               # 100
price = s[2]                # 490.1
```

但是，元组的内容无法修改。

```python
>>> s[1] = 75
TypeError: object does not support item assignment
```

你可以基于当前元组创建一个新元组。

```python
s = (s[0], 75, s[2])
```

### 元组打包

元组更多的是把相关的项打包到一个实体（entity）中。

```python
s = ('GOOG', 100, 490.1)
```

然后，该元组很容易作为单个对象传递给程序的其它部分。

### 元组拆包

要在其它地方使用元组，可以把元组的各部分拆包为变量。

```python
name, shares, price = s
print('Cost', shares * price)
```

左侧变量的数目必须与元组的结构匹配。

```python
name, shares = s     # ERROR
Traceback (most recent call last):
...
ValueError: too many values to unpack
```

### 元组与列表

元组看起来像只读列表。但是，元组最常用于由多个部分组成的单项。列表通常是类型相同的项的集合，

```python
record = ('GOOG', 100, 490.1)       # A tuple representing a record in a portfolio

symbols = [ 'GOOG', 'AAPL', 'IBM' ]  # A List representing three stock symbols
```

### 字典

字典是键到值的映射。有时，字典也称为哈希表（hash table）或关联数组（associative array）。键用作访问值的索引。

```python
s = {
    'name': 'GOOG',
    'shares': 100,
    'price': 490.1
}
```

### 常见操作

要从字典中获取值，请使用键名。

```python
>>> print(s['name'], s['shares'])
GOOG 100
>>> s['price']
490.10
>>>
```

要添加或修改值，请使用键名进行分配。

```python
>>> s['shares'] = 75
>>> s['date'] = '6/6/2007'
>>>
```

要删除值，请使用 `del` 语句。

```python
>>> del s['date']
>>>
```

### 为什么使用字典？

当存在很多不同的值并且可能会修改或操作这些值时，字典很有用。字典使代码更具可读性。

```python
s['price']
# vs
s[2]
```

## 练习

在上次的几个练习中，编写了一个取数据文件 `Data/portfolio.csv` 的程序 。使用 `csv` 模块，可以轻松地逐行读取文件。

```python
>>> import csv
>>> f = open('Data/portfolio.csv')
>>> rows = csv.reader(f)
>>> next(rows)
['name', 'shares', 'price']
>>> row = next(rows)
>>> row
['AA', '100', '32.20']
>>>
```

尽管读取文件很容易，但是与读取数据相比，通常使用数据做更多的事情。例如，也许想存储它并对其执行一些计算。不幸的是，原始的数据“行”并不能这样做。例如，即使是简单的数学计算也不行。

```python
>>> row = ['AA', '100', '32.20']
>>> cost = row[1] * row[2]
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
TypeError: can't multiply sequence by non-int of type 'str'
>>>
```

要执行更多的操作，通常需要以某种方式解释原始数据，并将其转换为更有用的对象类型，以便以后处理。有两种简单的方式可以选择：元组或者字典。

### 练习 2.1：元组

在交互式提示符下，创建以下代表上一行的元组，但数字列要转换为恰当的数字。

```python
>>> t = (row[0], int(row[1]), float(row[2]))
>>> t
('AA', 100, 32.2)
>>>
```

使用这种方式，现在可以使用股份数目乘以价格来计算总价，

```python
>>> cost = t[1] * t[2]
>>> cost
3220.0000000000005
>>>
```

在 Python 中，数学没用了吗？结果为什么是  3220.0000000000005？

这是计算机上浮点硬件的产物，只能在二进制（而不是十进制）中准确表示小数。即使是涉及十进制小数的简单计算，也会引入小的误差。这很正常，如果你之前没有见过，可能会有点惊讶。

虽然在所有使用浮点小数的编程语言中都会发生这种情况，但是打印的时候可以把它隐藏，例如：

```python
>>> print(f'{cost:0.2f}')
3220.00
>>>
```

元组是只读的。可以通过尝试把股份数目改为 75 来验证这点。

```python
>>> t[1] = 75
Traceback (most recent call last):
    File "<stdin>", line 1, in <module>
TypeError: 'tuple' object does not support item assignment
>>>
```

尽管无法更改元组的内容，但是始终可以创建一个全新的元组来替换旧的元组。

```python
>>> t = (t[0], 75, t[2])
>>> t
('AA', 75, 32.2)
>>>
```

每当像这样重新分配现有变量名时，旧值就会被丢弃。虽然上面的赋值可能看起来像在修改元组，但实际上是在创建一个新的元组，并且将旧的元组丢弃。

元组通常用于将值打包或拆包到变量中。请尝试以下操作：

```python
>>> name, shares, price = t
>>> name
'AA'
>>> shares
75
>>> price
32.2
>>>
```

取上面的变量并将其打包回元组中：

```python
>>> t = (name, 2*shares, price)
>>> t
('AA', 150, 32.2)
>>>
```

### 练习 2.2：把字典当作数据结构

可以创建字典来替代元组。

```python
>>> d = {
        'name' : row[0],
        'shares' : int(row[1]),
        'price'  : float(row[2])
    }
>>> d
{'name': 'AA', 'shares': 100, 'price': 32.2 }
>>>
```

计算持有的总价：

```python
>>> cost = d['shares'] * d['price']
>>> cost
3220.0000000000005
>>>
```

将此示例与上面涉及元组的相同的计算进行比较，将股份数目修改为 75。

```python
>>> d['shares'] = 75
>>> d
{'name': 'AA', 'shares': 75, 'price': 32.2 }
>>>
```

与元组不同，字典可以自由修改。添加一些属性：

```python
>>> d['date'] = (6, 11, 2007)
>>> d['account'] = 12345
>>> d
{'name': 'AA', 'shares': 75, 'price':32.2, 'date': (6, 11, 2007), 'account': 12345}
>>>
```

### 练习 2.3: 字典的其它操作

如果将一个字典转换为列表，则将获得其所有的键：

```python
>>> list(d)
['name', 'shares', 'price', 'date', 'account']
>>>
```

类似地，如果使用 `for` 语句对字典进行迭代，则将获得其所有的键。 

```python
>>> for k in d:
        print('k =', k)

k = name
k = shares
k = price
k = date
k = account
>>>
```

尝试使用这个同时执行查找的变体：

```python
>>> for k in d:
        print(k, '=', d[k])

name = AA
shares = 75
price = 32.2
date = (6, 11, 2007)
account = 12345
>>>
```

也可以使用 `keys()` 方法获得所有的键：

```python
>>> keys = d.keys()
>>> keys
dict_keys(['name', 'shares', 'price', 'date', 'account'])
>>>
```

在这里，`keys()` 稍微有点不同，它返回的是一个 `dict_keys` 对象。

这是对原始字典的覆盖，它始终提供当前字典的键——即使字典改变了。例如，试试一下操作：

```python
>>> del d['account']
>>> keys
dict_keys(['name', 'shares', 'price', 'date'])
>>>
```

请注意，尽管没有再次调用 `d.keys()` ，但键`'account'` 消失了。

一个更优雅地一起使用键和值的方式是使用 `items()` 方法。这可以获得键值组成的元组 `(key, value)`。 

```python
>>> items = d.items()
>>> items
dict_items([('name', 'AA'), ('shares', 75), ('price', 32.2), ('date', (6, 11, 2007))])
>>> for k, v in d.items():
        print(k, '=', v)

name = AA
shares = 75
price = 32.2
date = (6, 11, 2007)
>>>
```

如果有类似于 `items` 的元组，那么可以使用 `dict()`  函数创建一个字典。请尝试以下操作：

```python
>>> items
dict_items([('name', 'AA'), ('shares', 75), ('price', 32.2), ('date', (6, 11, 2007))])
>>> d = dict(items)
>>> d
{'name': 'AA', 'shares': 75, 'price':32.2, 'date': (6, 11, 2007)}
>>>
```

[目录](../Contents.md) \| [上一节 (1.7 函数)](../01_Introduction/07_Functions.md) \| [下一节 (2.2 容器)](02_Containers.md)