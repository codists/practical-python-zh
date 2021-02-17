[目录](../Contents.md) \| [上一节 (1.4 字符串)](04_Strings.md) \| [下一节 (1.6 文件)](06_Files.md)

# 1.5 列表

本节介绍 Python 原始数据类型列表（list）。 列表是一种有序的集合。

### 创建列表

使用方括号 [] 来定义列表字面量。

```python
names = [ 'Elwood', 'Jake', 'Curtis' ]
nums = [ 39, 38, 42, 65, 111]
```

有时候，列表也可以通过其它方法创建。例如：使用字符串的 `split()` 方法可以将一个字符串拆分为一个列表：

```python
>>> line = 'GOOG,100,490.10'
>>> row = line.split(',')
>>> row
['GOOG', '100', '490.10']
>>>
```

### 列表操作

列表可以存储任何类型的项（译注：item，或者称为**元素**）。使用 `append()` 方法添加一个新的项：

```python
names.append('Murphy')    # Adds at end
names.insert(2, 'Aretha') # Inserts in middle
```

使用加号 + 来拼接列表：

```python
s = [1, 2, 3]
t = ['a', 'b']
s + t           # [1, 2, 3, 'a', 'b']
```

列表通过整数进行索引，索引从 0 开始。

```python
names = [ 'Elwood', 'Jake', 'Curtis' ]

names[0]  # 'Elwood'
names[1]  # 'Jake'
names[2]  # 'Curtis'
```

负索引从列表尾部开始计数：

```python
names[-1] # 'Curtis'
```

你可以改变列表中的任何项:

```python
names[1] = 'Joliet Jake'
names                     # [ 'Elwood', 'Joliet Jake', 'Curtis' ]
```

列表的长度：

```python
names = ['Elwood','Jake','Curtis']
len(names)  # 3
```

成员测试（`in`, `not in`）：

```python
'Elwood' in names       # True
'Britney' not in names  # True
```

复制（`s * n`）：

```python
s = [1, 2, 3]
s * 3   # [1, 2, 3, 1, 2, 3, 1, 2, 3]
```

### 列表遍历和查找：

使用 `for` 遍历列表内容：

```python
for name in names:
    # use name
    # e.g. print(name)
    ...
```

这和其它编程语言的 `foreach` 语句类似。

为了快速找到某项的位置（索引），使用 `index()` 函数：

```python
names = ['Elwood','Jake','Curtis']
names.index('Curtis')   # 2
```

如果元素出现不止一次，`index()` 方法将会返回元素第一次出现的索引。

如果元素没有找到，`index()` 方法将引发 `ValueError` 异常。

### 列表删除

你可以按元素值或者按索引删除元素：

```python
# Using the value
names.remove('Curtis')

# Using the index
del names[1]
```

移除一个元素不会“留空”。其它的元素将会移动来填充删除元素后腾出的空间。如果元素出现不止一次，`remove()` 方法将只删除第一次出现的元素。

### 列表排序

列表可以“原地”排序：

```python
s = [10, 1, 7, 3]
s.sort()                    # [1, 3, 7, 10]

# Reverse order
s = [10, 1, 7, 3]
s.sort(reverse=True)        # [10, 7, 3, 1]

# It works with any ordered data
s = ['foo', 'bar', 'spam']
s.sort()                    # ['bar', 'foo', 'spam']
```

如果你想生成一个新的列表，使用 `sorted()` 函数：

```python
t = sorted(s)               # s unchanged, t holds sorted values
```

### 列表和数学

警告：列表不是为数学运算而设计的：

```python
>>> nums = [1, 2, 3, 4, 5]
>>> nums * 2
[1, 2, 3, 4, 5, 1, 2, 3, 4, 5]
>>> nums + [10, 11, 12, 13, 14]
[1, 2, 3, 4, 5, 10, 11, 12, 13, 14]
```

特别地，列表无法像 MATLAB, Octave, R 那样表示向量/矩阵。但是，有一些包可以帮助你解决这个问题（例如：[numpy](https://numpy.org)）。

## 练习

在本次练习中，我们尝试使用 Python 的列表数据类型。在上一节中，你使用了包含股票代码的字符串：

```python
>>> symbols = 'HPQ,AAPL,IBM,MSFT,YHOO,DOA,GOOG'
```

使用字符串的 `split()`  方法把 symbols 拆分为一个包含股票代码名字的列表：

```python
>>> symlist = symbols.split(',')
```

### 练习1.19：提取和重新分配列表元素

尝试一些查找：

```python
>>> symlist[0]
'HPQ'
>>> symlist[1]
'AAPL'
>>> symlist[-1]
'GOOG'
>>> symlist[-2]
'DOA'
>>>
```

尝试重新分配一个值：

```python
>>> symlist[2] = 'AIG'
>>> symlist
['HPQ', 'AAPL', 'AIG', 'MSFT', 'YHOO', 'DOA', 'GOOG']
>>>
```

切片：

```python
>>> symlist[0:3]
['HPQ', 'AAPL', 'AIG']
>>> symlist[-2:]
['DOA', 'GOOG']
>>>
```

创建一个空的列表并添加一个元素到其中：

```python
>>> mysyms = []
>>> mysyms.append('GOOG')
>>> mysyms
['GOOG']
```

你可以将一个列表的一部分重新分配到另一个列表中。例如：

```python
>>> symlist[-2:] = mysyms
>>> symlist
['HPQ', 'AAPL', 'AIG', 'MSFT', 'YHOO', 'GOOG']
>>>
```

当执行此操作时，左手边的列表（`symlist`）的大小将会被适当调整，以适应右手边的列表。

例如，在上面的实例中，`symlist` 的最后两项被 `mysyms` 列表中的单个元素（'GOOG'）取代。

### 练习1.20：遍历列表元素

 `for` 循环可以遍历列表这样的序列。通过输入下列的循环并且查看发生了什么来验证这点：

```python
>>> for s in symlist:
        print('s =', s)
# Look at the output
```

### 练习1.21：成员测试

使用 `in` 或者 `not in` 操作符来检查 `'AIG'`，`'AA'`，和  `'CAT'` 是否在 symbols 列表中：

```python
>>> # Is 'AIG' IN the `symlist`?
True
>>> # Is 'AA' IN the `symlist`?
False
>>> # Is 'CAT' NOT IN the `symlist`?
True
>>>
```

### 练习 1.22：添加，插入和删除元素

使用 `append()` 方法把 `'RHT'` 添加到列表 `symlist` 的末尾：

```python
>>> # append 'RHT'
>>> symlist
['HPQ', 'AAPL', 'AIG', 'MSFT', 'YHOO', 'GOOG', 'RHT']
>>>
```

使用 `insert()` 方法把 `'AA'` 作为列表的第二个元素插入到列表中：

```python
>>> # Insert 'AA' as the second item in the list
>>> symlist
['HPQ', 'AA', 'AAPL', 'AIG', 'MSFT', 'YHOO', 'GOOG', 'RHT']
>>>
```

使用 `remove()` 方法从列表中删除 `'MSFT'`：

```python
>>> # Remove 'MSFT'
>>> symlist
['HPQ', 'AA', 'AAPL', 'AIG', 'YHOO', 'GOOG', 'RHT']
>>>
```

添加一个重复的 `'YHOO'` 条目到列表的末尾。

*注意：列表有重复的值是完全没有问题的：*

```python
>>> # Append 'YHOO'
>>> symlist
['HPQ', 'AA', 'AAPL', 'AIG', 'YHOO', 'GOOG', 'RHT', 'YHOO']
>>>
```

使用 `index()` 方法查看 `'YHOO'` 在列表中的第一个位置：

```python
>>> # Find the first index of 'YHOO'
4
>>> symlist[4]
'YHOO'
>>>
```

统计 `'YHOO'` 在列表中出现了多少次：

```python
>>> symlist.count('YHOO')
2
>>>
```

删除第一次出现的 `'YHOO'`：

```python
>>> # Remove first occurrence 'YHOO'
>>> symlist
['HPQ', 'AA', 'AAPL', 'AIG', 'GOOG', 'RHT', 'YHOO']
>>>
```

众所周知，没有方法找到或者删除某个元素在列表中重复出现的所有项。但是，我们将会在第二节看到一种优雅的方式去实现它。

### 练习1.23：排序

想要对一个列表排序吗？使用 `sort()` 方法。试试看：

```python
>>> symlist.sort()
>>> symlist
['AA', 'AAPL', 'AIG', 'GOOG', 'HPQ', 'RHT', 'YHOO']
>>>
```

想要对一个列表倒序吗？尝试这个：

```python
>>> symlist.sort(reverse=True)
>>> symlist
['YHOO', 'RHT', 'HPQ', 'GOOG', 'AIG', 'AAPL', 'AA']
>>>
```

注意：对列表排序可以“原地”修改其内容。也就是说，是对列表的元素进行“洗牌”，而不是创建一个新的列表作为结果。

### 练习 1.24：列表转字符串

想要把一个包含字符串的列表连接到一个字符串中吗？像下面这样使用字符串的 `join()` 方法实现（注意：这初看起来很有趣）：

```python
>>> a = ','.join(symlist)
>>> a
'YHOO,RHT,HPQ,GOOG,AIG,AAPL,AA'
>>> b = ':'.join(symlist)
>>> b
'YHOO:RHT:HPQ:GOOG:AIG:AAPL:AA'
>>> c = ''.join(symlist)
>>> c
'YHOORHTHPQGOOGAIGAAPLAA'
>>>
```

### 练习1.25：包含任何内容的列表

列表可以包含任何类型的对象，包括列表（示例：嵌套的列表）。试试看：

```python
>>> nums = [101, 102, 103]
>>> items = ['spam', symlist, nums]
>>> items
['spam', ['YHOO', 'RHT', 'HPQ', 'GOOG', 'AIG', 'AAPL', 'AA'], [101, 102, 103]]
```

请注意上面的输出，`items`  是一个包含三个元素的列表，第一个元素是一个字符串，其它两个元素是列表。

你可以通过多个索引操作来访问嵌套列表的项。

```python
>>> items[0]
'spam'
>>> items[0][0]
's'
>>> items[1]
['YHOO', 'RHT', 'HPQ', 'GOOG', 'AIG', 'AAPL', 'AA']
>>> items[1][1]
'RHT'
>>> items[1][1][2]
'T'
>>> items[2]
[101, 102, 103]
>>> items[2][1]
102
>>>
```

尽管在技术上能够生成非常复杂的列表结构，但作为一般规则，你还是希望保持简单。通常，列表存储类型相同的元素。例如，完全由数字或者文本字符串构成的列表。在同一个列表中混合不同类型的数据，往往会让人感到头痛，因此最好避免这种情况。

[目录](../Contents.md) \| [上一节 (1.4 字符串)](04_Strings.md) \| [下一节 (1.6 文件)](06_Files.md)