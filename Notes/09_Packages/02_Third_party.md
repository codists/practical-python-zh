[目录](../Contents.md) \| [上一节 (9.1 包)](01_Packages.md) \| [下一节 (9.3 版本分发)](03_Distribution.md)

# 9.2 第三方模块

Python 拥有一个包含各种内置模块的大型库（*自带电池(batteries included)*）（译注：“自带电池”来自于官方文档的翻译，意为“功能齐全”）。

甚至还有很多第三方模块（可以使用）。请到  [Python 包索引（ PyPi ）](https://pypi.org/) 查看，或者使用谷歌搜索特定主题。

对于 Python 而言，如何处理第三方依赖关系是一个不断演化的主题。本节仅仅涵盖一些基础知识，帮助你了解它们是如何工作的。

### 模块搜索路径

`sys.path`  是一个列表，包含所有 `import` 语句要检查的目录。查看 `sys.path` ：

```python
>>> import sys
>>> sys.path
... look at the result ...
>>>
```

如果导入的内容不在目录中。那么将会触发 `ImportError` 异常。

### 标准库模块

Python 标准库中的模块通常来自于 `/usr/local/lib/python3.6' 之类的位置。可以通过一个简短的测试来确定模块来自于哪里：

```python
>>> import re
>>> re
<module 're' from '/usr/local/lib/python3.6/re.py'>
>>>
```

在交互式解释器（REPL）中查看模块的位置是一个很好的调试技巧。交互式解释器将会显示模块所在的位置。

### 第三方模块

第三方模块通常位于专门的目录 `site-packages` 中。如果执行与上述相同的步骤，可以看到第三方模块所在的位置：

```python
>>> import numpy
>>> numpy
<module 'numpy' from '/usr/local/lib/python3.6/site-packages/numpy/__init__.py'>
>>>
```

同样，如果要弄清楚导入的第三方模块为什么没有像预期那样工作，那么查看模块的位置是一个很好的调试技巧。

### 安装模块

安装第三方模块最常用的技术是使用 `pip`。示例：

```bash
bash % python3 -m pip install packagename
```

此命令会下载包并将包安装到 `site-packages` 目录中。

### 问题

* 你可能正在使用你不能直接控制的 Python。
  * 企业认可的 Python
  * 操作系统自带的 Python
* 你可能没有权限在计算机中安装全局包
* 可能还有其它依赖

### 虚拟环境

对于包安装问题，一个常见的解决方案是为自己创建所谓的“虚拟环境”。自然，创建虚拟环境的方式不止这一种——实际上，有好几种相互竞争的工具和技术。如果你正在使用标准方式安装的 Python，可以尝试输入以下代码创建虚拟环境：

```bash
bash % python -m venv mypython
bash %
```

稍等片刻后，你将拥有一个新目录 `mypython`，该目录安装了你自己的 Python。在 `mypython` 目录中，可以找到 `bin/` 目录（Unix 系统）或者 `Scripts/` 目录（Windows 系统）。运行 `bin/` 目录下的 `activate` 脚本会“激活“ 对应的 Python，使其成为 shell 的默认 `python` 命令。示例：

```bash
bash % source mypython/bin/activate
(mypython) bash %
```

现在，你可以从这里开始安装自己的 Python 包了。示例：

```
(mypython) bash % python -m pip install pandas
...
```

通常，为了试验和试用不同的包，可以使用虚拟环境。另一方面，如果你正在创建一个应用并且它有特定的包依赖关系，那么问题会稍有不同。

### 处理应用中的第三方依赖

如果你已经编写了一个应用，并且该应用具有特定的第三方依赖，那么创建并保存一个包括你自己的代码及其依赖的环境将会是一个挑战。可悲的是，这一直是一个非常混乱和频繁变化的领域。即使到现在，它仍在继续演变。

我不想向你提供必定会过时的信息，建议查阅 [Python 打包用户指南](https://packaging.python.org) 。

## 练习

### 练习 9.4：创建虚拟环境

看看你是否可以像上面一样创建一个虚拟环境并在虚拟环境中安装 pandas。

[目录](../Contents.md) \| [上一节 (9.1 包)](01_Packages.md) \| [下一节 (9.3 版本分发)](03_Distribution.md)






