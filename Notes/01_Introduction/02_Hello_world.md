[目录](../Contents.md) \| [上一节 (1.1 Python)](01_Python.md) \| [下一节 (1.3 数字)](03_Numbers.md)

# 1.2 第一个程序

本节讨论有关如何创建一个程序、运行解释器和调试的基础知识。

### 运行 Python

Python 程序始终在解释器中运行。

解释器是一个“基于控制台”的应用程序，通常从命令行 shell 启动。

```bash
python3
Python 3.6.1 (v3.6.1:69c0db5050, Mar 21 2017, 01:21:04)
[GCC 4.2.1 (Apple Inc. build 5666) (dot 3)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>>
```

老练的程序员以这种方式使用解释器时通常都没有问题，但对于初学者来说，这种方式并不是那么友好。您可能会使用一个为 Python 提供不同接口的环境，但是学会如何运行 Python 终端仍然是一项有必要了解的技能。

### 交互模式

当启动 Python 时，可以进入一种交互的模式，在这种模式下，可以进行编程实验。

如果输入代码完毕，这些代码将会马上运行。没有 edit/compile/run/debug 等流程。

```python
>>> print('hello world')
hello world
>>> 37*42
1554
>>> for i in range(5):
...     print(i)
...
0
1
2
3
4
>>>
```

这就是所谓的“读取-求值-输出-循环”（译者注：简写为 REPL，也有人译为 “交互式解释器”，正像 Python 这个词一样，为了更好地理解，这里不翻译），对于代码调试和探索非常有用。

**暂停**：如果您不知道如何与 Python 进行交互，先停止您正在做的事情，然后弄清楚如何与 Python 进行交互。如果您正在使用的是集成开发环境（IDE）,它可能隐藏在一个菜单选项或者其它窗口后面。本课程的许多部分都假定您能与解释器进行交互。

让我们仔细看看 REPL 的元素：

- `>>>` 提示符用于开始一个新的语句。
- `...` 提示符用于继续一个语句，输入一个空行表示全部输入结束，并运行所输入的代码。

`...` 提示符可能会显示，也可能不会显示，这取决于使用的环境。对于本课程，将 `...` 提示符显示为空白，以便于剪切、粘贴示例代码。

下划线 `_` 保存最后的结果。

```python
>>> 37 * 42
1554
>>> _ * 2
3108
>>> _ + 50
3158
>>>
```

仅在交互模式下如此（指下划线 `_` 保存最后的结果)，永远不要在程序中使用这种方式。

### 创建程序

程序写在 `.py` 文件中。

```python
# hello.py
print('hello world')
```

你可以使用你最喜欢的文本编辑器来创建这些 `.py` 文件。

### 运行程序

要执行程序，请在终端中使用  `python` 命令执行它。示例，在 Unix 系统的命令行中是这样执行一个 Python 程序的：

示例，在 Unix 系统的命令行中执行程序：

```bash
bash % python hello.py
hello world
bash %
```

或者，从 Windows shell 中执行一个 Python 程序：

```
C:\SomeFolder>hello.py
hello world

C:\SomeFolder>c:\python36\python hello.py
hello world
```

注意：在 Windows 系统上，可能需要指定 Python 解释器的完整路径，例如 `c:\python36\python`。但是，如果 Python 是以通常的方式安装的，可能只需要输入程序文件名即可，例如 `hello.py`。

### 示例程序

让我们解决以下的问题：

>一天早上，您走出去，在芝加哥的西尔斯大厦旁的人行道上放 1 美元钞票。从那以后的每一天，您放的钞票都是前一天的两倍。这叠钞票需要多长时间才能超过塔的高度。

这是答案:

```python
# sears.py
bill_thickness = 0.11 * 0.001 # Meters (0.11 mm)
sears_height = 442 # Height (meters)
num_bills = 1
day = 1

while num_bills * bill_thickness < sears_height:
    print(day, num_bills, num_bills * bill_thickness)
    day = day + 1
    num_bills = num_bills * 2

print('Number of days', day)
print('Number of bills', num_bills)
print('Final height', num_bills * bill_thickness)
```

当执行 `sears.py` 程序的时候，会得到下面的输出：

```bash
bash % python3 sears.py
1 1 0.00011
2 2 0.00022
3 4 0.00044
4 8 0.00088
5 16 0.00176
6 32 0.00352
...
21 1048576 115.34336
22 2097152 230.68672
Number of days 23 
Number of bills 4194304 
Final height 461.37344
```

使用此程序作为指南，您可以学到许多重要的关于 Python 的核心概念。

### 语句

一个 Python  程序是由一系列的语句组成。

```python
a = 3 + 4
b = a * 2
print(b)
```

每个语句以换行符终止。语句会一个接一个地执行，直到控制流达到文件的末尾。

### 注释

注释是不会被解释器执行的文本。

```python
a = 3 + 4
# This is a comment
b = a * 2
print(b)
```

注释由 `#` 号表示，并延伸到行的末尾。

### 变量

变量是一个值的名称。可以使用从 a 到 z 的字母（小写和大写都可以）表示，也可以使用下划线 `_` 符号表示。数字也可以作为变量名的一部分，但第一个字符除外。

```python
height = 442 # valid
_height = 442 # valid
height2 = 442 # valid
2height = 442 # invalid
```

### 类型

变量不需要使用值的类型进行声明。类型与右侧的值关联，而不是变量名。

```python
height = 442           # An integer
height = 442.0         # Floating point
height = 'Really tall' # A string
```

Python 是动态语言。当程序执行时，变量的感知“类型”可能会发生改变，这取决于赋给变量的当前值。

### 大小写敏感

Python 语言是大小写敏感的（即区分大小写），大写字母和小写字母被认为是不同的字母。下面这些都是不同的变量：

```python
name = 'Jake'
Name = 'Elwood'
NAME = 'Guido'
```

Python 语言自带的语句总是小写：

```python
while x < 0:   # OK
WHILE x < 0:   # ERROR
```

### 循环

  `while` 语句执行一个循环：

```python
while num_bills * bill_thickness < sears_height:
    print(day, num_bills, num_bills * bill_thickness)
    day = day + 1
    num_bills = num_bills * 2

print('Number of days', day)
```

只要 `while` 之后的表达式的值是 `true` ，`while` 下面缩进的语句就会被执行。

### 缩进

缩进用于表示放在一起的语句组。考虑前面的示例：

```python
while num_bills * bill_thickness < sears_height:
    print(day, num_bills, num_bills * bill_thickness)
    day = day + 1
    num_bills = num_bills * 2

print('Number of days', day)
```

缩进将下面的语句分组为一个重复的操作：

```python
    print(day, num_bills, num_bills * bill_thickness)
    day = day + 1
    num_bills = num_bills * 2
```

因为在末尾的 `print()` 语句（即 `print('Number of days', day)`）没有缩进，所以不属于循环。空行只是为了可读性，并不影响程序的执行。

### 缩进的最佳实践

- 使用空格（spaces）而不是制表符（tabs）。
- 每级使用四个空格。
- 使用支持 Python 的编辑器。

Python 唯一的要求就是同一代码块内的缩进必须一致。例如，下面的缩进是错误的：

```python
while num_bills * bill_thickness < sears_height:
    print(day, num_bills, num_bills * bill_thickness)
        day = day + 1 # ERROR
    num_bills = num_bills * 2
```

### 条件语句

`if` 语句用来执行一个条件：

```python
if a > b:
    print('Computer says no')
else:
    print('Computer says yes')
```

为了检查多个条件，可以使用 `elif`来添加额外的检查。

```python
if a > b:
    print('Computer says no')
elif a == b:
    print('Computer says yes')
else:
    print('Computer says maybe')
```

### 输出

 `print` 函数将传入的参数值输出成一行文本。

```python
print('Hello world!') # Prints the text 'Hello world!'
```

也可以将变量进行输出。输出的文本将是变量的值，而不是变量的名字。

```python
x = 100
print(x) # Prints the text '100'
```

如果将两个以上的值传递给 `print` 函数，它们在输出的时候被空格分开。

```python
name = 'Jake'
print('My name is', name) # Print the text 'My name is Jake'
```

`print()` 函数总是将换行符放在最后。

```python
print('Hello')
print('My name is', 'Jake')
```

上面的代码会输出如下的内容:

```code
Hello
My name is Jake
```

额外的换行符可以被抑制:

```python
print('Hello', end=' ')
print('My name is', 'Jake')
```

上面的代码将会输出如下内容:

```code
Hello My name is Jake
```

### 用户输入

要读取一行类型化的用户输入，可以使用 `input()` 函数：

```python
name = input('Enter your name:')
print('Your name is', name)
```

`input()` 函数向用户输出一句提示，并且返回他们的响应。

这对于一个简短的程序、学习练习或者简单的调试是非常有用的。

但这并未广泛应用于实际的程序中。

### pass 语句

有时候需要指定一个空的代码块，那么可以使用 `pass` 关键字。

```python
if a > b:
    pass
else:
    print('Computer says false')
```

`pass` 语句也称为 “无操作” 语句，它什么也不做。它用作语句的占位符，可能以后会被添加。

## 练习

这是需要创建 Python 文件并且运行这个文件的第一组练习。从现在开始，假定你正在编辑的文件位于`practical-python/Work/` 目录。为了帮您找到合适的 位置，已经创建了许多具有合适的文件名的空的启动文件。请找到在第一个练习中使用的 `Work/bounce.py` 文件。

### 练习 1.5: 弹跳球

一个橡胶球从 100 米高的地方落下来，球每次撞到地面时弹到原来高度的 3/5 。编写一个程序 `bounce.py` ，输出一张表格，显示前 10 次反弹的高度。

程序生成的表格大概像下面这样：

```code
1 60.0
2 36.0
3 21.599999999999998
4 12.959999999999999
5 7.775999999999999
6 4.6655999999999995
7 2.7993599999999996
8 1.6796159999999998
9 1.0077695999999998
10 0.6046617599999998
```

注意：如果使用 round() 函数，可以使输出稍微简洁一些。请尝试使用 round()  函数将输出四舍五入到 小数点后 4 位。

```code
1 60.0
2 36.0
3 21.6
4 12.96
5 7.776
6 4.6656
7 2.7994
8 1.6796
9 1.0078
10 0.6047
```

### 练习 1.6: 调试

下面的代码片段包含来自于**西尔斯大厦问题**的代码，它还有一个 bug：

```python
# sears.py

bill_thickness = 0.11 * 0.001    # Meters (0.11 mm)
sears_height   = 442             # Height (meters)
num_bills      = 1
day            = 1

while num_bills * bill_thickness < sears_height:
    print(day, num_bills, num_bills * bill_thickness)
    day = days + 1
    num_bills = num_bills * 2

print('Number of days', day)
print('Number of bills', num_bills)
print('Final height', num_bills * bill_thickness)
```

将上面显示的代码复制并粘贴到一个名为 `sears.py`的新程序中。当执行 `sears.py` 里的代码时，将会收到一条显示程序为什么崩溃的错误信息。错误信息如下所示：

```code
Traceback (most recent call last):
  File "sears.py", line 10, in <module>
    day = days + 1
NameError: name 'days' is not defined
```

读取错误信息是 Python 代码的重要组成部分。如果程序崩溃，则回溯消息的最后一行是程序为什么崩溃的实际原因。在此（`NameError: name 'days' is not defined`）之上，你应该可以看到源代码片段（ `day = days + 1`）、可识别的文件名（ `sears.py`）和错误代码的行号（ `line 10`）。

* 哪行是错误的？
* 错误是什么？
* 解决错误。
* 成功运行程序。

[目录](../Contents.md) \| [上一节 (1.1 Python)](01_Python.md) \| [下一节 (1.3 数字)](03_Numbers.md)