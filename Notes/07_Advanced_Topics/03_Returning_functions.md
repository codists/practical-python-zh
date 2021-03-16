[目录](../Contents.md) \| [上一节 (7.2 匿名函数)](02_Anonymous_function.md) \| [下一节 (7.4 装饰器)](04_Function_decorators.md)

# 7.3 返回函数

本节介绍使用函数创建其它函数的思想。

### 简介

考虑以下函数：

```python
def add(x, y):
    def do_add():
        print('Adding', x, y)
        return x + y
    return do_add
```

这是返回其它函数的函数。

```python
>>> a = add(3,4)
>>> a
<function do_add at 0x6a670>
>>> a()
Adding 3 4
7
```

### 局部变量

请观察内部函数是如何引用外部函数定义的变量的。

```python
def add(x, y):
    def do_add():
        # `x` and `y` are defined above `add(x, y)`
        print('Adding', x, y)
        return x + y
    return do_add
```

进一步观察会发现，在 `add()` 函数结束后，这些变量仍然保持存活。

```python
>>> a = add(3,4)
>>> a
<function do_add at 0x6a670>
>>> a()
Adding 3 4      # Where are these values coming from?
7
```

### 闭包

当内部函数作为结果返回时，该内部函数称为闭包（closure）。

```python
def add(x, y):
    # `do_add` is a closure
    def do_add():
        print('Adding', x, y)
        return x + y
    return do_add
```

基本特性：闭包保留该函数以后正常运行所需的所有变量的值。可以将闭包视作一个函数，该函数拥有一个额外的环境来保存它所依赖的变量的值。

### 使用闭包

虽然闭包是 Python 的基本特性，但是它们的用法通常很微妙。常见应用：

* 在回调函数中使用。
* 延迟计算。
* 装饰器函数（稍后介绍）。

### 延迟计算

考虑这样的函数：

```python
def after(seconds, func):
    import time
    time.sleep(seconds)
    func()
```

使用示例：

```python
def greeting():
    print('Hello Guido')

after(30, greeting)
```

`after` （延迟30 秒后）执行给定的函数......

闭包附带了其它信息。

```python
def add(x, y):
    def do_add():
        print(f'Adding {x} + {y} -> {x+y}')
    return do_add

def after(seconds, func):
    import time
    time.sleep(seconds)
    func()

after(30, add(2, 3))
# `do_add` has the references x -> 2 and y -> 3
```

### 代码重复

闭包也可以用作一种避免代码大量重复的技术。

## 练习

### 练习 7.7：使用闭包避免重复

闭包的一个更强大的特性是用于生成重复的代码。让我们回顾 [练习 5.7](../05_Object_model/02_Classes_encapsulation) 代码，该代码中定义了带有类型检查的属性：

```python
class Stock:
    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
    ...
    @property
    def shares(self):
        return self._shares

    @shares.setter
    def shares(self, value):
        if not isinstance(value, int):
            raise TypeError('Expected int')
        self._shares = value
    ...
```

与其一遍又一遍地输入代码，不如使用闭包自动创建代码。

请创建 `typedproperty.py` 文件，并把下述代码放到文件中：

```python
# typedproperty.py

def typedproperty(name, expected_type):
    private_name = '_' + name
    @property
    def prop(self):
        return getattr(self, private_name)

    @prop.setter
    def prop(self, value):
        if not isinstance(value, expected_type):
            raise TypeError(f'Expected {expected_type}')
        setattr(self, private_name, value)

    return prop
```

现在，通过定义下面这样的类来尝试以下：

```python
from typedproperty import typedproperty

class Stock:
    name = typedproperty('name', str)
    shares = typedproperty('shares', int)
    price = typedproperty('price', float)

    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
```

请尝试创建一个实例，并验证类型检查是否有效：

```python
>>> s = Stock('IBM', 50, 91.1)
>>> s.name
'IBM'
>>> s.shares = '100'
... should get a TypeError ...
>>>
```

### 练习 7.8：简化函数调用

在上面示例中，用户可能会发现调用诸如 `typedproperty('shares', int)` 这样的方法稍微有点冗长 ——尤其是多次重复调用的时候。请将以下定义添加到 `typedproperty.py` 文件中。

```python
String = lambda name: typedproperty(name, str)
Integer = lambda name: typedproperty(name, int)
Float = lambda name: typedproperty(name, float)
```

现在，请重新编写  `Stock`  类以使用以下函数：

```python
class Stock:
    name = String('name')
    shares = Integer('shares')
    price = Float('price')

    def __init__(self, name, shares, price):
        self.name = name
        self.shares = shares
        self.price = price
```

啊，好一点了。这里的要点是：闭包和 `lambda` 常用于简化代码，并消除令人讨厌的代码重复。这通常很不错。

### 练习 7.9：付诸实践

请重新编写  `stock.py` 文件中的 `Stock` 类，以便使用上面展示的类型化特性（typed properties）。

[目录](../Contents.md) \| [上一节 (7.2 匿名函数)](02_Anonymous_function.md) \| [下一节 (7.4 装饰器)](04_Function_decorators.md)