[目录](../Contents.md) \| [上一节 (3.6 设计讨论)](../03_Program_organization/06_Design_discussion.md) \| [下一节 (4.2 继承)](02_Inheritance.md)

# 4.1 类

本节介绍 class 语句以及创建新对象的方式。

### 面向对象编程（OOP）

面向对象编程是一种将代码组织成对象集合的编程技术。

一个对象包括：

* 数据。属性
* 行为。方法——应用于对象的函数。

在本课程中，你已经使用了面向对象编程技术。

例如，操作列表。

```python
>>> nums = [1, 2, 3]
>>> nums.append(4)      # Method
>>> nums.insert(1,10)   # Method
>>> nums
[1, 10, 2, 3, 4]        # Data
>>>
```

`nums` 是列表的实例（*instance*）。

方法（`append()` 和 `insert()`）被绑定到实例（`nums`）上。

### `class` 语句

使用 `class` 语句定义一个新的对象。

```python
class Player:
    def __init__(self, x, y):
        self.x = x
        self.y = y
        self.health = 100

    def move(self, dx, dy):
        self.x += dx
        self.y += dy

    def damage(self, pts):
        self.health -= pts
```

简而言之，类是一组函数，对所谓的 *实例（instance）* 执行各种操作。

### 实例

实例是你在程序中操作的实际对象。

通过像调用函数一样调用类来创建实例。

```python
>>> a = Player(2, 3)
>>> b = Player(10, 20)
>>>
```

`a` 和 `b` 都是 `Player` 类的实例。

*强调：class 语句仅仅是一个定义（它本身不执行任何操作）。类似于函数定义。*

### 实例数据

每个实例都拥有自己的局部数据。

```python
>>> a.x
2
>>> b.x
10
```

数据通过 `_init__()` 方法进行初始化。

```python
class Player:
    def __init__(self, x, y):
        # Any value stored on `self` is instance data
        self.x = x
        self.y = y
        self.health = 100
```

对属性的总数或者类型没有限制。

### 实例方法

应用于对象实例的函数称为实例方法。

```python
class Player:
    ...
    # `move` is a method
    def move(self, dx, dy):
        self.x += dx
        self.y += dy
```

对象本身始终作为第一个参数传递。

```python
>>> a.move(1, 2)

# matches `a` to `self`
# matches `1` to `dx`
# matches `2` to `dy`
def move(self, dx, dy):
```

按照惯例，实例称为 `self`。但是，使用的实际名字不重要。对象始终作为第一个参数传递。将这个参数称为 `self` 只是 Python 的编程风格。

### 类作用域

类未定义名称的作用域。

```python
class Player:
    ...
    def move(self, dx, dy):
        self.x += dx
        self.y += dy

    def left(self, amt):
        move(-amt, 0)       # NO. Calls a global `move` function
        self.move(-amt, 0)  # YES. Calls method `move` from above.
```

如果想要对实例进行操作，那么你始终需要显式地引用它（如： `self`）。

## 练习

从本组练习开始，我们将对前面章节的现有代码进行一系列更改。从练习 3.18 版本的代码开始非常重要。如果你还没有这些代码，请到 `Solutions/3_18`  目录下查看，然后复制它。

### 练习 4.1：把对象当做数据结构

在第 2 和第 3 节中，我们使用了了以元组和字典表示的数据。例如，持有的股票可以用像下面这样的元组表示：

```python
s = ('GOOG',100,490.10)
```

或者使用像下面这样的字典表示：

```python
s = { 'name'   : 'GOOG',
      'shares' : 100,
      'price'  : 490.10
}
```

你甚至可以编写用于操作此类数据的函数。例如：

```python
def cost(s):
    return s['shares'] * s['price']
```

但是，随着程序规模的不断扩大，你可能希望创建更好的代码组织意识（sense）。因此，可以定义一个类表示数据。请创建一个名为 `stock.py` 的文件，并定义一个名为 `Stock` 的类，用以表示持有的单支股票。`Stock`  类具有 `name`, `shares`，和 `price` 属性。示例：

```python
>>> import stock
>>> a = stock.Stock('GOOG',100,490.10)
>>> a.name
'GOOG'
>>> a.shares
100
>>> a.price
490.1
>>>
```

创建更多的  `Stock`  对象并对其进行操作。示例：

```python
>>> b = stock.Stock('AAPL', 50, 122.34)
>>> c = stock.Stock('IBM', 75, 91.75)
>>> b.shares * b.price
6117.0
>>> c.shares * c.price
6881.25
>>> stocks = [a, b, c]
>>> stocks
[<stock.Stock object at 0x37d0b0>, <stock.Stock object at 0x37d110>, <stock.Stock object at 0x37d050>]
>>> for s in stocks:
     print(f'{s.name:>10s} {s.shares:>10d} {s.price:>10.2f}')

... look at the output ...
>>>
```

需要强调的一点是，在这里， `Stock` 类充当创建实例对象的工厂。基本上，你可以像调用函数一样调用类为你创建新对象。另外，必须强调的是，每一个对象都是不同的——它们拥有各自的数据，这些数据与以创建的其它对象是分开的。

某种程度上，通过类定义的对象与字典类似——只是使用颇为不同的语法。例如，使用的是 `s.name` 和 `s.price`，而不是 `s['name']` 和 `s['price']`。

### 练习 4.2：添加方法

拥有对象后，你可以添加方法到对象上。众所皆知，方法就是对存储在对象内部的数据进行操作的函数。请给 `Stock` 对象添加 `cost()` 和 `sell()` 方法。它们应该像下面这样工作：

```python
>>> import stock
>>> s = stock.Stock('GOOG', 100, 490.10)
>>> s.cost()
49010.0
>>> s.shares
100
>>> s.sell(25)
>>> s.shares
75
>>> s.cost()
36757.5
>>>
```

### 练习 4.3：创建实例列表

尝试执行以下步骤，从列表字典中创建  Stock 的实例列表。然后计算总费用：

```python
>>> import fileparse
>>> with open('Data/portfolio.csv') as lines:
...     portdicts = fileparse.parse_csv(lines, select=['name','shares','price'], types=[str,int,float])
...
>>> portfolio = [ stock.Stock(d['name'], d['shares'], d['price']) for d in portdicts]
>>> portfolio
[<stock.Stock object at 0x10c9e2128>, <stock.Stock object at 0x10c9e2048>, <stock.Stock object at 0x10c9e2080>,
 <stock.Stock object at 0x10c9e25f8>, <stock.Stock object at 0x10c9e2630>, <stock.Stock object at 0x10ca6f748>,
 <stock.Stock object at 0x10ca6f7b8>]
>>> sum([s.cost() for s in portfolio])
44671.15
>>>
```

### 练习 4.4：使用类

请修改  `report.py` 程序里面的 `read_portfolio()` 函数，以便如练习 4.3 所示那样，读取股票投资组合到 `Stock` 的实例列表里面。修改完后，修复（fix）`report.py` 和 `pcost.py` 里面所有的代码，以便使用 `Stock`  的实例进行工作，而不是使用字典。

提示：你不必对代码进行大量更改，主要是将字典访问，如 `s['shares']` 更改为 `s.shares`。

修改完后应该能够像之前一样运行函数：

```python
>>> import pcost
>>> pcost.portfolio_cost('Data/portfolio.csv')
44671.15
>>> import report
>>> report.portfolio_report('Data/portfolio.csv', 'Data/prices.csv')
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

[目录](../Contents.md) \| [上一节 (3.6 设计讨论)](../03_Program_organization/06_Design_discussion.md) \| [下一节 (4.2 继承)](02_Inheritance.md)