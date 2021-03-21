[目录](../Contents.md) \| [上一节 (8.1 测试)](01_Testing.md) \| [下一节 (8.3 调试)](03_Debugging.md)

# 8.2 日志

本节对日志模块（logging module）进行简单的介绍。

### logging 模块

`logging` 模块是用于记录诊断信息的标准库模块。日志模块非常庞大，具有许多复制的功能。我们将会展示一个简单的例子来说明其用处。

### 再探异常

In the exercises, we wrote a function `parse()` that looked something
like this:

在本节练习中，我们创建这样一个 `parse()` 函数：

```python
# fileparse.py
def parse(f, types=None, names=None, delimiter=None):
    records = []
    for line in f:
        line = line.strip()
        if not line: continue
        try:
            records.append(split(line,types,names,delimiter))
        except ValueError as e:
            print("Couldn't parse :", line)
            print("Reason :", e)
    return records
```

Focus on the `try-except` statement. What should you do in the `except` block?

看到  `try-except`  语句，在 `except` 块中，我们应该做什么？

Should you print a warning message?

应该打印警告消息（warning message）？

```python
try:
    records.append(split(line,types,names,delimiter))
except ValueError as e:
    print("Couldn't parse :", line)
    print("Reason :", e)
```

Or do you silently ignore it?

或者忽略警告信息？

```python
try:
    records.append(split(line,types,names,delimiter))
except ValueError as e:
    pass
```

Neither solution is satisfactory because you often want *both* behaviors (user selectable).

任何一种方式都无法令人满意，因为通常情况下，我们两种方式都想要（允许用户选择）。

### 使用 logging

The `logging` module can address this.

`logging` 模块可以解决这个问题：

```python
# fileparse.py
import logging
log = logging.getLogger(__name__)

def parse(f,types=None,names=None,delimiter=None):
    ...
    try:
        records.append(split(line,types,names,delimiter))
    except ValueError as e:
        log.warning("Couldn't parse : %s", line)
        log.debug("Reason : %s", e)
```

The code is modified to issue warning messages or a special `Logger`
object. The one created with `logging.getLogger(__name__)`.

代码被修改以发出警告消息，或者特殊的  `Logger` 对象。  `Logger` 对象使用 `logging.getLogger(__name__)` 创建

### Logging Basics日志基础

Create a logger object.

创建一个记录器对象（logger object）。

```python
log = logging.getLogger(name)   # name is a string
```

Issuing log messages.

发出日志消息：

```python
log.critical(message [, args])
log.error(message [, args])
log.warning(message [, args])
log.info(message [, args])
log.debug(message [, args])
```

*Each method represents a different level of severity.*

*不同方法代表不同级别的严重性。*

All of them create a formatted log message. `args` is used with the `%` operator to create the message.

所有的方法都创建格式化的日志消息。`args` 和 `%` 运算符 一起使用以创建消息。

```python
logmsg = message % args # Written to the log
```

### Logging Configuration日志配置

The logging behavior is configured separately.

日志行为可以分开配置：

```python
# main.py

...

if __name__ == '__main__':
    import logging
    logging.basicConfig(
        filename  = 'app.log',      # Log output file
        level     = logging.INFO,   # Output level
    )
```

Typically, this is a one-time configuration at program startup.  The
configuration is separate from the code that makes the logging calls.

通常，在程序启动时，日志配置是一次性的。配置从代码中分离出来是为了实现日志调用。

### Comments说明

Logging is highly configurable.  You can adjust every aspect of it:
output files, levels, message formats, etc.  However, the code that
uses logging doesn't have to worry about that.

日志是可以任意配置的。你可以调整日志的任何一方面：输出文件，级别，消息格式等等，不比担心对使用日志模块的代码造成影响。

## 练习

### 练习 8.2：添加日志到模块中Adding logging to a module

In `fileparse.py`, there is some error handling related to
exceptions caused by bad input. It looks like this:

在 `fileparse.py` 中，有一些与异常有关的错误处理，这些异常是有错误输入引起的。如下所示：

```python
# fileparse.py
import csv

def parse_csv(lines, select=None, types=None, has_headers=True, delimiter=',', silence_errors=False):
    '''
    Parse a CSV file into a list of records with type conversion.
    '''
    if select and not has_headers:
        raise RuntimeError('select requires column headers')

    rows = csv.reader(lines, delimiter=delimiter)

    # Read the file headers (if any)
    headers = next(rows) if has_headers else []

    # If specific columns have been selected, make indices for filtering and set output columns
    if select:
        indices = [ headers.index(colname) for colname in select ]
        headers = select

    records = []
    for rowno, row in enumerate(rows, 1):
        if not row:     # Skip rows with no data
            continue

        # If specific column indices are selected, pick them out
        if select:
            row = [ row[index] for index in indices]

        # Apply type conversion to the row
        if types:
            try:
                row = [func(val) for func, val in zip(types, row)]
            except ValueError as e:
                if not silence_errors:
                    print(f"Row {rowno}: Couldn't convert {row}")
                    print(f"Row {rowno}: Reason {e}")
                continue

        # Make a dictionary or a tuple
        if headers:
            record = dict(zip(headers, row))
        else:
            record = tuple(row)
        records.append(record)

    return records
```

Notice the print statements that issue diagnostic messages.  Replacing those
prints with logging operations is relatively simple.  Change the code like this:

我们可以看到，print 语句打印出诊断消息。请使用日志操作来替换这些 print 语句相对来说更简单。请像下面这样修改代码：

```python
# fileparse.py
import csv
import logging
log = logging.getLogger(__name__)

def parse_csv(lines, select=None, types=None, has_headers=True, delimiter=',', silence_errors=False):
    '''
    Parse a CSV file into a list of records with type conversion.
    '''
    if select and not has_headers:
        raise RuntimeError('select requires column headers')

    rows = csv.reader(lines, delimiter=delimiter)

    # Read the file headers (if any)
    headers = next(rows) if has_headers else []

    # If specific columns have been selected, make indices for filtering and set output columns
    if select:
        indices = [ headers.index(colname) for colname in select ]
        headers = select

    records = []
    for rowno, row in enumerate(rows, 1):
        if not row:     # Skip rows with no data
            continue

        # If specific column indices are selected, pick them out
        if select:
            row = [ row[index] for index in indices]

        # Apply type conversion to the row
        if types:
            try:
                row = [func(val) for func, val in zip(types, row)]
            except ValueError as e:
                if not silence_errors:
                    log.warning("Row %d: Couldn't convert %s", rowno, row)
                    log.debug("Row %d: Reason %s", rowno, e)
                continue

        # Make a dictionary or a tuple
        if headers:
            record = dict(zip(headers, row))
        else:
            record = tuple(row)
        records.append(record)

    return records
```

Now that you've made these changes, try using some of your code on
bad data.

完成修改后，尝试在错误的数据上使用这些代码：

```python
>>> import report
>>> a = report.read_portfolio('Data/missing.csv')
Row 4: Bad row: ['MSFT', '', '51.23']
Row 7: Bad row: ['IBM', '', '70.44']
>>>
```

If you do nothing, you'll only get logging messages for the `WARNING`
level and above.  The output will look like simple print statements.
However, if you configure the logging module, you'll get additional
information about the logging levels, module, and more.  Type these
steps to see that:

如果什么都不做，你将会获得 `WARNING` 级别以上的日志消息。输出看起来像简单的打印语句。但是，如果你配置了日志模块，你将会获得有关日志级别，模块等其它信息。请按以下步骤操作查看：

```python
>>> import logging
>>> logging.basicConfig()
>>> a = report.read_portfolio('Data/missing.csv')
WARNING:fileparse:Row 4: Bad row: ['MSFT', '', '51.23']
WARNING:fileparse:Row 7: Bad row: ['IBM', '', '70.44']
>>>
```

You will notice that you don't see the output from the `log.debug()`
operation. Type this to change the level.

你会发现，没有看到来自于  `log.debug()` 操作的输出。请按以下步骤来修改日志级别：

```
>>> logging.getLogger('fileparse').level = logging.DEBUG
>>> a = report.read_portfolio('Data/missing.csv')
WARNING:fileparse:Row 4: Bad row: ['MSFT', '', '51.23']
DEBUG:fileparse:Row 4: Reason: invalid literal for int() with base 10: ''
WARNING:fileparse:Row 7: Bad row: ['IBM', '', '70.44']
DEBUG:fileparse:Row 7: Reason: invalid literal for int() with base 10: ''
>>>
```

Turn off all, but the most critical logging messages:

只留下 critical 级别的日志消息，关闭其它级别的日志消息。

```
>>> logging.getLogger('fileparse').level=logging.CRITICAL
>>> a = report.read_portfolio('Data/missing.csv')
>>>
```

### 练习 8.3：添加日志到程序中Adding Logging to a Program

To add logging to an application, you need to have some mechanism to
initialize the logging module in the main module.  One way to
do this is to include some setup code that looks like this:

要添加日志到应用中，你需要一个机制来实现在主模块中初始化日志。其中一种方式就是包含设置代码，如下所示：

```
# This file sets up basic configuration of the logging module.
# Change settings here to adjust logging output as needed.
import logging
logging.basicConfig(
    filename = 'app.log',            # Name of the log file (omit to use stderr)
    filemode = 'w',                  # File mode (use 'a' to append)
    level    = logging.WARNING,      # Logging level (DEBUG, INFO, WARNING, ERROR, or CRITICAL)
)
```

Again, you'd need to put this someplace in the startup steps of your
program.  For example, where would you put this in your `report.py` program?

再次说明，你需要将日志配置代码放到程序启动步骤中。例如，就像 `report.py` 程序里面的那样。

[目录](../Contents.md) \| [上一节 (8.1 测试)](01_Testing.md) \| [下一节 (8.3 调试)](03_Debugging.md)