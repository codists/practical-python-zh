[目录](../Contents.md) \| [上一节 (3.1 脚本)](01_Script.md) \| [下一节 (3.3 错误检查)](03_Error_checking.md)

# 3.2 深入函数

尽管函数在早先时候介绍了，但有关函数在更深层次上是如何工作的细节却很少提供。本节旨在填补这些空白，并讨论函数调用约定，作用域规则等问题。

### 调用函数

考虑以下函数：

```python
def read_prices(filename, debug):
    ...
```

可以使用位置参数调用该函数：

```
prices = read_prices('prices.csv', True)
```

或者，可以使用关键字参数调用该函数：

```python
prices = read_prices(filename='prices.csv', debug=True)
```

### 默认参数

有时候，您希望参数是可选的，如果是这样，请在函数定义中分配一个默认值。

```python
def read_prices(filename, debug=False):
    ...
```

如果分配了默认值，则参数在函数调用中是可选的。

```python
d = read_prices('prices.csv')
e = read_prices('prices.dat', True)
```

*注意：带有默认值的参数（译注：即关键字参数）必须出现在参数列表的末尾（所有非可选参数都放在最前面）*

### 首选关键字参数作为可选参数

比较以下两种不同的调用风格：

```python
parse_data(data, False, True) # ?????

parse_data(data, ignore_errors=True)
parse_data(data, debug=True)
parse_data(data, debug=True, ignore_errors=True)
```

在大部分情况下，关键字参数提高了代码的简洁性——特别是对于用作标志的参数，或者与可选特性相关的参数。

### 设计最佳实践

始终为函数参数指定简短但有意义的名称。

使用函数的人可能想要使用关键字调用风格。

```python
d = read_prices('prices.csv', debug=True)
```

Python 开发工具将会在帮助功能或者帮助文档中显示这些名称。

### 返回值

`return` 语句返回一个值：

```python
def square(x):
    return x * x
```

如果没有给出返回值或者 `return` 语句缺失，那么返回 `None`：

```python
def bar(x):
    statements
    return

a = bar(4)      # a = None

# OR
def foo(x):
    statements  # No `return`

b = foo(4)      # b = None
```

### 多个返回值

函数只能返回一个值。但是，通过将返回值放到元组中，函数可以返回多个值：

```python
def divide(a,b):
    q = a // b      # Quotient
    r = a % b       # Remainder
    return q, r     # Return a tuple
```

用例：

```python
x, y = divide(37,5) # x = 7, y = 2

x = divide(37, 5)   # x = (7, 2)
```

### 变量作用域

程序给变量赋值：

```python
x = value # Global variable

def foo():
    y = value # Local variable
```

变量赋值发生在函数的内部和外部。定义在函数外部的变量是“全局的”。定义在函数内部的变量是“局部的”。

### 局部变量

在函数内部赋值的变量是私有的。

```python
def read_portfolio(filename):
    portfolio = []
    for line in open(filename):
        fields = line.split(',')
        s = (fields[0], int(fields[1]), float(fields[2]))
        portfolio.append(s)
    return portfolio
```

在此示例中，`filename`, `portfolio`, `line`, `fields` 和 `s` 是局部变量。在函数调用之后，这些变量将不会保留或者不可访问。

```python
>>> stocks = read_portfolio('portfolio.csv')
>>> fields
Traceback (most recent call last):
File "<stdin>", line 1, in ?
NameError: name 'fields' is not defined
>>>
```

局部变量也不能与其它地方的变量冲突。

### 全局变量

函数可以自由地访问定义在同一文件中的全局变量值。

```python
name = 'Dave'

def greeting():
    print('Hello', name)  # Using `name` global variable
```

但是，函数不能修改全局变量：

```python
name = 'Dave'

def spam():
  name = 'Guido'

spam()
print(name) # prints 'Dave'
```

**切记：函数中的所有赋值都是局部的**

### 修改全局变量

如果必须修改全局变量，请像下面这样声明它：

```python
name = 'Dave'

def spam():
    global name
    name = 'Guido' # Changes the global name above
```

全局声明必须在使用之前出现，并且相应的变量必须与该函数处在同一文件中。看上面这个函数，要知道这是一种糟糕的形式。事实上，如果可以的话，尽量避免使用 `global` 。如果需要一个函数来修改函数外部的某种状态，最好是使用类来代替（稍后详细介绍）。

### 参数传递

当调用一个函数的时候，参数变量的传递是引用传递。不拷贝值（参见[2.7 节](../02_Working_with_data/07_Objects)）。如果传递了可变数据类型（如列表，字典），它们可以被原地修改。

```python
def foo(items):
    items.append(42)    # Modifies the input object

a = [1, 2, 3]
foo(a)
print(a)                # [1, 2, 3, 42]
```

**关键点：函数不接收输入参数的拷贝。**

### 重新赋值与修改

确保了解修改值与给变量名重新赋值的细微差别。

```python
def foo(items):
    items.append(42)    # Modifies the input object

a = [1, 2, 3]
foo(a)
print(a)                # [1, 2, 3, 42]

# VS
def bar(items):
    items = [4,5,6]    # Changes local `items` variable to point to a different object

b = [1, 2, 3]
bar(b)
print(b)                # [1, 2, 3]
```

*提醒：变量赋值永远不会重写内存。名称只是被绑定到了新的值上面*

## 练习

本组练习实现的内容可能是本课程最强大的和最难的。有很多步骤，并且过去练习中的许多概念被一次性整合在一起。虽然最后的题解只有大约 25 行的代码，但要花点时间，确保您理解每一个部分。

`report.py` 的中心部分主要用于读取 CSV 文件。例如，`read_portfolio()` 函数读取包含投资组合数据的文件，`read_prices()` 函数读取包含价格数据的文件。在这两个函数中，有很多底层的“精细的”事以及相似的特性。例如，它们都打开一个文件并使用 `csv` 模块来处理，并且将各种字段转换为新的类型。

如果真的需要对大量的文件进行解析，可能需要清理其中的一些内容使其更通用。这是我们的目标。

通过打开 `Work/fileparse.py` 文件开始本练习，该文件是我们将要写代码的地方。

### 练习 3.3：读取 CSV 文件

首先，让我们仅关注将 CSV 文件读入字典列表的问题。在 `fileparse.py` 中，定义一个如下所示的函数：

```python
# fileparse.py
import csv

def parse_csv(filename):
    '''
    Parse a CSV file into a list of records
    '''
    with open(filename) as f:
        rows = csv.reader(f)

        # Read the file headers
        headers = next(rows)
        records = []
        for row in rows:
            if not row:    # Skip rows with no data
                continue
            record = dict(zip(headers, row))
            records.append(record)

    return records
```

该函数将 CSV 文件读入字典列表中，但是隐藏了打开文件，使用 `csv` 模块处理，忽略空行等详细信息。

试试看：

提示： `python3 -i fileparse.py`.

```python
>>> portfolio = parse_csv('Data/portfolio.csv')
>>> portfolio
[{'price': '32.20', 'name': 'AA', 'shares': '100'}, {'price': '91.10', 'name': 'IBM', 'shares': '50'}, {'price': '83.44', 'name': 'CAT', 'shares': '150'}, {'price': '51.23', 'name': 'MSFT', 'shares': '200'}, {'price': '40.37', 'name': 'GE', 'shares': '95'}, {'price': '65.10', 'name': 'MSFT', 'shares': '50'}, {'price': '70.44', 'name': 'IBM', 'shares': '100'}]
>>>
```

这很好，除了不能使用数据做任何有用的计算之外。因为所有的内容都是用字符串表示。我们将马上解决此问题，先让我们继续在此基础上进行构建。

### 练习 3.4：构建列选择器

在大部分情况下，您只对 CSV 文件中选定的列感兴趣，而不是所有数据。修改 `parse_csv()` 函数，以便让用户指定任意的列，如下所示：

```python
>>> # Read all of the data
>>> portfolio = parse_csv('Data/portfolio.csv')
>>> portfolio
[{'price': '32.20', 'name': 'AA', 'shares': '100'}, {'price': '91.10', 'name': 'IBM', 'shares': '50'}, {'price': '83.44', 'name': 'CAT', 'shares': '150'}, {'price': '51.23', 'name': 'MSFT', 'shares': '200'}, {'price': '40.37', 'name': 'GE', 'shares': '95'}, {'price': '65.10', 'name': 'MSFT', 'shares': '50'}, {'price': '70.44', 'name': 'IBM', 'shares': '100'}]

>>> # Read only some of the data
>>> shares_held = parse_csv('Data/portfolio.csv', select=['name','shares'])
>>> shares_held
[{'name': 'AA', 'shares': '100'}, {'name': 'IBM', 'shares': '50'}, {'name': 'CAT', 'shares': '150'}, {'name': 'MSFT', 'shares': '200'}, {'name': 'GE', 'shares': '95'}, {'name': 'MSFT', 'shares': '50'}, {'name': 'IBM', 'shares': '100'}]
>>>
```

[练习 2.23](../02_Working_with_data/06_List_comprehension) 中给出了列选择器的示例。

然而，这里有一个方法可以做到这一点：

```python
# fileparse.py
import csv

def parse_csv(filename, select=None):
    '''
    Parse a CSV file into a list of records
    '''
    with open(filename) as f:
        rows = csv.reader(f)

        # Read the file headers
        headers = next(rows)

        # If a column selector was given, find indices of the specified columns.
        # Also narrow the set of headers used for resulting dictionaries
        if select:
            indices = [headers.index(colname) for colname in select]
            headers = select
        else:
            indices = []

        records = []
        for row in rows:
            if not row:    # Skip rows with no data
                continue
            # Filter the row if specific columns were selected
            if indices:
                row = [ row[index] for index in indices ]

            # Make a dictionary
            record = dict(zip(headers, row))
            records.append(record)

    return records
```

这部分有一些棘手的问题，最重要的一个可能是列选择到行索引的映射。例如，假设输入文件具有以下标题：

```python
>>> headers = ['name', 'date', 'time', 'shares', 'price']
>>>
```

现在，假设选定的列如下：

```python
>>> select = ['name', 'shares']
>>>
```

为了执行正确的选择，必须将选择的列名映射到文件中的列索引。这就是该步骤正在执行的操作：

```python
>>> indices = [headers.index(colname) for colname in select ]
>>> indices
[0, 3]
>>>
```

换句话说，名称（"name" ）是第 0 列，股份数目（"shares" ）是第 3 列。

当从文件读取数据行的时候，使用索引对其进行过滤：

```python
>>> row = ['AA', '6/11/2007', '9:50am', '100', '32.20' ]
>>> row = [ row[index] for index in indices ]
>>> row
['AA', '100']
>>>
```

### 练习 3.5：执行类型转换

修改 `parse_csv()` 函数，以便可以选择将类型转换应用到返回数据上。例如：

```python
>>> portfolio = parse_csv('Data/portfolio.csv', types=[str, int, float])
>>> portfolio
[{'price': 32.2, 'name': 'AA', 'shares': 100}, {'price': 91.1, 'name': 'IBM', 'shares': 50}, {'price': 83.44, 'name': 'CAT', 'shares': 150}, {'price': 51.23, 'name': 'MSFT', 'shares': 200}, {'price': 40.37, 'name': 'GE', 'shares': 95}, {'price': 65.1, 'name': 'MSFT', 'shares': 50}, {'price': 70.44, 'name': 'IBM', 'shares': 100}]

>>> shares_held = parse_csv('Data/portfolio.csv', select=['name', 'shares'], types=[str, int])
>>> shares_held
[{'name': 'AA', 'shares': 100}, {'name': 'IBM', 'shares': 50}, {'name': 'CAT', 'shares': 150}, {'name': 'MSFT', 'shares': 200}, {'name': 'GE', 'shares': 95}, {'name': 'MSFT', 'shares': 50}, {'name': 'IBM', 'shares': 100}]
>>>
```

在 [练习 2.24](../02_Working_with_data/07_Objects) 中已经对此进行了探索。需要将下列代码片段插入到题解中：

```python
...
if types:
    row = [func(val) for func, val in zip(types, row) ]
...
```

### 练习 3.6：处理无标题的数据

某些 CSV 文件不包含任何的标题信息。例如，`prices.csv` 文件看起来像下面这样：

```csv
"AA",9.22
"AXP",24.85
"BA",44.85
"BAC",11.27
...
```

修改 `parse_csv()` 文件以便通过创建元组列表来处理此类文件。例如：

```python
>>> prices = parse_csv('Data/prices.csv', types=[str,float], has_headers=False)
>>> prices
[('AA', 9.22), ('AXP', 24.85), ('BA', 44.85), ('BAC', 11.27), ('C', 3.72), ('CAT', 35.46), ('CVX', 66.67), ('DD', 28.47), ('DIS', 24.22), ('GE', 13.48), ('GM', 0.75), ('HD', 23.16), ('HPQ', 34.35), ('IBM', 106.28), ('INTC', 15.72), ('JNJ', 55.16), ('JPM', 36.9), ('KFT', 26.11), ('KO', 49.16), ('MCD', 58.99), ('MMM', 57.1), ('MRK', 27.58), ('MSFT', 20.89), ('PFE', 15.19), ('PG', 51.94), ('T', 24.79), ('UTX', 52.61), ('VZ', 29.26), ('WMT', 49.74), ('XOM', 69.35)]
>>>
```

要执行此更改，需要修改代码以便数据的第一行不被解释为标题行。另外，需要确保不创建字典，因为不再有可用于列名的键。

### 练习 3.7：选择其它的列分隔符

尽管 CSV 文件非常普遍，但还可能会遇到使用其它列分隔符（如 制表符（tab） 或空格符（space））的文件。例如，如下所示的 `Data/portfolio.dat` 文件：

```csv
name shares price
"AA" 100 32.20
"IBM" 50 91.10
"CAT" 150 83.44
"MSFT" 200 51.23
"GE" 95 40.37
"MSFT" 50 65.10
"IBM" 100 70.44
```

`csv.reader()` 函数允许像下面这样指定不同的分隔符：

```python
rows = csv.reader(f, delimiter=' ')
```

修改 `parse_csv()` 函数以便也允许修改分隔符。

例如：

```python
>>> portfolio = parse_csv('Data/portfolio.dat', types=[str, int, float], delimiter=' ')
>>> portfolio
[{'price': '32.20', 'name': 'AA', 'shares': '100'}, {'price': '91.10', 'name': 'IBM', 'shares': '50'}, {'price': '83.44', 'name': 'CAT', 'shares': '150'}, {'price': '51.23', 'name': 'MSFT', 'shares': '200'}, {'price': '40.37', 'name': 'GE', 'shares': '95'}, {'price': '65.10', 'name': 'MSFT', 'shares': '50'}, {'price': '70.44', 'name': 'IBM', 'shares': '100'}]
>>>
```

### 说明

到目前为止，如果您已经完成，那么您创建了一个非常有用的库函数。您可以使用它去解析任意的 CSV 文件，选择感兴趣的列，执行类型转换，而不用对文件或者 `csv` 模块的内部工作有太多的担心。

[目录](../Contents.md) \| [上一节 (3.1 脚本)](01_Script.md) \| [下一节 (3.3 错误检查)](03_Error_checking.md)