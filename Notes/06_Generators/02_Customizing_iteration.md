[目录](../Contents.md) \| [上一节 (6.1 迭代协议)](01_Iteration_protocol.md) \| [下一节 (6.3 生产者/消费者)](03_Producers_consumers.md)

# 6.2 自定义迭代

本节探究如何使用生成器函数自定义迭代。

### 问题

假设你想要自定义迭代模式。

例如：倒数：

```python
>>> for x in countdown(10):
...   print(x, end=' ')
...
10 9 8 7 6 5 4 3 2 1
>>>
```

有一个j简单方法可以做到这一点。

### 生成器

生成器（generator）是定义了迭代的函数：

```python
def countdown(n):
    while n > 0:
        yield n
        n -= 1
```

示例:

```python
>>> for x in countdown(10):
...   print(x, end=' ')
...
10 9 8 7 6 5 4 3 2 1
>>>
```

任何使用了 `yield` 语句的函数称为生成器。

生成器函数的行为不同于普通于普通函数。调用生成器函数会创建一个生成器对象（generator object），而不是立即执行函数：

```python
def countdown(n):
    # Added a print statement
    print('Counting down from', n)
    while n > 0:
        yield n
        n -= 1
```

```python
>>> x = countdown(10)
# There is NO PRINT STATEMENT
>>> x
# x is a generator object
<generator object at 0x58490>
>>>
```

生成器函数只在 `__next__()` 方法被调用时才执行：

```python
>>> x = countdown(10)
>>> x
<generator object at 0x58490>
>>> x.__next__()
Counting down from 10
10
>>>
```

`yield` 生成一个值，但是挂起（suspend）函数执行。生成器函数会在下次调用 `__next__()` 方法时恢复（resume），

```python
>>> x.__next__()
9
>>> x.__next__()
8
```

当生成器返回最后一个值后，再次迭代将会触发一个错误（译注：StopIteration）。

```python
>>> x.__next__()
1
>>> x.__next__()
Traceback (most recent call last):
File "<stdin>", line 1, in ? StopIteration
>>>
```

*观察：生成器函数实现的协议与 for 语句在列表、元组、字典、文件上使用的底层协议相同。*

## 练习

### 练习 6.4：一个简单的生成器

如果想要自定义迭代，那么你应该始终考虑生成器函数。生成器函数非常容易编写——创建一个函数，执行所需的迭代逻辑，并使用 `yield` 发送一个值。

例如，创建一个在文件各行中查找匹配子串的生成器：

```python
>>> def filematch(filename, substr):
        with open(filename, 'r') as f:
            for line in f:
                if substr in line:
                    yield line

>>> for line in open('Data/portfolio.csv'):
        print(line, end='')

name,shares,price
"AA",100,32.20
"IBM",50,91.10
"CAT",150,83.44
"MSFT",200,51.23
"GE",95,40.37
"MSFT",50,65.10
"IBM",100,70.44
>>> for line in filematch('Data/portfolio.csv', 'IBM'):
        print(line, end='')

"IBM",50,91.10
"IBM",100,70.44
>>>
```

这是一种有趣的思想——你可以在函数中隐藏自定义的处理过程，并将该函数应用于 for  循环。下一个例子探究一种更不寻常的情况。

### 练习 6.5：监视流数据源

生成器可应用于监视实时数据源（如：日志文件，股票市场消息）。本部分，我们将对“使用生成器监视实时数据源”这一思想进行探索。首先，请严格遵循以下说明。

`Data/stocksim.py` 用来模仿股市数据，将实时数据不断的写入到 `Data/stocklog.csv` 文件。请打开一个独立的命令行窗口，进入到 `Data/` 目录，然后运行 `stocksim.py` 程序：

```bash
bash % python3 stocksim.py
```

如果你使用的是 Windows 系统，那么请找到 `stocksim.py` 文件，然后双击该文件运行。然后，让我们先把这个程序放到一边（让它一直在那运行），打开另外一个命令行窗口，查看正在被模拟程序（译注：`stocksim.py`）写入数据的 `Data/stocklog.csv` 文件（译注：如果使用的是 Linux 系统，那么可以进入到 Data 目录下，然后使用 `tail -f  stocklog.csv` 命令查看）。你应该可以看到每隔几秒新的文本行被添加到 `Data/stocklog.csv` 文件中。同样，让程序在后台运行——该程序会运行几个小时（对此不用担心）。

 `stocksim.py` 程序运行后，让我们编写一个程序来打开 `Data/stocklog.csv` 文件、移动到文件末尾、并查看新的输出。请在 Work 目录下创建 `follow.py` 文件，并把以下代码放入其中：

```python
# follow.py
import os
import time

f = open('Data/stocklog.csv')
f.seek(0, os.SEEK_END)   # Move file pointer 0 bytes from end of file

while True:
    line = f.readline()
    if line == '':
        time.sleep(0.1)   # Sleep briefly and retry
        continue
    fields = line.split(',')
    name = fields[0].strip('"')
    price = float(fields[1])
    change = float(fields[4])
    if change < 0:
        print(f'{name:>10s} {price:>10.2f} {change:>10.2f}')
```

运行  `follow.py ` 程序，你将会看到实时的股票报价（stock ticker）。 `follow.py ` 里的代码类似于 Unix 系统查看日志文件的  `tail -f` 命令。

注意事项：在本示例中，`readline()` 方法的使用与通常从文件中读取行的方式稍微有点不同（通常使用 `for` 循环）。在这种情况下，我们使用 `readline()`  来重复探测文件的末尾，以查看是否添加了新的数据（`readline()` 方法返回新的数据或者空字符串）。

### 练习 6.6：使用生成器生成数据

查看练习 6.5 中代码你会发现，代码的第一部分产生了几行数据，而 `while`  循环末尾的语句消费数据。生成器的一个主要特性是你可以将生成数据的代码移动到可重用的函数中。

请修改练习 6.5 的代码，以便通过生成器函数 `follow(filename)`  执行文件读取。请实现更改以便下面的代码能够工作：

```python
>>> for line in follow('Data/stocklog.csv'):
          print(line, end='')

... Should see lines of output produced here ...
```

请修改股票报价代码，使代码看起来像下面这样：


```python
if __name__ == '__main__':
    for line in follow('Data/stocklog.csv'):
        fields = line.split(',')
        name = fields[0].strip('"')
        price = float(fields[1])
        change = float(fields[4])
        if change < 0:
            print(f'{name:>10s} {price:>10.2f} {change:>10.2f}')
```

### 练习 6.7：查看股票投资组合

请修改 `follow.py` 程序，以便程序能够查看股票数据流，并打印股票投资组合中的那些股票的信息。示例：

```python
if __name__ == '__main__':
    import report

    portfolio = report.read_portfolio('Data/portfolio.csv')

    for line in follow('Data/stocklog.csv'):
        fields = line.split(',')
        name = fields[0].strip('"')
        price = float(fields[1])
        change = float(fields[4])
        if name in portfolio:
            print(f'{name:>10s} {price:>10.2f} {change:>10.2f}')
```

注意事项：要想这段代码能够运行， `Portfolio` 类必须支持 `in` 运算符。请参阅 [练习 6.3 ](01_Iteration_protocol) ，确保 `Portfolio` 类实现了 `__contains__()` 运算符。

### 讨论

在这里，你将一个有趣的迭代模式（在文件末尾读取行）移动到函数中。`follow()`函数现在是可以在任何程序中使用的完全通用的实用程序。例如，你可以使用 `follow()` 函数查看服务器日志、调试日志、其它类似的数据源。

[目录](../Contents.md) \| [上一节 (6.1 迭代协议)](01_Iteration_protocol.md) \| [下一节 (6.3 生产者/消费者)](03_Producers_consumers.md)