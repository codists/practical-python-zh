[目录](../Contents.md) \| [上一节 (6.3 生产者/消费者)](03_Producers_consumers.md) \| [下一节 (7 高级主题)](../07_Advanced_Topics/00_Overview.md)

# 6.4 有关生成器的更多信息

本节介绍其它与生成器相关的主题，包括生成器表达式（generator expressions）和 itertools 模块。

### 生成器表达式

生成器表达式可以理解为列表解析式（list comprehension）的生成器版本：

```python
>>> a = [1,2,3,4]
>>> b = (2*x for x in a)
>>> b
<generator object at 0x58760>
>>> for i in b:
...   print(i, end=' ')
...
2 4 6 8
>>>
```

生成器表达式与列表列表解析式的区别：

* 不构造列表
* 唯一有用的目的是迭代
* 一旦被消费，无法重复使用

生成器表达式语法：

```python
(<expression> for i in s if <conditional>)
```

生成器表达式也可以用作函数参数：

```python
sum(x*x for x in a)
```

生成器表达式可应用于任何迭代：

```python
>>> a = [1,2,3,4]
>>> b = (x*x for x in a)
>>> c = (-x for x in b)
>>> for i in c:
...   print(i, end=' ')
...
-1 -4 -9 -16
>>>
```

生成器表达式在代码中的一个主要用途是：对序列进行计算，但只使用一次结果。例如，跳过文件中的所有注释。

```python
f = open('somefile.txt')
lines = (line for line in f if not line.startswith('#'))
for line in lines:
    ...
f.close()
```

使用生成器，代码运行更快并且占用的内存更少，类似应用于流的过滤器。

### 为什么使用生成器


* 许多问题以迭代的形式进行表示会更清晰
  * 对集合中的元素进行遍历，并执行某些操作（如查找、替换，修改等）。
  * 处理管道（processing pipelines）可应用于各种数据处理问题。
* 内存效率更高
  * 只在需要的时候才生成值。
  * 不构造庞大的列表。
  * 可对流数据进行操作。
* 生成器表达式鼓励代码复用
  * 从使用迭代的代码中分离出迭代。
  * 构建迭代函数工具箱，混合搭配（mix-n-match）使用各种工具。

### `itertools` 模块

`itertools` 是 Python 自带的一个库模块，包含各种函数，旨在帮助开发迭代器/生成器。

```python
itertools.chain(s1,s2)
itertools.count(n)
itertools.cycle(s)
itertools.dropwhile(predicate, s)
itertools.groupby(s)
itertools.ifilter(predicate, s)
itertools.imap(function, s1, ... sN)
itertools.repeat(s, n)
itertools.tee(s, ncopies)
itertools.izip(s1, ... , sN)
```

`itertools`  模块中所有的函数都可以迭代地处理数据。并且，这些函数实现了各类迭代模式。

有关生成器的更多信息可以查看 PyCon '08 上的 [Generator Tricks for Systems Programmers](http://www.dabeaz.com/generators/) 教程。

## 练习

在上一节练习中，我们编写代码监视写入日志文件的数据，并将其解析为行序列。

本次练习将以上一节练习中的代码为基础，所以请确保 `Data/stocksim.py` 仍在运行。

### 练习 6.13：生成器表达式

生成器表达式是列表解析式的生成器版本。示例：

```python
>>> nums = [1, 2, 3, 4, 5]
>>> squares = (x*x for x in nums)
>>> squares
<generator object <genexpr> at 0x109207e60>
>>> for n in squares:
...     print(n)
...
1
4
9
16
25
```

与列表解析式不同，生成器表达式只能使用一次。因此，如果你遍历完生成器中的所有元素后，再次对生成器进行遍历，那么你将一无所获。

```python
>>> for n in squares:
...     print(n)
...
>>>
```

### 练习 6.14：函数参数中的生成器表达式

有时，生成器表达式会作为函数参数使用。虽然这看起来有点怪，但请尝试以下实验：

```python
>>> nums = [1,2,3,4,5]
>>> sum([x*x for x in nums])    # A list comprehension
55
>>> sum(x*x for x in nums)      # A generator expression
55
>>>
```
在上面的示例中，如果正在操作庞大的列表，那么使用生成器的第二个版本占用的内存会显著减少。

在  `portfolio.py` 文件中，我们执行了一些与列表相关的计算，请尝试将其替换为生成器表达式。

### 练习 6.15：简化代码

通常，对于简单的生成器函数，可以使用生成器表达式进行替换。例如，与其这样编写函数：

```python
def filter_symbols(rows, names):
    for row in rows:
        if row['name'] in names:
            yield row
```

不如使用生成器表达式进行替换：

```python
rows = (row for row in rows if row['name'] in names)
```

请修改 `ticker.py` 程序来适当的使用生成器表达式。

[目录](../Contents.md) \| [上一节 (6.3 生产者/消费者)](03_Producers_consumers.md) \| [下一节 (7 高级主题)](../07_Advanced_Topics/00_Overview.md)