[目录](../Contents.md) \| [上一节 (3.4 模块)](04_Modules.md) \| [下一节 (3.6 设计讨论)](06_Design_discussion.md)

# 3.5 主模块

本节介绍主程序（主模块）的概念

### 主函数

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

这是启动应用程序时执行的第一个函数。

### Python 主模块

Python 没有主函数（*main* function）或主方法（main method）。相反，Python 有一个主模块（*main*
module）。主模块是第一个运行的源文件。

```bash
bash % python3 prog.py
...
```

在应用程序启动时，提供给解释器的任何文件都将成为主模块。名字并不重要。

### `__main__` 检查

对于作为主脚本运行的模块，使用此约定（译注：`__main__` 检查）是标准做法。

```python
# prog.py
...
if __name__ == '__main__':
    # Running as the main program ...
    statements
    ...
```

在  `if` 里面的语句称为主程序（ *main* program）。

### 主程序与库导入

任何 Python 文件都可以作为主程序运行，或者作为一个库（译注：library，在 Python 中 library 既可以指模块 module，也可以指包 package），导入后运行。

```bash
bash % python3 prog.py # Running as main
```

```python
import prog   # Running as library import
```

在这两种情况下，`__name__`  都是模块的名称（译注：prog）。然而，如果作为主程序运行，`__name__` 只能被设置为 `__main__`。

通常，我们不希望主程序中的语句在库导入的时候执行。所以，通常在代码中包含一个 `if-` 检查，判断当前文件是否是主程序（译注：如果当前程序不是主程序，则 `if __name__ == '__main__':` 里面的语句不执行）。

```python
if __name__ == '__main__':
    # Does not execute if loaded with import ...
```

### 程序模板

这里有一个用于编写 Python 程序的通用模板：

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

### 命令行工具

Python 通常在命令行工具中使用：

```bash
bash % python3 report.py portfolio.csv prices.csv
```

这意味着脚本在 shell 或者 终端（terminal）执行。通常用于自动化，后台任务等。 

### 命令行参数

命令行参数是一个文本字符串列表。

```bash
bash % python3 report.py portfolio.csv prices.csv
```

该文本字符串列表可以在 `sys.argv` 中找到。

```python
# In the previous bash command
sys.argv # ['report.py, 'portfolio.csv', 'prices.csv']
```

这里有一个处理参数的简单示例：

```python
import sys

if len(sys.argv) != 3:
    raise SystemExit(f'Usage: {sys.argv[0]} ' 'portfile pricefile')
portfile = sys.argv[1]
pricefile = sys.argv[2]
...
```

### 标准 I/O

标准输入/输出（或者stdio）是和普通文件使用相同工作方式的文件。

```python
sys.stdout
sys.stderr
sys.stdin
```

默认情况下，打印定向到 `sys.stdout` 文件。输入是从 `sys.stdin` 文件读取。回溯和错误定向到 `sys.stderr` 文件。

请注意，标准输入/输出（*stdio*）可以连接到终端（terminals），文件（files），管道（pipes）等。

```bash
bash % python3 prog.py > results.txt
# or
bash % cmd1 | python3 prog.py | cmd2
```

### 环境变量

环境变量在 shell 中设置。

```bash
bash % setenv NAME dave
bash % setenv RSH ssh
bash % python3 prog.py
```

`os.environ` 是包含这些值的字典。

```python
import os

name = os.environ['NAME'] # 'dave'
```

更改会反映在程序随后启动的任何子进程中。

### 程序退出

通过异常处理程序退出。

```python
raise SystemExit
raise SystemExit(exitcode)
raise SystemExit('Informative message')
```

其它方式。

```python
import sys
sys.exit(exitcode)
```

非零（non-zero ）退出码表示错误。

### `#!` 行

在 Unix 系统中，`#!` 行指定某个路径下的 Python 解释器来执行该脚本（译注：`#!` 称为 Shebang  或者 hashbang，因为 # 号通常称为 hash 或者 sharp，而 ! 号则常常称为 bang）。将以下内容添加到脚本文件的第一行。

```python
#!/usr/bin/env python3
# prog.py
...
```

（译注：`#!/usr/bin/env python3` 的意思——到 Unix 系统 env 所包含的全部环境变量中寻找 Python3 解释器，并使用 Python3 解释器执行该脚本）

执行脚本需要脚本具有可执行权限。

```bash
bash % chmod +x prog.py
# Then you can execute
bash % prog.py
... output ...
```

*注意：Windows 系统上的 Python 启动器也会寻找 `#!`  行以指示语言版本。* 

### 脚本模板

最后，这里有一个通用代码模板，用于将 Python 程序作为命令行脚本运行：

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

在 `report.py` 文件中添加一个 `main()` 函数，该函数接受命令行选项列表，并生成与以前相同的输出。修改后，应该能够像下面这样交互地运行它：

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

请修改  `pcost.py`  文件，添加一个类似的 `main()` 函数。

```python
>>> import pcost
>>> pcost.main(['pcost.py', 'Data/portfolio.csv'])
Total cost: 44671.15
>>>
```

### 练习 3.16：编写脚本

请修改 `report.py` 和 `pcost.py`  程序，以便它们在命令行上可以作为脚本执行：

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
