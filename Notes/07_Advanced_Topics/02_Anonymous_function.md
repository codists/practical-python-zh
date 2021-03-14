[目录](../Contents.md) \| [上一节 (7.1可变参数)](01_Variable_arguments.md) \| [下一节 (7.3 返回函数)](03_Returning_functions.md)

# 7.2 匿名函数和 Lambda

### 再探列表排序

列表可以使用 `sort` 方法进行*原地（in-place）排序*：

```python
s = [10,1,7,3]
s.sort() # s = [1,3,7,10]
```

也可以进行降序排序（译注：原文使用的是  `sort in reverse order`，很多时候翻译为倒序排序或者反向排序，个人认为容易引起歧义，故翻译为降序排序）：

```python
s = [10,1,7,3]
s.sort(reverse=True) # s = [10,7,3,1]
```

对单个列表排序似乎非常简单，但是，如果对字典列表排序，那么我们怎么做呢？

```python
[{'name': 'AA', 'price': 32.2, 'shares': 100},
{'name': 'IBM', 'price': 91.1, 'shares': 50},
{'name': 'CAT', 'price': 83.44, 'shares': 150},
{'name': 'MSFT', 'price': 51.23, 'shares': 200},
{'name': 'GE', 'price': 40.37, 'shares': 95},
{'name': 'MSFT', 'price': 65.1, 'shares': 50},
{'name': 'IBM', 'price': 70.44, 'shares': 100}]
```

通过什么样的规则进行排序呢？

你可以使用键函数（key function）指导排序。键函数是这样的一种函数：接受一个字典并且返回一个用于排序的值。

```python
def stock_name(s):
    return s['name']

portfolio.sort(key=stock_name)
```

排序结果如下：

```python
# Check how the dictionaries are sorted by the `name` key
[
  {'name': 'AA', 'price': 32.2, 'shares': 100},
  {'name': 'CAT', 'price': 83.44, 'shares': 150},
  {'name': 'GE', 'price': 40.37, 'shares': 95},
  {'name': 'IBM', 'price': 91.1, 'shares': 50},
  {'name': 'IBM', 'price': 70.44, 'shares': 100},
  {'name': 'MSFT', 'price': 51.23, 'shares': 200},
  {'name': 'MSFT', 'price': 65.1, 'shares': 50}
]
```

### 回调函数

在上面的示例中，键函数是一个回调函数（callback function）。`sort()` 方法调用了 `stock_name（）`函数，然后 `stock_name（）` 函数返回一个值给 `sort()` 方法。通常，回调函数是一个简短的单行函数，只用于一个操作。程序员经常会要求提供一种快捷方法来指定这种额外的处理。

### Lambda： 匿名函数

在之前的排序示例中，我们可以使用 lambda， 而不是创建（具名）函数。

```python
portfolio.sort(key=lambda s: s['name'])
```

上面的代码创建了一个匿名（*unnamed*）函数用来计算单个表达式，比初始代码短很多。

```python
def stock_name(s):
    return s['name']

portfolio.sort(key=stock_name)

# vs lambda
portfolio.sort(key=lambda s: s['name'])
```

### 使用 lambda

* lambda 是受到严格限制的。
* 只允许一个表达式。
* 没有 `if`，`while` 之类的语句。
* 多和其它函数一起使用，类似于 `sort()` 方法。

## 练习

读取股票投资组合数据，并将数据转换为列表：

```python
>>> import report
>>> portfolio = list(report.read_portfolio('Data/portfolio.csv'))
>>> for s in portfolio:
        print(s)

Stock('AA', 100, 32.2)
Stock('IBM', 50, 91.1)
Stock('CAT', 150, 83.44)
Stock('MSFT', 200, 51.23)
Stock('GE', 95, 40.37)
Stock('MSFT', 50, 65.1)
Stock('IBM', 100, 70.44)
>>>
```

### 练习 7.5：对字段进行排序

请尝试下面的语句，这些语句按股票名称的字母顺序对投资组合数据进行排序。

```python
>>> def stock_name(s):
       return s.name

>>> portfolio.sort(key=stock_name)
>>> for s in portfolio:
           print(s)

... inspect the result ...
>>>
```

在此部分，`stock_name()` 函数从 `portfolio` 列表的单个条目中提取股票名称。`sort()` 方法使用 `stock_name()` 函数的返回值进行比较并排序。

### 练习 7.6：使用 lambda 对字段进行排序

请尝试使用 `lambda` 表达式，按股份数目对投资组合数据进行排序：

```python
>>> portfolio.sort(key=lambda s: s.shares)
>>> for s in portfolio:
        print(s)

... inspect the result ...
>>>
```

尝试按每只股票的价格对投资组合数据进行排序：

```python
>>> portfolio.sort(key=lambda s: s.price)
>>> for s in portfolio:
        print(s)

... inspect the result ...
>>>
```

注意事项：`lambda` 是一种非常有用的快捷方式，因为它让你直接在 `sort()` 方法的调用中定义一个特殊的处理函数，而不必事先定义一个单独的函数。

[目录](../Contents.md) \| [上一节 (7.1可变参数)](01_Variable_arguments.md) \| [下一节 (7.3 返回函数)](03_Returning_functions.md)