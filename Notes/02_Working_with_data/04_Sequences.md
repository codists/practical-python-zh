[目录](../Contents.md) \| [上一节 (2.3 格式化)](03_Formatting.md) \| [下一节 (2.5 Collections模块)](05_Collections.md)

# 2.4 序列

### 序列数据类型

Python 有三种序列数据类型。

* 字符串：如 `'Hello'`。字符串是字符序列
* 列表：如 `[1, 4, 5]`。
* 元组：如 `('GOOG', 100, 490.1)`。

所有的序列都是有序的，由整数进行索引，并且具有长度。

```python
a = 'Hello'               # String
b = [1, 4, 5]             # List
c = ('GOOG', 100, 490.1)  # Tuple

# Indexed order
a[0]                      # 'H'
b[-1]                     # 5
c[1]                      # 100

# Length of sequence
len(a)                    # 5
len(b)                    # 3
len(c)                    # 3
```

序列可以通过重复操作符 * 进行重复：`s * n` 。

```python
>>> a = 'Hello'
>>> a * 3
'HelloHelloHello'
>>> b = [1, 2, 3]
>>> b * 2
[1, 2, 3, 1, 2, 3]
>>>
```

相同类型的序列可以通过加号 + 进行拼接：`s + t`。

```python
>>> a = (1, 2, 3)
>>> b = (4, 5)
>>> a + b
(1, 2, 3, 4, 5)
>>>
>>> c = [1, 5]
>>> a + c
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: can only concatenate tuple (not "list") to tuple
```

### 切片

切片是指着从序列中提取子序列。切片的语法为 `s[start:end]` 。 `start` 和 `end`  是想要的子序列的索引。

```python
a = [0,1,2,3,4,5,6,7,8]

a[2:5]    # [2,3,4]
a[-5:]    # [4,5,6,7,8]
a[:3]     # [0,1,2]
```

* 索引 `start` 和 `end` 必须是整数。
* 切片不包括结尾值。这就像数学上的半开区间。
* 如果省略索引，则它们默认为序列的开头或结尾。

### 切片与重新赋值

在列表上，切片可以被重新赋值和删除。

```python
# Reassignment
a = [0,1,2,3,4,5,6,7,8]
a[2:4] = [10,11,12]       # [0,1,10,11,12,4,5,6,7,8]
```

*注意：重新赋值的切片不需要具有相同的长度。*

```python
# Deletion
a = [0,1,2,3,4,5,6,7,8]
del a[2:4]                # [0,1,4,5,6,7,8]
```

### 序列的缩减

有一常见的函数用于把序列缩减为单个值。

```python
>>> s = [1, 2, 3, 4]
>>> sum(s)
10
>>> min(s)
1
>>> max(s)
4
>>> t = ['Hello', 'World']
>>> max(t)
'World'
>>>
```

### 迭代序列

可以使用 for 循环对序列中的元素进行迭代。

```python
>>> s = [1, 4, 9, 16]
>>> for i in s:
...     print(i)
...
1
4
9
16
>>>
```

在循环的每次迭代中，会获取一个新的项来处理。这个新的值会被放到迭代变量中。在此示例中，迭代变量为 x： 

```python
for x in s:         # `x` is an iteration variable
    ...statements
```

在每次迭代中，迭代变量的先前值会被覆盖（如果有）。循环结束后，迭代变量保留最后一个值。

### break 语句 

可以使用 `break` 语句提前跳出循环。

```python
for name in namelist:
    if name == 'Jake':
        break
    ...
    ...
statements
```

当 `break` 语句执行时，它退出循环并且进入下一个语句。`break` 语句仅应用于最内部的循环。如果此循环在另一个循环的内部，那么 `break` 不会中断外部循环。

### continue 语句

要跳过一个元素并且进入到下一个，请使用 `continue` 语句。

```python
for line in lines:
    if line == '\n':    # Skip blank lines
        continue
    # More statements
    ...
```

如果当前项不重要或者是在处理时需要忽略，那么使用 `continue` 语句很有用。

### 遍历整数

如果需要计数，请使用 `range()` 函数。

```python
for i in range(100):
    # i = 0,1,...,99
```

range() 函数的语法是`range([start,] end [,step])`。

```python
for i in range(100):
    # i = 0,1,...,99
for j in range(10,20):
    # j = 10,11,..., 19
for k in range(10,50,2):
    # k = 10,12,...,48
    # Notice how it counts in steps of 2, not 1.
```

* 不包括结尾值。这与切片类似。
* `start` 是可选的 ， 默认值是 `0`。
* `step` 是可选的，默认值是 `1`。
* 当需要的值时候 `range()`才计算值，实际上，它不存储大范围的数。

### enumerate() 函数

`enumerate` 函数为迭代添加一个额外的计数值。

```python
names = ['Elwood', 'Jake', 'Curtis']
for i, name in enumerate(names):
    # Loops with i = 0, name = 'Elwood'
    # i = 1, name = 'Jake'
    # i = 2, name = 'Curtis'
```

一般格式为`enumerate(sequence [, start = 0])`，`start`是可选的，一个很好的使用示例：读取文件时跟踪行数。

```python
with open(filename) as f:
    for lineno, line in enumerate(f, start=1):
        ...
```

`enumerate`可以看成以下语句的简写：

```python
i = 0
for x in s:
    statements
    i += 1
```

使用 `enumerate` 函数可以减少输入，运行速度也稍快一些。

### For 与元组

可以迭代多个变量：

```python
points = [
  (1, 4),(10, 40),(23, 14),(5, 6),(7, 8)
]
for x, y in points:
    # Loops with x = 1, y = 4
    #            x = 10, y = 40
    #            x = 23, y = 14
    #            ...
```

当使用多个变量时，每个元组被拆包为一组迭代变量。变量的数目必须与每个元组中的项数匹配。

### zip() 函数

`zip` 函数采用多个序列，并且生成将它们组合在一起的迭代器。

```python
columns = ['name', 'shares', 'price']
values = ['GOOG', 100, 490.1 ]
pairs = zip(columns, values)
# ('name','GOOG'), ('shares',100), ('price',490.1)
```

要获得结果，必须进行迭代。可以如先前所示的那样使用多个变量对元组进行拆包。

```python
for column, value in pairs:
    ...
```

`zip` 函数的常见用法是创建用于构造字典的键值对。

```python
d = dict(zip(columns, values))
```

## 练习

### 练习 2.13：计数

尝试一些基本的计数示例：

```python
>>> for n in range(10):            # Count 0 ... 9
        print(n, end=' ')

0 1 2 3 4 5 6 7 8 9
>>> for n in range(10,0,-1):       # Count 10 ... 1
        print(n, end=' ')

10 9 8 7 6 5 4 3 2 1
>>> for n in range(0,10,2):        # Count 0, 2, ... 8
        print(n, end=' ')

0 2 4 6 8
>>>
```

### 练习 2.14：更多序列操作

交互地试验一些序列缩减操作。

```python
>>> data = [4, 9, 1, 25, 16, 100, 49]
>>> min(data)
1
>>> max(data)
100
>>> sum(data)
204
>>>
```

尝试遍历数据。

```python
>>> for x in data:
        print(x)

4
9
...
>>> for n, x in enumerate(data):
        print(n, x)

0 4
1 9
2 1
...
>>>
```

有时候，`for` 语句，`len()` 和 `range()` 函数被初学者用于一些可怕的代码片段中，这些代码看起来像来自于古老的 C 程序。

```python
>>> for n in range(len(data)):
        print(data[n])

4
9
1
...
>>>
```

不要那样做。阅读这些代码不仅辣眼睛，而且内存效率低，运行慢。如果想要迭代数据，使用普通的`for` 循环即可。如果碰巧因为某些原因需要使用索引，请使用 `enumerate()`函数。

### 练习 2.15：enumerate() 函数使用示例

回想一下，`Data/missing.csv` 文件包含一个股票投资组合的数据，但是有一些行缺少值。请使用  `enumerate()` 函数修改 `pcost.py` 程序，以便在遇到错误的输入时，打印带有警告信息的行号。

```python
>>> cost = portfolio_cost('Data/missing.csv')
Row 4: Couldn't convert: ['MSFT', '', '51.23']
Row 7: Couldn't convert: ['IBM', '', '70.44']
>>>
```

为此，需要修改部分代码。

```python
...
for rowno, row in enumerate(rows, start=1):
    try:
        ...
    except ValueError:
        print(f'Row {rowno}: Bad row: {row}')
```

### 练习 2.16：使用 zip() 函数

在 `Data/portfolio.csv` 文件中，第一行包含列标题。在之前所有代码中，我们把它丢弃了。

```python
>>> f = open('Data/portfolio.csv')
>>> rows = csv.reader(f)
>>> headers = next(rows)
>>> headers
['name', 'shares', 'price']
>>>
```

但是，如果标题要用于其它有用的事情呢？这就涉及到 `zip()` 函数了。首先，尝试把文件标题和数据行配对。

```python
>>> row = next(rows)
>>> row
['AA', '100', '32.20']
>>> list(zip(headers, row))
[ ('name', 'AA'), ('shares', '100'), ('price', '32.20') ]
>>>
```

请注意 `zip()` 函数是如何把列标题与列值配对。在这里，我们使用 `list()` 函数把结果转换为列表，以便查看。通常，`zip()` 函数创建一个必须由 for 循环使用的迭代器。

这种配对是构建字典的中间步骤。现在尝试：

```python
>>> record = dict(zip(headers, row))
>>> record
{'price': '32.20', 'name': 'AA', 'shares': '100'}
>>>
```

在处理大量数据文件时，这种转换是最有用的技巧之一。例如，假设需要使 `pcost.py` 程序处理各种输入文件，但是不考虑名称，份额，价格所在列的编号。

修改 `pcost.py` 程序中的 `portfolio_cost()`，使其看起来像这样：

```python
# pcost.py

def portfolio_cost(filename):
    ...
        for rowno, row in enumerate(rows, start=1):
            record = dict(zip(headers, row))
            try:
                nshares = int(record['shares'])
                price = float(record['price'])
                total_cost += nshares * price
            # This catches errors in int() and float() conversions above
            except ValueError:
                print(f'Row {rowno}: Bad row: {row}')
        ...
```

现在，在一个完全不同的数据文件 `Data/portfoliodate.csv`（如下所示）上尝试 portfolio_cost() 函数。

```csv
name,date,time,shares,price
"AA","6/11/2007","9:50am",100,32.20
"IBM","5/13/2007","4:20pm",50,91.10
"CAT","9/23/2006","1:30pm",150,83.44
"MSFT","5/17/2007","10:30am",200,51.23
"GE","2/1/2006","10:45am",95,40.37
"MSFT","10/31/2006","12:05pm",50,65.10
"IBM","7/9/2006","3:15pm",100,70.44
```

```python
>>> portfolio_cost('Data/portfoliodate.csv')
44671.15
>>>
```

如果操作正确，会发现程序仍然能够正常运行，即使数据文件的列格式与之前的完全不同，这很酷！

此处所做的更改是微妙的，但是却意义重大。新版的 `portfolio_cost()`可以读取任何 CSV 文件，并从中选择需要的值，而不是硬编码去读取单个固定文件格式。只要文件有必要的列，代码就能正常运行。

修改在 2.3 节编写的 `report.py` 程序，以便能够使用相同的技术挑选出列标题。

尝试以 `Data/portfoliodate.csv` 文件作为输入，运行 `report.py` 程序，并观察是否生成和之前一样的答案。

### 练习 2.17：翻转字典

字典将键映射到值。例如，股票价格字典。

```python
>>> prices = {
        'GOOG' : 490.1,
        'AA' : 23.45,
        'IBM' : 91.1,
        'MSFT' : 34.23
    }
>>>
```

如果使用字典的 `items()`  方法，那么可以获取到键值对 `(key,value)`：

```python
>>> prices.items()
dict_items([('GOOG', 490.1), ('AA', 23.45), ('IBM', 91.1), ('MSFT', 34.23)])
>>>
```

但是，如果想要获取 `(value, key)` 键值对列表呢？

*提示：使用 `zip()`函数。*

```python
>>> pricelist = list(zip(prices.values(),prices.keys()))
>>> pricelist
[(490.1, 'GOOG'), (23.45, 'AA'), (91.1, 'IBM'), (34.23, 'MSFT')]
>>>
```

为什么这样操作？首先，这允许对字典数据执行确切类型的数据处理。

```python
>>> min(pricelist)
(23.45, 'AA')
>>> max(pricelist)
(490.1, 'GOOG')
>>> sorted(pricelist)
[(23.45, 'AA'), (34.23, 'MSFT'), (91.1, 'IBM'), (490.1, 'GOOG')]
>>>
```

其次，这也说明了元组的一个重要特征，当在比较中使用元组时，从第一项开始，逐元素进行比较，类似于字符串中字符与字符逐个比较。

`zip()` 函数经常应用于需要从不同的地方把数据进行配对。例如，为了使用已命名的值构建字典，将列名和列值进行配对。

请注意，`zip()` 函数不限于一对。例如，可以使用任意数量的列表作为输入。

```python
>>> a = [1, 2, 3, 4]
>>> b = ['w', 'x', 'y', 'z']
>>> c = [0.2, 0.4, 0.6, 0.8]
>>> list(zip(a, b, c))
[(1, 'w', 0.2), (2, 'x', 0.4), (3, 'y', 0.6), (4, 'z', 0.8))]
>>>
```

另外，请注意，一旦最短的输入序列耗尽，`zip()` 函数将会停止。

```python
>>> a = [1, 2, 3, 4, 5, 6]
>>> b = ['x', 'y', 'z']
>>> list(zip(a,b))
[(1, 'x'), (2, 'y'), (3, 'z')]
>>>
```

[目录](../Contents.md) \| [上一节 (2.3 格式化)](03_Formatting.md) \| [下一节 (2.5 Collections模块)](05_Collections.md)