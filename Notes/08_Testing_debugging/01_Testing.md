[目录](../Contents.md) \| [上一节 (7.5 装饰方法](../07_Advanced_Topics/05_Decorated_methods.md) \| [下一节 (8.2 日志)](02_Logging.md)

# 8.1 测试

## 多测试，少调试（Testing Rocks, Debugging Sucks）

Python 的动态性质使得测试对大多数程序而言至关重要。编译器不会发现你的 bug，发现 bug 的唯一方式是运行代码，并确保尝试了所有的特性。

## 断言（Assertions）

`assert` 语句用于程序的内部检查。如果表达式不为真，则会触发 `AssertionError` 异常。

`assert` 语句语法：

```python
assert <expression> [, 'Diagnostic message']
```

示例：

```python
assert isinstance(10, int), 'Expected int'
```

 `assert` 语句不应用于检查用户的输入（例如，在网页表单输入的数据）。 `assert` 语句旨在用于内部检查或者用于不变量（invariant，始终为 True 的条件）。

### 契约式编程

契约式编程（contract programming）也称为契约式设计（Design By Contract），自由使用断言是一种软件设计方法。契约式编程规定软件设计人员应该为软件组件定义精确的接口规范。

例如，你可以在所有的函数输入中使用断言：

```python
def add(x, y):
    assert isinstance(x, int), 'Expected int'
    assert isinstance(y, int), 'Expected int'
    return x + y
```

如果函数调用者没有使用正确的参数，那么检查输入可以立即捕捉到。

```python
>>> add(2, 3)
5
>>> add('2', '3')
Traceback (most recent call last):
...
AssertionError: Expected int
>>>
```

### 内联测试

断言也可以用于简单的测试。

```python
def add(x, y):
    return x + y

assert add(2,2) == 4
```

这样，你就可以将测试与代码包含在同一模块中。

*好处：如果代码明显被破坏，那么尝试导入模块将会导致程序崩溃。*

对于详尽的测试，不推荐这样做。这种做法更像是基本的“冒烟测试（smoke test）”。函数是否可以在所有的用例上正常工作？如果不可以，那么肯定是有问题的。

### `unittest` 模块

假设你有下面这样一段代码：

```python
# simple.py

def add(x, y):
    return x + y
```

现在，你想对这些代码进行测试，请创建一个单独的测试文件，如下所示：

```python
# test_simple.py

import simple
import unittest
```

然后定义一个测试类：

```python
# test_simple.py

import simple
import unittest

# Notice that it inherits from unittest.TestCase
class TestAdd(unittest.TestCase):
    ...
```

测试类必须继承自`unittest.TestCase`。

在测试类中，定义测试方法：

```python
# test_simple.py

import simple
import unittest

# Notice that it inherits from unittest.TestCase
class TestAdd(unittest.TestCase):
    def test_simple(self):
        # Test with simple integer arguments
        r = simple.add(2, 2)
        self.assertEqual(r, 5)
    def test_str(self):
        # Test with strings
        r = simple.add('hello', 'world')
        self.assertEqual(r, 'helloworld')
```

*重要提示：每个方法的名称必须以 `test` 开头。*

### 使用 `unittest` 

`unittest` 中内置了一些断言，每种断言对不同的事情进行诊断。

```python
# Assert that expr is True
self.assertTrue(expr)

# Assert that x == y
self.assertEqual(x,y)

# Assert that x != y
self.assertNotEqual(x,y)

# Assert that x is near y
self.assertAlmostEqual(x,y,places)

# Assert that callable(arg1,arg2,...) raises exc
self.assertRaises(exc, callable, arg1, arg2, ...)
```

上述列表并不是一个完整的列表，`unittest` 模块还有其它断言。

### 运行 `unittest`

要运行测试，请把代码转换为脚本。

```python
# test_simple.py

...

if __name__ == '__main__':
    unittest.main()
```

然后使用 Python 执行测试文件：

```bash
bash % python3 test_simple.py
F.
========================================================
FAIL: test_simple (__main__.TestAdd)
--------------------------------------------------------
Traceback (most recent call last):
  File "testsimple.py", line 8, in test_simple
    self.assertEqual(r, 5)
AssertionError: 4 != 5
--------------------------------------------------------
Ran 2 tests in 0.000s
FAILED (failures=1)
```

### 说明

高效的单元测试是一种艺术。对于大型应用而言，单元测试可能会变得非常复杂。

`unittest` 模块具有大量与测试运行器（test runners），测试结果集（collection of results）以及测试其他方面相关的选项。相关详细信息，请查阅文档。

### 第三方测试工具

虽然内置 `unittest` 模块的优势是可以随处使用——因为它是 Python 的一部分，但是许多程序员也觉得 `unittest` 非常繁琐。另一个流行的的测试工具是 [pytest](https://docs.pytest.org/en/latest/)。使用 pytest，测试文件可以简化为以下形式：

```python
# test_simple.py
import simple

def test_simple():
    assert simple.add(2,2) == 4

def test_str():
    assert simple.add('hello','world') == 'helloworld'
```

要运行测试，只需要输入一个命令即可，例如：`python -m pytest` 。它将会发现所有的测试并运行这些测试。

除了这个示例之外，`pytest` 还有很多内容。如果你决定尝试一下，通常很容易上手。

## 练习

在本次练习中，我们将探索使用 Python `unittest` 模块的基本机制（mechanics）。

在前面的练习中，我们编写了一个包含 `Stock` 类的 `stock.py` 文件。对于本次练习，假设我们使用的是 [练习7.9](../07_Advanced_Topics/03_Returning_functions) 中编写的与类型化属性相关的代码（译注：`typedproperty.py`）。如果因为某些原因，练习 7.9 的代码无法正常工作，你可以从 `Solutions/7_9` 中复制 `typedproperty.py` 到工作目录中。

### 练习 8.1：编写单元测试

请创建一个单独的 `test_stock.py` 文件，为 `Stock` 编写单元测试集。为了让你入门，这里有一小段测试实例创建的代码：


```python
# test_stock.py

import unittest
import stock

class TestStock(unittest.TestCase):
    def test_create(self):
        s = stock.Stock('GOOG', 100, 490.1)
        self.assertEqual(s.name, 'GOOG')
        self.assertEqual(s.shares, 100)
        self.assertEqual(s.price, 490.1)

if __name__ == '__main__':
    unittest.main()
```

运行单元测试，你应该可以获得一些像下面这有的输出：

```
.
----------------------------------------------------------------------
Ran 1 tests in 0.000s

OK
```

然后，编写其它单元测试来检查以下各项内容：

* 确保 `s.cost` 属性返回正确的值（49010.0）。
* 确保 `s.sell()` 方法正常工作。它应该相应地减小 `s.shares`。
* 确保 `s.shares`  属性只能设置为整数值。

对于最后一部分，你需要检查异常的触发。要做到这些，一种简单的方法是使用如下代码：

```python
class TestStock(unittest.TestCase):
    ...
    def test_bad_shares(self):
         s = stock.Stock('GOOG', 100, 490.1)
         with self.assertRaises(TypeError):
             s.shares = '100'
```

[目录](../Contents.md) \| [上一节 (7.5 装饰方法](../07_Advanced_Topics/05_Decorated_methods.md) \| [下一节 (8.2 日志)](02_Logging.md)