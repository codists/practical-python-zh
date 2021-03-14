[目录](../Contents.md) \| [上一节 (6.4 生成器表达式)](../06_Generators/04_More_generators.md) \| [下一节 (7.2 匿名函数)](02_Anonymous_function.md)

# 7.1 可变参数

本节介绍可变（variadic）参数。有时，可变参数使用 `*args` 和 `**kwargs` 进行表示。

### 可变位置参数（`*args`）

如果一个函数接受任意数量的（位置）参数，那么我们称该函数使用了可变参数（variable arguments）。示例：

```python
def f(x, *args):
    ...
```

函数调用：

```python
f(1,2,3,4,5)
```

额外的参数作为元组进行传递：

```python
def f(x, *args):
    # x -> 1
    # args -> (2,3,4,5)
```

### 可变关键字参数（`**kwargs`）

一个函数也可以接受任意数量的关键字参数。示例：

```python
def f(x, y, **kwargs):
    ...
```

函数调用：

```python
f(2, 3, flag=True, mode='fast', header='debug')
```

额外的参数作为字典进行传递：

```python
def f(x, y, **kwargs):
    # x -> 2
    # y -> 3
    # kwargs -> { 'flag': True, 'mode': 'fast', 'header': 'debug' }
```

### 可变位置参数与可变关键字参数结合使用

一个函数可以同时接受可变非关键字参数和可变关键字参数。

```python
def f(*args, **kwargs):
    ...
```

函数调用:

```python
f(2, 3, flag=True, mode='fast', header='debug')
```

这些参数被分为位置参数和关键字参数两部分。

```python
def f(*args, **kwargs):
    # args = (2, 3)
    # kwargs -> { 'flag': True, 'mode': 'fast', 'header': 'debug' }
    ...
```

上述函数接受任意数量的位置参数和关键字参数。当编写包装器（wrappers）或要将参数传递给另一个函数时使用。

### 传递元组和字典

元组可扩展为可变参数:

```python
numbers = (2,3,4)
f(1, *numbers)      # Same as f(1,2,3,4)
```

字典也可以扩展为关键字参数:

```python
options = {
    'color' : 'red',
    'delimiter' : ',',
    'width' : 400
}
f(data, **options)
# Same as f(data, color='red', delimiter=',', width=400)
```

## 练习

### 练习 7.1: 可变参数的简单示例

尝试定义下列函数：

```python
>>> def avg(x,*more):
        return float(x+sum(more))/(1+len(more))

>>> avg(10,11)
10.5
>>> avg(3,4,5)
4.0
>>> avg(1,2,3,4,5,6)
3.5
>>>
```

请注意 `*more` 是如何收集其它所有参数的。　

### 练习 7.2：将元组和字典作为参数进行传递

假设你从文件中读取数据，并获得一个元组。例如：

```
>>> data = ('GOOG', 100, 490.1)
>>>
```

现在，假设你想根据上面的数据创建一个 `Stock` 对象。如果你直接传 `data` ，那就行不通了：

```
>>> from stock import Stock
>>> s = Stock(data)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
TypeError: __init__() takes exactly 4 arguments (2 given)
>>>
```

这个问题很容易解决，直接使用 `*data` 即可。试试看：

```python
>>> s = Stock(*data)
>>> s
Stock('GOOG', 100, 490.1)
>>>
```

如果你拥有的是一个字典，那么你可以改用 `**`。示例：

```python
>>> data = { 'name': 'GOOG', 'shares': 100, 'price': 490.1 }
>>> s = Stock(**data)
Stock('GOOG', 100, 490.1)
>>>
```

### 练习 7.3：创建实例列表

在  `report.py`  程序中，我们使用如下代码创建了一个实例列表：

```python
def read_portfolio(filename):
    '''
    Read a stock portfolio file into a list of dictionaries with keys
    name, shares, and price.
    '''
    with open(filename) as lines:
        portdicts = fileparse.parse_csv(lines,
                               select=['name','shares','price'],
                               types=[str,int,float])

    portfolio = [ Stock(d['name'], d['shares'], d['price'])
                  for d in portdicts ]
    return Portfolio(portfolio)
```

我们可以改用 `Stock(**d)` 来简化代码。请完成修改。

### 练习 7.4：参数传递

`fileparse.parse_csv()` 函数具有一些选项，用于更改文件分隔符和错误报告。也许你会想要这些选择暴露给上面的 `read_portfolio()` 函数。请完成修改：

```
def read_portfolio(filename, **opts):
    '''
    Read a stock portfolio file into a list of dictionaries with keys
    name, shares, and price.
    '''
    with open(filename) as lines:
        portdicts = fileparse.parse_csv(lines,
                                        select=['name','shares','price'],
                                        types=[str,int,float],
                                        **opts)

    portfolio = [ Stock(**d) for d in portdicts ]
    return Portfolio(portfolio)
```

修改完成后，尝试阅读读取一些带有错误的文件：

```python
>>> import report
>>> port = report.read_portfolio('Data/missing.csv')
Row 4: Couldn't convert ['MSFT', '', '51.23']
Row 4: Reason invalid literal for int() with base 10: ''
Row 7: Couldn't convert ['IBM', '', '70.44']
Row 7: Reason invalid literal for int() with base 10: ''
>>>
```

现在，尝试隐藏错误：

```python
>>> import report
>>> port = report.read_portfolio('Data/missing.csv', silence_errors=True)
>>>
```

[目录](../Contents.md) \| [上一节 (6.4 生成器表达式)](../06_Generators/04_More_generators.md) \| [下一节 (7.2 匿名函数)](02_Anonymous_function.md)