[目录](../Contents.md) \| [上一节 (7.3 返回函数)](03_Returning_functions.md) \| [下一节 (7.5 装饰方法)](05_Decorated_methods.md)

# 7.4 函数装饰器

本节介绍装饰器（decorator）。因为这是一个高级主题，所以我们只做简单介绍。

译注：根据译者个人的猜测，在《设计模式》(《 Design Patterns: Elements of Reusable Object-Oriented Software》)一书中写到 `decorator also known as wrapper`，所以下文提到包装器（wrapper），其实说的就是装饰器。这里为了保持和原文一致，所以翻译的时候没有将“包装器”替换为“装饰器”。

### 日志示例

考虑这样一个函数：

```python
def add(x, y):
    return x + y
```

考虑给 `add(x, y)` 函数添加日志功能：

```python
def add(x, y):
    print('Calling add')
    return x + y
```

也带有日志功能的 `sub(x, y)`函数：

```python
def sub(x, y):
    print('Calling sub')
    return x - y
```

### 观察

*观察： 这是一种重复。*

在有大量重复代码的地方编写程序通常很烦人。这些代码不仅写起来枯燥，维护起来也很麻烦。尤其是你决定更改其工作方式的时候（例如，可能是另一种类型的日志记录）。

### 记录日志的代码

也许你可以创建一个添加了日志功能的函数。例如包装器（wrapper）：

```python
def logged(func):
    def wrapper(*args, **kwargs):
        print('Calling', func.__name__)
        return func(*args, **kwargs)
    return wrapper
```

使用该函数：

```python
def add(x, y):
    return x + y

logged_add = logged(add)
```

当调用 `logged` 返回的函数时会发生什么？

```python
logged_add(3, 4)      # You see the logging message appear
```

此示例阐明了创建所谓的*包装器函数（wrapper function）* 的过程。

包装器是一个函数，它包装了另一个带有额外处理功能的函数，但在其它方面与原始函数的工作方式完全相同。

```python
>>> logged_add(3, 4)
Calling add   # Extra output. Added by the wrapper
7
>>>
```

注意事项：`logged()`  函数创建了一个包装器，并作为结果返回。

## 装饰器

在 Python 中，在函数中使用包装器非常常见。因为如此普遍，所以有一个特殊的语法。

```python
def add(x, y):
    return x + y
add = logged(add)

# Special syntax
@logged
def add(x, y):
    return x + y
```

该特殊语法执行与上面完全相同的确切步骤。装饰器只是一种新语法，用于装饰函数。

### 说明

对于装饰器而言，还有许多比这里展示的更微妙的细节，例如，在类里面使用装饰器，或者对同一个函数使用多个装饰器。不过，这里的例子已经很好的说明了如何使用它们。一般而言，它是对出现在各种函数定义中的重复代码的响应。装饰器可以将重复代码移至中心定义。

## 练习

### 练习 7.10：计时装饰器

如果你定义了一个函数，那么函数的名称和函数所属模块的名称会分别存储到 `__name__` 和 `__module__`属性中。示例：

```python
>>> def add(x,y):
     return x+y

>>> add.__name__
'add'
>>> add.__module__
'__main__'
>>>
```

请创建 `timethis.py` 文件，并在文件中编写 `timethis(func)` 函数。`timethis(func)` 函数包装一个具有额外逻辑层的函数，逻辑层打印出函数执行所需要的事件。为此，你将在函数中添加如下计时调用。

```python
start = time.time()
r = func(*args,**kwargs)
end = time.time()
print('%s.%s: %f' % (func.__module__, func.__name__, end-start))
```

（`timethis(func)`）装饰器工作方式示例：

```python
>>> from timethis import timethis
>>> @timethis
def countdown(n):
        while n > 0:
             n -= 1

>>> countdown(10000000)
__main__.countdown : 0.076562
>>>
```

讨论：`@timethis` 装饰器可以放在任何函数的前面，即你应该把装饰器用作性能调优（performance tuning）的诊断工具。

[目录](../Contents.md) \| [上一节 (7.3 返回函数)](03_Returning_functions.md) \| [下一节 (7.5 装饰方法)](05_Decorated_methods.md)