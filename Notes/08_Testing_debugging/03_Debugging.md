[目录](../Contents.md) \| [上一节 (8.2 日志)](02_Logging.md) \| [下一节 (9 包)](../09_Packages/00_Overview.md)

# 8.3 调试

### 调试建议

假设程序崩溃了：

```bash
bash % python3 blah.py
Traceback (most recent call last):
  File "blah.py", line 13, in ?
    foo()
  File "blah.py", line 10, in foo
    bar()
  File "blah.py", line 7, in bar
    spam()
  File "blah.py", 4, in spam
    line x.append(3)
AttributeError: 'int' object has no attribute 'append'
```

那么现在该怎么办呢？

### 阅读回溯信息

最后一行是程序崩溃的具体原因：

```bash
bash % python3 blah.py
Traceback (most recent call last):
  File "blah.py", line 13, in ?
    foo()
  File "blah.py", line 10, in foo
    bar()
  File "blah.py", line 7, in bar
    spam()
  File "blah.py", 4, in spam
    line x.append(3)
# Cause of the crash
AttributeError: 'int' object has no attribute 'append'
```

不过，回溯信息并不总是那么阅读或理解。

*专业建议：将整个回溯粘贴到谷歌。*

### 使用交互式解释器（REPL）

执行脚本的 时候，可以使用选项 `-i` 使 Python 保持存活（keep alive）。

```bash
bash % python3 -i blah.py
Traceback (most recent call last):
  File "blah.py", line 13, in ?
    foo()
  File "blah.py", line 10, in foo
    bar()
  File "blah.py", line 7, in bar
    spam()
  File "blah.py", 4, in spam
    line x.append(3)
AttributeError: 'int' object has no attribute 'append'
>>>
```

选项 `-i` 可以保留解释器状态。这意味着可以在程序崩溃后查找错误信息。对变量的值和其它状态进行检查。

### 使用打印进行调试

使用 `print()` 函数进行调试非常常见。

*建议：确保使用的是  `repr()` 函数*。

```python
def spam(x):
    print('DEBUG:', repr(x))
    ...
```

`repr()`  函数显示一个值的准确表示，而不是格式良好的输出。

```python
>>> from decimal import Decimal
>>> x = Decimal('3.4')
# NO `repr`
>>> print(x)
3.4
# WITH `repr`
>>> print(repr(x))
Decimal('3.4')
>>>
```

### Python 的调试器

可以在程序内手动启动调试器（debugger）。

```python
def some_function():
    ...
    breakpoint()      # Enter the debugger (Python 3.7+)
    ...
```

上述操作会在  `breakpoint()` 调用时启动调试器。

在 Python 的早期版本中，可能会看到下面这样的调试指南：

```python
import pdb
...
pdb.set_trace()       # Instead of `breakpoint()`
...
```

（译注：Python 3.7 之后，可以使用内置函数 `breakpoint()` 代替 ` import pdb; pdb.set_trace()`）

### 在调试解释器下运行程序

也可以在调试器下运行整个程序：

```bash
bash % python3 -m pdb someprogram.py
```

上述操作会在第一行语句之前自动进入调试器，允许设置断点和修改配置。

常见的调试器命令：

```code
(Pdb) help            # Get help
(Pdb) w(here)         # Print stack trace
(Pdb) d(own)          # Move down one stack level
(Pdb) u(p)            # Move up one stack level
(Pdb) b(reak) loc     # Set a breakpoint
(Pdb) s(tep)          # Execute one instruction
(Pdb) c(ontinue)      # Continue execution
(Pdb) l(ist)          # List source code
(Pdb) a(rgs)          # Print args of current function
(Pdb) !statement      # Execute statement
```

断点的位置可以用下列任意一种方式进行表示：

```code
(Pdb) b 45            # Line 45 in current file
(Pdb) b file.py:45    # Line 34 in file.py
(Pdb) b foo           # Function foo() in current file
(Pdb) b module.foo    # Function foo() in a module
```

## 练习

### 练习 8.4：Bugs? 什么是 Bugs?

有 bug，我们就解决 bug（It runs. Ship it!）。

[目录](../Contents.md) \| [上一节 (8.2 日志)](02_Logging.md) \| [下一节 (9 包)](../09_Packages/00_Overview.md)