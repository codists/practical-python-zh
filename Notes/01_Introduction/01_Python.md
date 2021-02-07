[目录](../Contents.md) \| [下一节 (1.2 第一个程序)](02_Hello_world.md)

# 1.1 Python
### Python 是什么?
Python 是一种解释型（译者注：区别于编译型）的高级语言， 通常被归类为 ["脚本语言"](https://en.wikipedia.org/wiki/Scripting_language) 并被认为类似于Perl、Tcl 或 Ruby 等语言。Python 的语法大致受 C 编程语言的启发。
Python 由吉多·范罗苏姆（Guido van Rossum）于 1990 年创建，用 Python 命名是为了纪念吉多·范罗苏姆所喜欢的马戏团 Monty Python。
### 从哪里获取 Python?
您可以从 [Python.org](https://www.python.org/) 获取Python。就本课程而言，您只需要对 Python 进行基本的安装即可。建议安装  Python 3.6 版本或者更新的 Python 版本，因为课程和练习题中使用的是 Python 3.6。
### 为什么创建 Python?
为什么创建 Python 呢？用 Python 创建者的话来说就是:
>我创建 Python 的原始动机是认为在 Amoeba [操作系统] 项目中需要一种更高级的语言。考虑到使用 C 语言开发系统管理工具所花费的时间太长，并且，由于各种原因，在 Bourne shell（译者注：  Bourne shell 是 shell 的一种类型） 中执行这些操作无效，所以需要一种语言来弥合 C 语言和 shell 之间的鸿沟。
>
>- 吉多·范罗苏姆（Guido van Rossum）
### Python 在电脑上的哪个位置?
尽管在很多环境中都可以运行 Python，但是 Python 通常作为一个程序安装在您的电脑上，并且这个程序可以在终端或者命令行 shell 运行。在终端上，您可以像下面这样输入 Python：
```
bash $ python
Python 3.8.1 (default, Feb 20 2020, 09:29:22)
[Clang 10.0.0 (clang-1000.10.44.4)] on darwin
Type "help", "copyright", "credits" or "license" for more information.
>>> print("hello world")
hello world
>>>
```
如果您是刚开始使用 shell 或者终端，您可能应该先停下来，先找一个关于 shell 或者终端的简短课程进行学习，然后再返回到这里继续本课程的学习。
尽管也可以在很多非 shell 的环境中编写 Python 代码，但是如果您能够在终端上运行、调试并与之交互，那么您将成为一名更强大的 Python 程序员。这是 Python 的原生环境，如果您能在原生环境中使用 Python，那么您就能在其它任何地方使用 Python。
## 练习
### 练习 1.1: 使用 Python 作为计算器
在您的电脑上，启动 Python 并把它当做计算器去解决下面的问题。
幸运的拉里（Larry）以每股 235.14 美元的价格购买了 75 股谷歌的股票。如今，谷歌的股价是 711.25 美元每股，使用 Python 的交互模式作为计算器，计算出如果拉里出售他所有的股票，他将获取多少利润。
```python
>>> (711.25 - 235.14) * 75
35708.25
>>>
```
高级技巧：使用下划线（_）变量来使用上次的计算结果。示例：如果股票经纪人（译者注：证券经纪人）拿走了 20% 的抽成，拉里赚了多少利润？
```python
>>> _ * 0.80
28566.600000000002
>>>
```
### 练习 1.2: 获取帮助
使用  `help()` 函数可获取有关 `abs()` 函数的帮助。然后还可以使用 `help()` 函数获取关于 `round()` 函数的帮助。仅仅输入不带参数的 `help（）` 函数可以进入交互的帮助查看器（viewer）。
使用 `help()` 函数有一个要注意点的是：`help()` 函数并不适用于基本的 Python 语句，例如 `for`, `if`, `while` 语句（也就是说，如果您输入 `help(for) `，那么将会得到一个语法错误（ SyntaxError））。替代方案是：将需要获取帮助的主题放在引号中（quotes，译者注：在 Python 中，单引号和双引号是一样的，所以 quotes 在这里既可以指单引号（''），也可以指双引号（""）），例如：`help("for")`。如果这还是没用，那么您就得上网搜索了。
进阶：访问 <http://docs.python.org>，然后找到 `abs()` 函数的说明文档（提示：可以在库参考（Library Reference）中的内建函数（Built-in Functions）部分找到它）。
### 练习 1.3: 剪切和粘贴
本课程是由一系列的传统网页构成，建议您尝试**亲自手动输入**这些交互的 Python 示例代码。尤其是对于第一次学习 Python 的人，推荐使用这种 ”慢速的方法“。通过降低学习速度，手动输入代码、并且思考您在做什么，您将对Python 这门语言有更好的了解。
如果您一定要剪切和粘贴示例代码，请从  `>>>` 提示符后开始选择，一直选择到最后，但是不要超过第一个空行或者下一个 `>>>`  提示符（以先出现的 `>>>`  提示符为准）。从浏览器中选择 “复制”，然后回到 Python 的窗口，接着选择 “粘贴” 将代码复制到 Python shell 中。当您粘贴之后，为了让代码运行，您需要按一下回车键（译者注：在windows系统的电脑上，是 **Enter 键**，在 mac 系统的电脑上是 **Return 键**）。
在此会话（ session）中使用 “剪切粘贴” 的方式执行 Python 语句：
```python
>>> 12 + 20
32
>>> (3 + 4
         + 5 + 6)
18
>>> for i in range(5):
        print(i)

0
1
2
3
4
>>>
```
警告：不能一次粘贴多个 Python 命令（**命令**指 `>>>` 符号后面的 **语句（statements）**）到基本的 Python shell 中，一次只能粘贴一个命令。
现在，您已经完成了这一步，请记住，通过缓慢地输入代码，并且思考它——而不是剪切和粘贴，在学习课程之后，您将取得更多的收获。
### 练习 1.4: 我的公共汽车在哪里?
现在尝试一些高级的内容，输入下面的语句，看看在芝加哥的克拉克街（Clark street）和巴尔莫勒尔街（Balmoral street）拐角处等下一辆北上的 CTA \#22 公交车的人需要等多久：
```python
>>> import urllib.request
>>> u = urllib.request.urlopen('http://ctabustracker.com/bustime/map/getStopPredictions.jsp?stop=14791&route=22')
>>> from xml.etree.ElementTree import parse
>>> doc = parse(u)
>>> for pt in doc.findall('.//pt'):
        print(pt.text)

6 MIN
18 MIN
28 MIN
>>>
```
上面大约6 行的代码下载了一个网页，解析了一个 XML 文档，并且提取了一些有用的信息。上面访问的数据实际上是提供给网站 <http://ctabustracker.com/bustime/home.jsp> 的。再运行一次上面的代码，然后观察预期的变化。
注意：此服务只报告接下来 30 分钟内公交车的到达时间。如果您位于与芝加哥不同的时区，而此时凑巧是芝加哥的凌晨 3 点，那么您可能得不到任何的输出。您可以使用上面的跟踪链接（<http://ctabustracker.com/bustime/home.jsp> ）进行仔细检查。
如果第一个导入语句 `import urllib.request` 导入失败，很可能是因为您使用的是 Python 2。对于本课程，需要确保使用的是 Python 3.6 或者更新的版本。如果有需要，请访问 <https://www.python.org>  进行下载。
如果您的工作环境要求使用 HTTP 代理服务器，则您可能需要设置 `HTTP_PROXY` 环境变量，才能使本部分练习正常进行。示例：
```python
>>> import os
>>> os.environ['HTTP_PROXY'] = 'http://yourproxy.server.com'
>>>
```
如果您无法完成本部分的练习，那么也没关系，本课程的其余部分与解析 XML 文档无关。
[目录](../目录.md) \| [下一节 (1.2 第一个程序)](02_Hello_world.md)

