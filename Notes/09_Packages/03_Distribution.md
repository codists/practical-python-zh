[目录](../Contents.md) \| [上一节 (9.2 第三方包)](02_Third_party.md) \| [下一节 (完结)](TheEnd.md)

# 9.3 Distribution版本分发

在某些时候，你可能想要将自己的代码提供给其他人——可能只是同事（使用）。本节给出执行此操作的最基本技术。更多详细信息，请参考 [Python 打包用户指南](https://packaging.python.org)。

### 创建 setup.py 文件

请添加一个  `setup.py` 到项目目录的顶层。

```python
# setup.py
import setuptools

setuptools.setup(
    name="porty",
    version="0.0.1",
    author="Your Name",
    author_email="you@example.com",
    description="Practical Python Code",
    packages=setuptools.find_packages(),
)
```

### 创建 MANIFEST.in 文件

如果有其它文件与你的项目相关联，请使用一个 `MANIFEST.in` 文件指定这些关联的文件。示例：

```
# MANIFEST.in
include *.csv
```

请将 `MANIFEST.in` 文件放到 `setup.py` 所在的目录。

### 创建源码发行版

要创建源码发行版，请使用 `setup.py` 文件。示例：

```
bash % python setup.py sdist
```

这将在 `dist/` 目录中创建 `.tar.gz` 或者 `.zip` 文件。该文件就是你要提供给其他人使用的文件。

### 安装你的代码

其他人可以使用 `pip`  像安装其它软件包一样安装你的 Python 代码。他们仅仅需要提供在之前的步骤中创建的文件即可。示例：

```
bash % python -m pip install porty-0.0.1.tar.gz
```

### 说明

上面的步骤描述了创建 Python 代码包的最基本的知识，您可以将这些代码包提供给其他人使用。实际上，根据第三方的依赖关系，它可能要复杂得多，无论你的应用是否包含外部代码(例如 C 或 C++)。这已经超出了本课程的范围，我们只作初步了解。

## 练习

### 练习 9.5：创建软件包

使用练习 9.3 中创建的 `porty-app/` 代码，看看是否可以重新创建此前描述的步骤。具体来说，添加一个 `setup.py` 文件和一个 `MANIFEST.in` 文件到顶级目录中。然后通过运行 `python setup.py sdist` 创建源码发行版。

最后，看看是否可以在 Python 虚拟环境中安装你的软件包。

[目录](../Contents.md) \| [上一节 (9.2 第三方包)](02_Third_party.md) \| [下一节 (完结)](TheEnd.md)






