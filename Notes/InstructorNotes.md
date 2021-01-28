# 实用的 Python 编程——讲师说明

作者:戴维.比兹利（David Beazley） 

## 概述

本文档提供一些有关“实用 Python” 教学的一般说明和建议，包括课程目标、课程受众、棘手的内容等。

一些人员会在一个典型的、为期三天的公司培训中进行教学，本说明就是提供给这些教学人员的。在如何教授自己的课程方面，这些说明可能会给您带来一些启发。

## 目标受众和一般方法

本课程旨在作为已经有一定的编程经验人员的“Python 入门”课程。这绝对不是一门为编程新手而设计的课程。

话虽如此，我发现 Python 课程中的学生也不大可能是硬核软件工程师或者程序员。相反，您可能会遇到工程师，科学家, web 程序员以及经验不足的开发者。学生背景差异很大。有些学生可能具有丰富的 C,C++, Java 经验，有些学生可能了解 PHP 和 HTML，有些学生可能掌握的是诸如 MATLAB 这样的工具，尽管我已经尽了最大努力把课程的先决条件说清楚，但是有些学生可能仍然几乎没有传统的"编程"经验。

考虑到这一点，本课程旨在通过操作数据（尤其是股市数据）的一般问题来教 Python。之所以选择这个领域是因为它很简单，并且无论每个人的背景如何，都应该对此有所了解。举个例子，编程能力很较弱的学生仍然可能要了解一些常用的东西，例如使用电子表格（Excel）。所以，如果他们真的被卡住了，您可以像这样告诉他们“元组的就像电子表格里面的数据行”或者“列表的操作和电子表格的列进行操作并且把结果放到新的一列中类似。”关键思想是扎根于现实世界环境中，而不是偏离到深奥的“计算机科学”问题上（例如：计算斐波那契数列）。

这个领域的问题也可以很好的引入其它编程主题。例如，科学家/工程师可能想了解数据分析或者数据可视化，所以您可以给他们展示如果使用 matplotlib 绘图。Web 程序员可能想要了解如何在网页展示股市数据，所以，您可以讨论模板引擎。系统管理员可能想使用日志文件做一些事情，所以，您可以引导他们查看实时的股票数据流日志文件。软件工程师可能想了解设计，所以，您可以让他们看看将股票数据封装到对象内部或者使程序可扩展的方法（例如，如何让程序以 10 种不同的表格形式产生输出）。你懂的。

## 演示指南

演示幻灯片（notes）为课程提供了叙述结构，供学生练习时参考。加入学生能够阅读那些幻灯片并且他们写代码时有时间回头查看，那么就没必要一字不落的讲解。我倾向于快速地浏览幻灯片，并在前进的过程中展示一些简短的例子。我经常跳过这些幻灯片，进行现场演示。例如，您真的不需要那么多关于列表（list）的幻灯片，而是走向解释器，现场执行一些列表示例即可。经验法则：每张幻灯片的时间不超过 1 分钟，除非是特别棘手的内容。老实说，如果您觉得适合自己的话，可以跳过大部分的幻灯片，而只使用实时演示讲课。我经常这样做。

## 课程练习

本课程大约有 130 个动手练习。如果您进行每一项练习，并让学生有时间思考和写代码，则可能需要大约 10-12 小时。在实践中，您可能会发现学生在某些练习中需要更多的时间。对此，我在下面有一些说明。

您应该向学生反复强调，题解代码是可用的，并且查看和复制题解代码也是可以的——尤其是有时间限制的时候。

在教授课程之前，我强烈建议您浏览并完成每一道练习，以免发生意外。

在授课期间，当学生做练习的时候，我通常也在自己的电脑上从头开始不看答案地完成每一道练习。为此，我强烈建议您手头准备一份练习的打印副本，而不必在电脑上查看练习（电脑屏幕那时可能正在投影）。在练习时间快要结束时，我将会开始讨论我的题解代码，强调屏幕上的不同点并对此进行讨论。如果题解存在任何潜在问题（包括设计注意事项），我也将进行讨论。请向学生强调，在继续之前，他们可能要看看或者复制题解代码。

## 第 1 节:  简介

本节的主要目标是让学生从编程环境开始入手。包括使用交互式shell，编辑或者运行简短的程序。在本节结束时，学生应该能够编写用于读取数据文件并执行简单的计算的简短脚本。他们将会了解数字，字符串，列表和文件，也会了解函数，异常和模块，但是不会介绍得太详细。

本节往往所耗费的时间是最长的，因为学生对工具不熟悉，可能会遇到各种问题。您应该在教室到处走走并且确保每个人可以编辑，运行和调试简单的代码，这是至关重要的。确保学生正确安装了 Python，下载了课程练习，网络正常运行。解决出现的任何其他问题。

时间：我打算在第 1 天的中午结束第 1 节。

## 第 2 节：处理数据

在这门课程中，本节可能是最重要的。它覆盖了数据表示和操作的基础，包括元组，列表，字典和集合。

2.2 节是重中之重，请在合理的情况下，给学生足够的时间让他们完成练习。这些练习可能会持续 45 分钟，具体取决于学生。在本练习的中间，我往往会前进到 2.3 节（格式化输出），给学生足够多的时间来继续做练习。综上所述，2.2 或 2.3 可能需要 1 个小时以上的时间。

2.4 节让学生探索 enumerate() 和 zip() 函数的用法。我认为这两个函数是必不可少的，所以，请不要跳过它们。

2.5 节介绍 collections 模块。关于 collections，大量的内容可以说，但这个时候，学生可能还不完全能领会其重要性。不要停留在“collections 是一个很酷的模块，稍后再看。这仅仅是一些示例”的观点中，而要详细了解collections。

2.6 节介绍列表推导式，这是一个用于处理列表数据的重要特性。向学生强调列表推导式与 SQL 数据库查询非常相似。在练习结束时，我经常做一些涉及更高级内容的交互性示例，如写一个列表推导式，或者使用 matplotlib 将数据可视化。如果您愿意，这也是一个介绍 Jupyter 的机会。

2.7 节是最复杂的练习。它涉及到在 Python 中一级数据的使用，以及事实——诸如列表之类的数据结构可以存储任何类型对象，只要你想。这些练习与解析 CSV 中的数据列相关，稍后在 3.2 节中会再次使用相关的概念。

时间：理想情况下，您希望第一天就完成第 2 节。但是，通常以第 2.5 节或者第 2.6 节结束。因此，如果您觉得自己有写落后，也不要惊慌。

## 第 3 节：程序组织

本节的主要目标是介绍有关函数的更相信的信息，并且鼓励学生使用函数。本节将函数构建到模块和脚本编写中。

3.1 节是关于如何将简单的“脚本”转变为函数。不要鼓励学生写乱七八糟的“脚本”，相反，代码至少应该模块化到函数中。这使代码更易于理解，更易于后续变更，运行更快。函数很好。

在整个课程中，3.2 节可能是最高级的一组练习。它让学生编写一个通用的实用函数来解析面向列的数据。但是，这会大量使用列表推导式和函数（例如，作为一等对象的函数）。您可能需要指导学生完成此代码的每一个步骤，详细地展示它的工作原理。回报是巨大的，但是——您可以向学生展示一个简短的通用目标函数，这些函数做一些令人惊讶的，强大的事情，如果没有大量复杂的代码，这些功能几乎不可能在C，C++ 或者 Java 中实现。对于这些代码，有很多可能的设计或者讨论方法。请发挥您的想象力。

3.3 节增加错误处理到在 3.2 节中创建的函数。一般而言，这是非常不错的时机去讨论异常处理。明确地讨论捕获所有异常的危险。这可能是一个好的时机去讨论“错误永不应该默默的传递”——“Python 之禅”里面的一项。

注意：在练习 3.4 之前，确保学生获取了足够的有关report.py, pcost.py 和 fileparse.py的练习版本。如果需要，请从 Solution 目录复制。

3.4 节介绍模块导入。在 3.2-3.3 节的文件写入用来简化 3.1 节的代码。请注意，您可能需要帮助学生解决有关 IDLE，sys.path 和其它有关导入的各种设置。

3.5 节讨论 `__main__` 和脚本编写。有大量的 命令行参数。您可能倾向于讨论诸如 argparse 的模块。但是，请注意，请跳过它们，一般而言，最最好是提到它，然后继续。

3.6 节 开放有关 Python 中的一般设计的讨论。编写灵活的代码好？还是编写硬编码文件名的代码好？这是您修改代码和对已存在代码重构的第一个地方。

从这里开始，大部分的练习对已编写代码进行微小修改。

You will probably need to guide
people through every single step of this code, showing how it works in
great detail.  The payoff is huge however---you can show people a
short general purpose function that does something amazingly powerful
and which would be virtually impossible to write in C, C++, or Java
without having a *LOT* of very complicated code.  There are a lot of
possible design/discussion avenues for this code.  Use your
imagination.

Section 3.3 adds error handling to the function created in Section 3.2
This is a good time to talk about exception handling generally.
Definitely talk about the dangers of catching all exceptions.  This
might be a good time to talk about the “Errors should never pass
silently” item on the “Zen of Python.”

*Note: Before Exercise 3.4, make sure students get fully working versions of report.py, pcost.py, and fileparse.py.   Copy from Solutions folder if needed *



Section 3.4 Introduces module imports.  The file written in Section
3.2-3.3 is used to simplify code in Section 3.1.  Be aware that you
may need to help students fix issues with IDLE, sys.path, and other
assorted settings related to import.

Section 3.5 talks about `__main__` and script writing.  There's a bit
about command line arguments.  You might be inclined to discuss a
module like argparse.  However, be warned that doing so opens up
a quagmire. It's usually better to just mention it and move on. 

Section 3.6 opens up a discussion about design more generally in Python.
Is it better to write code that's more flexible vs code that's
hardwired to only work with filenames?  This is the first place
where you make a code change and have to refactor existing code.

Going forward from here, most of the exercises make small changes
to code that's already been written.



## 4. Classes and Objects

This section is about very basic object oriented programming.  In
general, it is not safe to assume that people have much background in
OO.  So, before starting this, I usually generally describe the OO
“style” and how it's data and methods bundled together.  Do some
examples with strings and lists to illustrate that they are “objects”
and that the methods (invoked via .) do things with the object.
Emphasize how the methods are attached to the object itself.  For
example, you do items.append(x), you don’t call a separate function
append(items, x).

Section 4.1 introduces the class statement and shows people how to
make a basic object.  Really, this just introduces classes as another
way to define a simple data structure--relating back to using tuples
and dicts for this purpose in section 2.

Section 4.2 is about inheritance and how you use to create extensible
programs.  This set of exercises is probably the most significant in terms of
OO programming and OO design.  Give students a lot of time to work on
it (30-45 minutes).  Depending on interest, you can spend a LOT of
time discussing aspects of OO. For example, different
design patterns, inheritance hierarchies, abstract base classes, etc.

Section 4.3 does a few experiments with special methods.  I wouldn't
spend too much time fooling around with this.  Special methods come up
a bit later in Exercise 6.1 and elsewhere.

Timing:   This is usually the end of the 2nd day.

## 5. Inside Objects

This section takes students behind the scenes of the object system and
how it’s built using dictionaries, how instances and classes are tied
together, and how inheritance works.  However, most important part of
this section is probably the material about encapsulation (private
attributes, properties, slots, etc.)

Section 5.1 just peels back the covers and has students observe and
play with the underlying dicts of instances and classes.

Section 5.2 is about hiding attributes behind get/set functions and
using properties.  I usually emphasize that these techniques are
commonly used in libraries and frameworks--especially in situations
where more control over what a user is allowed to do is desired.

An astute Python master will notice that I do not talk about advanced
topics such as descriptors, or attribute access methods (`__getattr__`,
`__setattr__`) at all.  I have found, through experience, that this is
just too much mental overload for students taking the intro course.
Everyone’s head is already on the verge of exploding at this point and
if you go talk about how something like descriptors work, you’ll lose
them for the rest of the day, if not the rest of the course.  Save it
for an "Advanced Python" course.

If you're looking at the clock thinking "There's no way I'm going to
finish this course", you can skip section 5 entirely.

## 6. Generators

The main purpose of this section is to introduce generators as a way
to define custom iteration and to use them for various problems
related to data handling.  The course exercises have students analyze
streaming data in the form of stock updates being written to a log
file.

There are two big ideas to emphasize. First, generators can be used to
write code based on incremental processing.  This can be very useful
for things like streaming data or huge datasets that are too large to
fit into memory all at once.  The second idea is that you can chain
generators/iterators together to create processing pipelines (kind of
like Unix pipes).  Again, this can be a really powerful way to process
and think about streams, large datasets, etc.

Some omissions: Although the iteration protocol is described, the
notes don’t go into detail about creating iterable objects (i.e.,
classes with `__iter__()` and `next()`).  In practice, I’ve found that
it’s not necessary to do this so often (generators are often
better/easier).  So, in the interest of time, I’ve made a conscious
decision to omit it.  Also not included are extended generators
(coroutines) or uses of generators for concurrency (tasklets, etc.).
That’s better covered in advanced courses.

## 7. Advanced Topics

Basically this section is an assortment of more advanced topics that
could have been covered earlier, but weren’t for various reasons
related to course flow and content of the course exercises.  If you
must know, I used to present this material earlier in the course, but
found that students were already overloaded with enough information.
Coming back to it later seems to work better---especially since by
this point, everyone is much more familiar with working in Python and
starting to get the hang of it.

Topics include variadic function arguments (*args, **kwargs), lambda,
closures, and decorators.  Discussion of decorators is only a tiny
hint of what’s possible with metaprogramming.  Feel free to say more
about what’s possible, but I’d probably stay out of metaclasses!
Lately, I have been demoing "numba" as an example of a more
interesting decorator.

If you're pressed for time, most of section 7 can be skipped or heavily
compressed (you could skip exercises for instance).

## 8. Testing and Debugging

The main purpose of this section is just to introduce various tools
and techniques related to testing, debugging, and software
development.  Show everyone the unittest module.  Introduce the
logging module.  Discuss assertions and the idea of “contracts.”  Show
people the debugger and profiler.  Most of this is self-explanatory.

## 9. Packages

At this point, students have written an assortment of files (pcost.py,
report.py, fileparse.py, tableformat.py, stock.py, portfolio.py,
follow.py, etc.).  Two main goals in this section.  First, put all of
the code into a Python package structure.  This is only a gentle
introduction to that, but they'll move the files into a directory and
everything will break.  They'll need to fix their import statements
(package relative imports) and maybe fiddle with an `__init__.py` file.
Second goal, write a simple setup.py file that they can use to package
up the code and give it away to someone.  That's it.  End of the
course.

[Contents](Contents.md)


