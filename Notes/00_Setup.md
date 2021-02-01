# 课程设置与概述

欢迎访问本课程（Practical Python Programming）。这个页面包含一些关于课程设置的重要信息。

## 课程周期和时间要求

该课程最初是作为一个由讲师主导的，持续 3 -4 天的现场培训。要全部完成本课程，您应该最少计划用25-35小时进行学习。大部分的参与者发现，在不浏览题解代码的情况下，学习材料是相当具有挑战性的（见下文）

## 设置和 Python 安装

您只需要安装 3.6 或者更新版本的 Python 即可。不依赖于任何特定的操作系统，编辑器，IDE，或者其它与 Python 相关的工具。没有第三方依赖。

也就是说，本课程大部分内容涉及学习如何编写脚本与小型程序，这些脚本与小型程序涉及从文件中读取数据。因此，您需要确保您处在一个可以轻松处理文件的环境中。这包括使用编辑器创建 Python 程序，并能够从 shell 或终端运行这些程序。

您可能倾向于使用更具交互性的环境来学习本课程，例如 Jupyter Notebooks。**我建议不要这样做**。尽管 Jupyter Notebooks 非常棒，但本课程中的许多练习教授与程序组织的相关的观念，包括使用函数，模块，导入语句以及重构源代码跨越多个文件的程序。以我的经验，很难在  Jupyter Notebooks 环境中重复这样的环境。

## 派生（Forking）/克隆（Cloning） 课程仓库

为了准备本课程的环境，我推荐您从本课程的仓库 [https://github.com/dabeaz-course/practical-python](https://github.com/dabeaz-course/practical-python) 派生您自己的 GitHub 仓库。完成后，您可以将其克隆到本地计算机上：

```
bash % git clone https://github.com/yourname/practical-python
bash % cd practical-python
bash %
```

请在 `practical-python/` 目录下完成所有的练习。如果将解题代码提交回派生的仓库，那么您的所有代码会保存到一个地方。完成后，您将拥有良好的学习记录。

如果您不想派生一个自己的 GitHub 仓库或者您没有 GitHub 账号，您仍然可以将本课程的仓库克隆到您自己的计算机上：

```
bash % git clone https://github.com/dabeaz-course/practical-python
bash % cd practical-python
bash %
```

如果这样做，除了对计算机的本地副本进行更改外，您将无法提交代码更改到 GitHub 上。

## 课程排版

在 Work/ 目录下完成所有的编程工作。在Work/ 目录里面，有一个 Data/ 目录。 Data/ 目录包含各类在课程中使用的数据文件及其它脚本。您将会经常访问位于 Data/ 目录下的文件。课程练习假定您在 Work/ 目录下创建程序。

## 课程顺序

课程材料应该从第 1 节开始，按章节顺序完成。后面章节中的课程练习以前面章节中编写的代码为基础。后面的许多练习涉及对现有代码的微小重构。

## 题解代码

[`Solutions/`](../Solutions) 目录包含选定练习的题解代码。如果您需要一些提示，请随时查看。为了最大限度地利用课程，您应该首先尝试创建自己的题解。

[目录](Contents.md) \| [下一节 (1 Python 简介)](01_Introduction/00_Overview.md)









