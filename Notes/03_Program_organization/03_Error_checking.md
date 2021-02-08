[目录](../Contents.md) \| [上一节 (3.2 深入函数)](02_More_functions.md) \| [下一节 (3.4 模块)](04_Modules.md)

# 3.3 错误检查

虽然前面已经介绍了异常，但本节补充一些有关错误检查和异常处理的其它细节。

### 程序是如何运行失败的

Python 不对函数参数类型或值进行检查或者校验。函数可以处理与函数内部语句兼容的任何数据，

```python
def add(x, y):
    return x + y

add(3, 4)               # 7
add('Hello', 'World')   # 'HelloWorld'
add('3', '4')           # '34'
```

如果函数中有错误，它们将（作为异常）在运行时出现。

```python
def add(x, y):
    return x + y

>>> add(3, '4')
Traceback (most recent call last):
...
TypeError: unsupported operand type(s) for +:
'int' and 'str'
>>>
```

为了验证代码，强烈建议进行测试（稍后介绍）。

### 异常

异常用于发出错误信号。

要自己触发异常，请使用 `raise` 语句。

```python
if name not in authorized:
    raise RuntimeError(f'{name} not authorized')
```

要捕获异常，请使用 `try-except` 语句。

```python
try:
    authenticate(username)
except RuntimeError as e:
    print(e)
```

### 异常处理

异常传递到第一个匹配的 `except` 。

```python
def grok():
    ...
    raise RuntimeError('Whoa!')   # Exception raised here

def spam():
    grok()                        # Call that will raise exception

def bar():
    try:
       spam()
    except RuntimeError as e:     # Exception caught here
        ...

def foo():
    try:
         bar()
    except RuntimeError as e:     # Exception does NOT arrive here
        ...

foo()
```

要处理异常，请将语句放到 `except` 块里面。可以添加要处理该错误的任何语句。

```python
def grok(): ...
    raise RuntimeError('Whoa!')

def bar():
    try:
      grok()
    except RuntimeError as e:   # Exception caught here
        statements              # Use this statements
        statements
        ...

bar()
```

异常处理之后，从 `try-except` 之后的第一个语句继续执行。

```python
def grok(): ...
    raise RuntimeError('Whoa!')

def bar():
    try:
      grok()
    except RuntimeError as e:   # Exception caught here
        statements
        statements
        ...
    statements                  # Resumes execution here
    statements                  # And continues here
    ...

bar()
```

### 内置异常

有非常多的內建异常。通常，异常名称表明出了什么问题（例如，因为提供错误的值而触发 `ValueError`）。这不是一分详尽的清单，请访问 [文档](https://docs.python.org/3/library/exceptions.html) 以获取更多信息。

```python
ArithmeticError
AssertionError
EnvironmentError
EOFError
ImportError
IndexError
KeyboardInterrupt
KeyError
MemoryError
NameError
ReferenceError
RuntimeError
SyntaxError
SystemError
TypeError
ValueError
```

### 异常值

异常具有一个关联值。它包含有关错误的更明确的信息。

```python
raise RuntimeError('Invalid user name')
```

这个值是异常实例的一部分，它被放置在提供给 `except` 的变量中。

```python
try:
    ...
except RuntimeError as e:   # `e` holds the exception raised
    ...
```

`e` 是异常类型的一个实例。但是，当打印的时候，它通常看起来像一个字符串。

```python
except RuntimeError as e:
    print('Failed : Reason', e)
```

### 捕获多个异常

可以使用多个 `except` 块捕获不同类型的异常。

```python
try:
  ...
except LookupError as e:
  ...
except RuntimeError as e:
  ...
except IOError as e:
  ...
except KeyboardInterrupt as e:
  ...
```

或者，如果处理不同异常的语句是相同的，则可以对它们进行分组。

```python
try:
  ...
except (IOError,LookupError,RuntimeError) as e:
  ...
```

### 捕获所有的异常

要捕获所有的异常，请使用 `Exception` 。如下所示：

```python
try:
    ...
except Exception:       # DANGER. See below
    print('An error occurred')
```

通常，像这样编写代码是个坏主意，因为这说明不知道程序为什么会失败。

### 捕获异常的错误方式

这里是一个使用异常的错误方式。

```python
try:
    go_do_something()
except Exception:
    print('Computer says no')
```

这将捕获所有可能的错误，并且，当代码因为某些根本没想到的原因（如卸载 Python 模块等）运行失败时，可能无法进行调试。

### 更好的方式

如果想要捕获所有的错误，这有一个更明智的方法。

```python
try:
    go_do_something()
except Exception as e:
    print('Computer says no. Reason :', e)
```

它报告了失败的明确原因。当编写捕获所有可能异常的代码时，拥有查看/报告错误的机制几乎总是一个好主意。

不过，通常来说，最好在合理的范围内尽量窄地捕获异常。仅捕获能处理的异常。让其它错误通过——也许其它代码可以处理。

### 重新触发异常

使用 `raise` 传递捕获的错误。

```python
try:
    go_do_something()
except Exception as e:
    print('Computer says no. Reason :', e)
    raise
```

这允许您采取措施（例如:记录日志）并将错误传递给调用者。

### 异常最佳实践

不要捕获异常，而是失败发生时“停止运行，发出预警”（Fail fast and loud）。如果重要的话，别人会处理的。只有您是那个人的时候才捕获异常。即，只捕获可以恢复并正常运行的错误。

### `finally` 语句

`finally` 语句指定无论是否发生异常都必须运行的代码。

```python
lock = Lock()
...
lock.acquire()
try:
    ...
finally:
    lock.release()  # this will ALWAYS be executed. With and without exception.
```

通常使用 `finally` 语句安全地管理资源（尤其是锁，文件等）。

### `with` 语句

在现代代码中，`try-finally` 语句通常被 `with` 语句取代。

```python
lock = Lock()
with lock:
    # lock acquired
    ...
# lock released
```

一个更熟悉的例子：

```python
with open(filename) as f:
    # Use the file
    ...
# File closed
```

`with` 语句定义资源的使用*上下文*。当执行离开上下文时，资源被释放。`with` 语句仅适用于经过专门编程以支持它的某些对象。

## 练习

### 练习 3.8：触发异常

在上一节中编写的 `parse_csv()` 函数允许选择用户指定的列，但是只有输入数据文件具有列标题时才会生效。

请修改代码，以便在同时传递 `select` 和 `has_headers=False` 参数时触发异常。例如：

```python
>>> parse_csv('Data/prices.csv', select=['name','price'], has_headers=False)
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "fileparse.py", line 9, in parse_csv
    raise RuntimeError("select argument requires column headers")
RuntimeError: select argument requires column headers
>>>
```

添加此检查后，您可能会问是否应该在函数中执行其它类型的完整性检查。例如，检查文件名是字符串，列表还是其它类型？

一般来说，最好是跳过此类测试，输入错误的时候让程序运行失败。回溯信息会指出问题的根源，并且帮助调试。

添加上述检查的主要原因是为了避免在无意义的模式下运行代码（例如，使用要求列标题的特性，但是同时指定没有标题）。

这表明调用代码部分出现一个编程错误。检查“不应发生”的情况通常是个好主意。

### 练习 3.9：捕获异常

您编写的 `parse_csv()` 函数用于处理文件的全部内容。但是，在现实世界中，输入文件可能包含损坏的，丢失的或者脏数据。尝试下面这个实验：

```python
>>> portfolio = parse_csv('Data/missing.csv', types=[str, int, float])
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "fileparse.py", line 36, in parse_csv
    row = [func(val) for func, val in zip(types, row)]
ValueError: invalid literal for int() with base 10: ''
>>>
```

请修改  `parse_csv()`  函数以便捕获所有在记录创建期间生成的 `ValueError` 异常，并为无法转换的行打印警告消息。

错误消息应该包括行号以及有关失败原因的信息。要测试函数，尝试读取上面的 `Data/missing.csv` 文件，例如：

```python
>>> portfolio = parse_csv('Data/missing.csv', types=[str, int, float])
Row 4: Couldn't convert ['MSFT', '', '51.23']
Row 4: Reason invalid literal for int() with base 10: ''
Row 7: Couldn't convert ['IBM', '', '70.44']
Row 7: Reason invalid literal for int() with base 10: ''
>>>
>>> portfolio
[{'price': 32.2, 'name': 'AA', 'shares': 100}, {'price': 91.1, 'name': 'IBM', 'shares': 50}, {'price': 83.44, 'name': 'CAT', 'shares': 150}, {'price': 40.37, 'name': 'GE', 'shares': 95}, {'price': 65.1, 'name': 'MSFT', 'shares': 50}]
>>>
```

### 练习 3.10：隐藏错误 

请修改 `parse_csv()`函数，以便用户明确需要时可以隐藏解析的错误消息，例如：

```python
>>> portfolio = parse_csv('Data/missing.csv', types=[str,int,float], silence_errors=True)
>>> portfolio
[{'price': 32.2, 'name': 'AA', 'shares': 100}, {'price': 91.1, 'name': 'IBM', 'shares': 50}, {'price': 83.44, 'name': 'CAT', 'shares': 150}, {'price': 40.37, 'name': 'GE', 'shares': 95}, {'price': 65.1, 'name': 'MSFT', 'shares': 50}]
>>>
```

在大部分的程序中，错误处理是最难做好的事情之一。一般来说，不应该默默地忽略错误。相反，最好是报告问题，并且让用户选择是否隐藏错误信息（如果它们选择这样做）。

[目录](../Contents.md) \| [上一节 (3.2 深入函数)](02_More_functions.md) \| [下一节 (3.4 模块)](04_Modules.md)