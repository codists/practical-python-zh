[目录](../Contents.md) \| [上一节 (2.2 容器)](02_Containers.md) \| [下一节 (2.4 序列)](04_Sequences.md)

# 2.3 格式化

虽然本节稍微有点离题，但是当处理数据时，通常想要生成结构化的输出（如表格）。示例：

```code
      Name      Shares        Price
----------  ----------  -----------
        AA         100        32.20
       IBM          50        91.10
       CAT         150        83.44
      MSFT         200        51.23
        GE          95        40.37
      MSFT          50        65.10
       IBM         100        70.44
```

### 字符串格式化

在 Python 3.6+ 中，格式化字符串的一种方法是使用 `f-strings`：

```python
>>> name = 'IBM'
>>> shares = 100
>>> price = 91.1
>>> f'{name:>10s} {shares:>10d} {price:>10.2f}'
'       IBM        100      91.10'
>>>
```

`{expression:format}` 部分会被取代。

`f-strings` 通常和 `print()` 函数一起使用：

```python
print(f'{name:>10s} {shares:>10d} {price:>10.2f}')
```

### 格式码

格式码（在 `{}` 内 `:` 之后）与 C 语言的 `printf()` 函数类似。常见格式码包括：

```code
d       Decimal integer
b       Binary integer
x       Hexadecimal integer
f       Float as [-]m.dddddd
e       Float as [-]m.dddddde+-xx
g       Float, but selective use of E notation
s       String
c       Character (from integer)
```

常见的修饰符可调整字段宽度和数的精度。这是部分内容：

```code
:>10d   Integer right aligned in 10-character field
:<10d   Integer left aligned in 10-character field
:^10d   Integer centered in 10-character field
:0.2f   Float with 2 digit precision
```

### 字典格式化

可以使用字符串的 `format_map()` 方法将字符串格式化应用于值的字典：

```python
>>> s = {
    'name': 'IBM',
    'shares': 100,
    'price': 91.1
}
>>> '{name:>10s} {shares:10d} {price:10.2f}'.format_map(s)
'       IBM        100      91.10'
>>>
```

虽然 `format_map()` 和 `f-strings` 使用相同的格式码，但是是从提供的字典中获取值。

### format()方法 

有一个 `format()` 方法可以将格式化应用于参数或者关键字参数：

```python
>>> '{name:>10s} {shares:10d} {price:10.2f}'.format(name='IBM', shares=100, price=91.1)
'       IBM        100      91.10'
>>> '{:10s} {:10d} {:10.2f}'.format('IBM', 100, 91.1)
'       IBM        100      91.10'
>>>
```

坦白说，`format()` 方法稍微有点冗长，我更倾向于使用 f-strings。

### C 风格的格式化

也可以使用格式化操作符 `%` ：

```python
>>> 'The value is %d' % 3
'The value is 3'
>>> '%5d %-5d %10d' % (3,4,5)
'    3 4              5'
>>> '%0.2f' % (3.1415926,)
'3.14'
```

这要求右边是一个单项或者元组，格式码也是模仿 C 语言 `printf()` 函数的。

*注意：这是字节字符串上唯一可用的格式化方法。*

```python
>>> b'%s has %n messages' % (b'Dave', 37)
b'Dave has 37 messages'
>>>
```

## 练习

### 练习 2.8：如何格式化数字

打印数字常见的一个问题就是指定数字的小数位数。其中的一种解决方法就是使用 f-strings。请尝试以下示例：

```python
>>> value = 42863.1
>>> print(value)
42863.1
>>> print(f'{value:0.4f}')
42863.1000
>>> print(f'{value:>16.2f}')
        42863.10
>>> print(f'{value:<16.2f}')
42863.10
>>> print(f'{value:*>16,.2f}')
*******42,863.10
>>>
```

有关 f-strings 使用的格式码的完整文档在 [这里](https://docs.python.org/3/library/string.html#format-specification-mini-language) 可以找到。有时，也使用字符串操作符 `%`  执行格式化。

```python
>>> print('%0.4f' % value)
42863.1000
>>> print('%16.2f' % value)
        42863.10
>>>
```

与操作符 `%` 使用的各种格式码有关的文档可以在 [这里](https://docs.python.org/3/library/stdtypes.html#printf-style-string-formatting) 找到。尽管它通常与 `print()` 函数一起使用，但是字符串格式化与打印无关。如果要保存格式化的字符串，把它赋值给变量即可。

```python
>>> f = '%0.4f' % value
>>> f
'42863.1000'
>>>
```

### 练习 2.9：收集数据

在练习 2.7 中，编写了一个用于计算股票投资盈亏的程序 [`report.py`](../../Solutions/2_7/report.py)。在本练习中，需要修改这个程序来生成如下表格：

```
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84
```

在此表格中，"Price" 是当前股价，"Change" 是当前股价与原始购买股价的差。

为了生成上述表格，首先需要收集表中展示的所有数据。编写 `make_report()` 函数，以股票列表和价格字典作为输入，并返回一个包含上表中所有行的元组列表。

把 `make_report()` 函数添加到 `report.py` 文件中。如果交互式地执行该函数，则应该按以下步骤进行：

```python
>>> portfolio = read_portfolio('Data/portfolio.csv')
>>> prices = read_prices('Data/prices.csv')
>>> report = make_report(portfolio, prices)
>>> for r in report:
        print(r)

('AA', 100, 9.22, -22.980000000000004)
('IBM', 50, 106.28, 15.180000000000007)
('CAT', 150, 35.46, -47.98)
('MSFT', 200, 20.89, -30.339999999999996)
('GE', 95, 13.48, -26.889999999999997)
...
>>>
```

### 练习 2.10：打印格式化的表格

重做练习 2.9 中的 for 循环，但是请更改打印语句以格式化元组。

```python
>>> for r in report:
        print('%10s %10d %10.2f %10.2f' % r)

          AA        100       9.22     -22.98
         IBM         50     106.28      15.18
         CAT        150      35.46     -47.98
        MSFT        200      20.89     -30.34
...
>>>
```

也可以使用 f-strings 扩展值。例如：

```python
>>> for name, shares, price, change in report:
        print(f'{name:>10s} {shares:>10d} {price:>10.2f} {change:>10.2f}')

          AA        100       9.22     -22.98
         IBM         50     106.28      15.18
         CAT        150      35.46     -47.98
        MSFT        200      20.89     -30.34
...
>>>
```

把上面的语句添加到  `report.py` 程序中，让程序获取`make_report()` 的输出，并打印如打印如上图所示的格式化的表。

### 练习 2.11：添加标题

假定有一个像下面这样的标题名称元组：

```python
headers = ('Name', 'Shares', 'Price', 'Change')
```

把上面的标题元组代码添加到程序中，并且创建一个字符串，每个标题向右对齐并且宽度是10，每个字段使用单个空格分隔。

```python
'      Name     Shares      Price      Change'
```

编写在标题和数据之间创建分隔字符串的代码。分隔字符串指每个字段名下的一串下划线（"-"）字符。例如：

```python
'---------- ---------- ---------- -----------'
```

当完成后，程序应生成本节顶部所示的表。

```
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84
```

### 练习 2.12：格式化挑战

如何修改代码使得价格包括货币符号（$），并且像下面这样输出：

```
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100      $9.22     -22.98
       IBM         50    $106.28      15.18
       CAT        150     $35.46     -47.98
      MSFT        200     $20.89     -30.34
        GE         95     $13.48     -26.89
      MSFT         50     $20.89     -44.21
       IBM        100    $106.28      35.84
```

[目录](../Contents.md) \| [上一节 (2.2 容器)](02_Containers.md) \| [下一节 (2.4 序列)](04_Sequences.md)