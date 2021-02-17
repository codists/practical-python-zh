[目录](../Contents.md) \| [上一节 (1.2 第一个程序)](02_Hello_world.md) \| [下一节 (1.4 字符串)](04_Strings.md)

# 1.3 数字

本节讨论数学计算。

### 数字类型

Python 有 4 种类型的数字：

* 布尔型
* 整型
* 浮点型
* 复数（虚数）

### 布尔型（bool） 

布尔型数字有两个值：`True`，`False`。

```python
a = True
b = False
```

在数值上，它们被计算成值为 `1`，`0` 的数。

```python
c = 4 + True # 5
d = False
if d == 0:
    print('d is False')
```

但是，不要像上面这样写代码，这会很奇怪。

### 整型（int）

任意大小的有符号值，或者以任意数为基数的有符号值。

```python
a = 37
b = -299392993727716627377128481812241231
c = 0x7fa8      # 十六进制（Hexadecimal）
d = 0o253       # 八进制（Octal）
e = 0b10001111  # 二进制（Binary）
```

常用操作：

```
x + y      加（Add）
x - y      减（Subtract）
x * y      乘（Multiply）
x / y      除（Divide，生成一个浮点数） 
x // y     地板除（Floor Divide，生成一个整数）
x % y      取模（Modulo，余数(remainder)） 
x ** y     幂运算（Power）
x << n     左移位（Bit shift left）
x >> n     右移位（Bit shift right）
x & y      按位与（Bit-wise AND）
x | y      按位或（Bit-wise OR）
x ^ y      按位异或（Bit-wise XOR）
~x         取反（Bit-wise NOT）
abs(x)     绝对值（Absolute value）
```

### 浮点型（float）

使用十进制或者指数表示法来指定浮点数的值：

```python
a = 37.45
b = 4e5 # 4 x 10**5 or 400,000
c = -1.345e-10
```

使用浮点数表示 [IEEE 754](https://en.wikipedia.org/wiki/IEEE_754) 标准的双精度。这与 C 语言中的 `double` 类型相同。

> 17 digits of precision  
> Exponent from -308 to 308

请注意，当代表小数时，浮点数是不精确的。

```python
>>> a = 2.1 + 4.2
>>> a == 6.3
False
>>> a
6.300000000000001
>>>
```

这不是 Python 的问题，而是 CPU 硬件上底层浮点硬件的问题。

常用操作：

```
x + y      加（Add）
x - y      减（Subtract）
x * y      乘（Multiply）
x / y      除（Divide）
x // y     地板除（Floor Divide）
x % y      取模（Modulo）
x ** y     幂运算（Power）
abs(x)     绝对值（Absolute Value）
```

除了按位运算符之外，浮点数的运算符与整数的运算符是一样的。

其它数学函数可以在 `math` 中找到。

```python
import math
a = math.sqrt(x)
b = math.sin(x)
c = math.cos(x)
d = math.tan(x)
e = math.log(x)
```

### 比较

下面的比较/关系运算符可以应用于数字：

```
x < y      小于（Less than）
x <= y     小于等于（Less than or equal）
x > y      大于（Greater than）
x >= y     大于等于（Greater than or equal）
x == y     等于（Equal to）
x != y     不等于（Not equal to）
```

可以使用 `and`， `or`，`not` 组成更复杂的布尔表达式。

这里有一些例子：

```python
if b >= a and b <= c:
    print('b is between a and c')

if not (b < a or b > c):
    print('b is still between a and c')
```

### 转换数字

类型名可以被用来将其它数据转换为数字。

```python
a = int(x)    # Convert x to integer
b = float(x)  # Convert x to float
```

试试下面这些操作：

```python
>>> a = 3.14159
>>> int(a)
3
>>> b = '3.14159' # It also works with strings containing numbers
>>> float(b)
3.14159
>>>
```

## 练习

提醒：这些练习假定你正在 [`practical-python/Work`](../../Work) 目录中操作，具体在 [`mortgage.py`](../../Work/mortgage.py) 文件。

### 练习 1.7：戴夫的抵押贷款

戴夫决定从 Guido 的抵押贷款、股票投资和比特币交易公司获得 50 万美元的 30 年期固定利率抵押贷款。利率是  5%，每月还款额是 2684.11 美元。

下面这个程序用于计算戴夫在抵押期内需要支付的总金额：

```python
# mortgage.py

principal = 500000.0
rate = 0.05
payment = 2684.11
total_paid = 0.0

while principal > 0:
    principal = principal * (1+rate/12) - payment
    total_paid = total_paid + payment

print('Total paid', total_paid)
```

输入该程序并执行，你应该会得到答案为 `966,279.6`。

### 练习 1.8：额外付款

假设戴夫在抵押期的前 12 个月每月额外支付 1000 美元。

修改程序以包含这部分额外的付款，并且输出已支付的总金额以及所需的月数。

当你执行这个新程序时，它应该报告 342 个月的总付款额是 `929,965.62` 。

### 练习 1.9：制作一个额外的付款计算器

修改程序，以便可以更一般地处理额外的付款信息。做到这一点，以便用户可以设置下面这些变量：

```python
extra_payment_start_month = 61
extra_payment_end_month = 108
extra_payment = 1000
```

使程序查看这些变量，并适当地计算总付款额 。如果戴夫从抵押期的第五年开始，每月额外支付 1000 每月并支付 4 年，那么他将要支付多少？

### 练习 1.10：制作表格

修改程序，使其显示迄今为止支付的月数，支付的总金额和剩余的本金。输出看起来应该像下面这样：

```bash
1 2684.11 499399.22
2 5368.22 498795.94
3 8052.33 498190.15
4 10736.44 497581.83
5 13420.55 496970.98
...
308 874705.88 3478.83
309 877389.99 809.21
310 880074.1 -1871.53
Total paid 880074.1
Months 310
```

### 练习 1.11：奖金

使用该程序时，请修复程序以纠正发生在上个月的超额支付。

### 练习 1.12：一个谜

`int()` 函数和 `float()` 函数可以将其它类型的数据转换为数字类型。示例：

```python
>>> int("123")
123
>>> float("1.23")
1.23
>>>
```

考虑到这一点，你能否解释下面这种行为？

```python
>>> bool("False")
True
>>>
```

[目录](../Contents.md) \| [上一节 (1.2 第一个程序)](02_Hello_world.md) \| [下一节 (1.4 字符串)](04_Strings.md)

