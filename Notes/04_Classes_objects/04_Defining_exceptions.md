[目录](../Contents.md) \| [上一节 (4.3 特殊方法)](03_Special_methods.md) \| [下一节 (5 对象模型)](../05_Object_model/00_Overview.md)

# 4.4 定义异常

用户可以通过类实现自定义异常：

```python
class NetworkError(Exception):
    pass
```

**异常类始终继承自 `Exception` **

它们通常是空类。空类内部使用 `pass`  表示。

你也可以对异常进行分层：

```python
class AuthenticationError(NetworkError):
     pass

class ProtocolError(NetworkError):
    pass
```

## 练习

### 练习 4.11：自定义异常

通常情况下，为库定义自己的异常是一种良好的习惯。

这样可以更容易区分异常是常见编程错误触发的，还是库为了提示特定问题而有意触发的。

请修改上次练习中的 `create_formatter()` 函数，当用户提供错误的格式名时，触发自定义的 `FormatError` 异常。

示例：

```python
>>> from tableformat import create_formatter
>>> formatter = create_formatter('xls')
Traceback (most recent call last):
  File "<stdin>", line 1, in <module>
  File "tableformat.py", line 71, in create_formatter
    raise FormatError('Unknown table format %s' % name)
FormatError: Unknown table format xls
>>>
```

[目录](../Contents.md) \| [上一节 (4.3 特殊方法)](03_Special_methods.md) \| [下一节 (5 对象模型)](../05_Object_model/00_Overview.md)