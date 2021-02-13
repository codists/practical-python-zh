[目录](../Contents.md) \| [上一节 (3.4 模块)](04_Modules.md) \| [下一节 (3.6 设计讨论)](06_Design_discussion.md)

# 3.5 主模块

This section introduces the concept of a main program or main module.

本节介绍主程序（主模块）的概念

### 主函数Main Functions

In many programming languages, there is a concept of a *main* function or method.

在许多编程语言中，存在一个主函数或者主方法的概念。

```c
// c / c++
int main(int argc, char *argv[]) {
    ...
}
```

```java
// java
class myprog {
    public static void main(String args[]) {
        ...
    }
}
```

This is the first function that executes when an application is launched.

当应用运行的时候，这是第一个执行的函数。

### Python 主模块Main Module

Python has no *main* function or method.  Instead, there is a *main*
module. The *main module* is the source file that runs first.

Python 没有主函数或者主方法。而是有一个主模块。主模块是第一个运行的源文件。

```bash
bash % python3 prog.py
...
```

Whatever file you give to the interpreter at startup becomes *main*. It doesn't matter the name.

开始的时候，任何给到解释器的文件都会成为主函数。名字不重要。

### `__main__` 检查

It is standard practice for modules that run as a main script to use this convention:

始终这种方式的最标准实践是把模块当做主脚本运行。

```python
# prog.py
...
if __name__ == '__main__':
    # Running as the main program ...
    statements
    ...
```

Statements enclosed inside the `if` statement become the *main* program.

在  `if` 语句里面的语句变成主程序。

### Main programs vs. library imports主程序与库导入

Any Python file can either run as main or as a library import:

任何 Python 文件可以作为主模块运行，也可以作为库导入：

```bash
bash % python3 prog.py # Running as main
```

```python
import prog   # Running as library import
```

In both cases, `__name__` is the name of the module.  However, it will only be set to `__main__` if
running as main.

在上面两种情形中，`__name__`  是模块的名称。但是，如果要作为主模块运行，`__name__` 只能设置为  `__main__` 。

Usually, you don't want statements that are part of the main program
to execute on a library import.  So, it's common to have an `if-`check
in code that might be used either way.

通常，您不想主程序的部分语句在库导入上执行。所以，通常在代码中可能进行 `if-` 检查。

```python
if __name__ == '__main__':
    # Does not execute if loaded with import ...
```

### Program Template程序模板

Here is a common program template for writing a Python program:

这里有一个用于编写 Python 程序的模板：

```python
# prog.py
# Import statements (libraries)
import modules

# Functions
def spam():
    ...

def blah():
    ...

# Main function
def main():
    ...

if __name__ == '__main__':
    main()
```

### Command Line Tools命令行工具

Python is often used for command-line tools

Python 通常用于命令行工具：

```bash
bash % python3 report.py portfolio.csv prices.csv
```

It means that the scripts are executed from the shell /
terminal. Common use cases are for automation, background tasks, etc.

这意味这脚本从 shell 或者 终端（terminal）执行。通常，用于自动化，后台任务等。 

### Command Line Args命令行参数

The command line is a list of text strings.

命令行是一个文本字符串列表。

```bash
bash % python3 report.py portfolio.csv prices.csv
```

This list of text strings is found in `sys.argv`.

该文本字符串列表被 `sys.argv` 调用。

```python
# In the previous bash command
sys.argv # ['report.py, 'portfolio.csv', 'prices.csv']
```

Here is a simple example of processing the arguments:

这里有一个处理参数的例子：

```python
import sys

if len(sys.argv) != 3:
    raise SystemExit(f'Usage: {sys.argv[0]} ' 'portfile pricefile')
portfile = sys.argv[1]
pricefile = sys.argv[2]
...
```

### 标准 I/O

Standard Input / Output (or stdio) are files that work the same as normal files.

标准输入/输出（或者stdio）是作为普通文件一样工作的文件。

```python
sys.stdout
sys.stderr
sys.stdin
```

By default, print is directed to `sys.stdout`.  Input is read from
`sys.stdin`.  Tracebacks and errors are directed to `sys.stderr`.

默认情况下，输出定向到 `sys.stdout`。输入从 `sys.stdin` 读取。回溯和错误定向到 `sys.stderr`。

Be aware that *stdio* could be connected to terminals, files, pipes, etc.

请注意，标准输入/输出（*stdio*）可以连接到终端，文件，管道等。

```bash
bash % python3 prog.py > results.txt
# or
bash % cmd1 | python3 prog.py | cmd2
```

### Environment Variables环境变量

Environment variables are set in the shell.

环境变量在 shell 中设置。

```bash
bash % setenv NAME dave
bash % setenv RSH ssh
bash % python3 prog.py
```

`os.environ` is a dictionary that contains these values.

`os.environ` 是包含这些值的字典。

```python
import os

name = os.environ['NAME'] # 'dave'
```

Changes are reflected in any subprocesses later launched by the program.

更改反映在程序后续运行的子程序中。

### Program Exit程序退出

Program exit is handled through exceptions.

程序退出通过异常进行处理。

```python
raise SystemExit
raise SystemExit(exitcode)
raise SystemExit('Informative message')
```

An alternative.

其它方式。

```python
import sys
sys.exit(exitcode)
```

A non-zero exit code indicates an error.

非零（non-zero ）退出码表示错误。

### The `#!` line `#!`行

On Unix, the `#!` line can launch a script as Python.
Add the following to the first line of your script file.

在 Unix 系统中，`#!` 可以把脚本当做 Python 运行。把下面的内容添加到您的脚本文件的第一行。

```python
#!/usr/bin/env python3
# prog.py
...
```

It requires the executable permission.

该脚本需要执行权限。

```bash
bash % chmod +x prog.py
# Then you can execute
bash % prog.py
... output ...
```

*Note: The Python Launcher on Windows also looks for the `#!` line to indicate language version.*

*注意：Windows 系统上的 Python 启动器也寻找 `#!`  行去表示语言版本。* 

### Script Template脚本模板

Finally, here is a common code template for Python programs that run
as command-line scripts:

最后，这里有一个代码模板，用于将 Python 程序作为命令行脚本运行。

```python
#!/usr/bin/env python3
# prog.py

# Import statements (libraries)
import modules

# Functions
def spam():
    ...

def blah():
    ...

# Main function
def main(argv):
    # Parse command line args, environment, etc.
    ...

if __name__ == '__main__':
    import sys
    main(sys.argv)
```

## 练习

### 练习 3.15：`main()` 函数

In the file `report.py` add a `main()` function that accepts a list of
command line options and produces the same output as before.  You
should be able to run it interatively like this:

在 `report.py` 文件中，添加一个 `main()` 函数，接受命令行选项列表并且生成和之前一样的输出。您应该能够像下面这样交互地运行它：

```python
>>> import report
>>> report.main(['report.py', 'Data/portfolio.csv', 'Data/prices.csv'])
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84
>>>
```

Modify the `pcost.py` file so that it has a similar `main()` function:

请修改  `pcost.py`  文件，以便它拥有一个类似的 `main()` 函数。

```python
>>> import pcost
>>> pcost.main(['pcost.py', 'Data/portfolio.csv'])
Total cost: 44671.15
>>>
```

### 练习 3.16：生成脚本 Making Scripts

Modify the `report.py` and `pcost.py` programs so that they can
execute as a script on the command line:

请修改 `report.py` 和 `pcost.py`  程序，以便它们在命令行上可以作为脚本执行。

```bash
bash $ python3 report.py Data/portfolio.csv Data/prices.csv
      Name     Shares      Price     Change
---------- ---------- ---------- ----------
        AA        100       9.22     -22.98
       IBM         50     106.28      15.18
       CAT        150      35.46     -47.98
      MSFT        200      20.89     -30.34
        GE         95      13.48     -26.89
      MSFT         50      20.89     -44.21
       IBM        100     106.28      35.84

bash $ python3 pcost.py Data/portfolio.csv
Total cost: 44671.15
```

[目录](../Contents.md) \| [上一节 (3.4 模块)](04_Modules.md) \| [下一节 (3.6 设计讨论)](06_Design_discussion.md)
