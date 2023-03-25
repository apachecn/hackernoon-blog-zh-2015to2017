# IPython 还是 Jupyter？

> 原文：<https://medium.com/hackernoon/ipython-or-jupyter-10a9fb82682b>

*原贴于:*[*【https://www.datacamp.com/community/blog/ipython-jupyter】*](https://www.datacamp.com/community/blog/ipython-jupyter)

对于学习者和更高级的数据科学家来说，Jupyter 笔记本是最受欢迎的[数据科学](https://hackernoon.com/tagged/data-science) [工具](https://hackernoon.com/tagged/tools)之一:交互式环境不仅是教和学以及与同行分享您的工作的理想选择，还能确保可重复的研究。然而，当您发现如何使用这款笔记本时，您会经常碰到 IPython。

在某些情况下，这两者似乎是同义词，你会同意我说的，当你想深入挖掘时，这是非常令人困惑的:magics 是 Jupyter 还是 IPython 的一部分？保存和加载笔记本是 IPython 还是 Jupyter 的功能？

你可以继续提问。

今天的博客文章旨在更明确地说明两者之间的一些核心差异，不仅从两者的起源出发来解释两者之间的关系，而且还涵盖了两者之间的一些特定特征，以便您更容易区分两者！

还可以考虑阅读 DataCamp 的[Jupyter 笔记本权威指南](https://www.datacamp.com/community/tutorials/tutorial-jupyter-notebook)以获得提示和技巧、最佳实践、示例等等。

# IPython & Jupyter 笔记本的起源

为了充分理解 Jupyter 笔记本是什么以及它与 IPython 有何不同，首先阅读一下这两者如何融入计算笔记本的历史和未来可能会很有意思。

## 计算笔记本的开始:MATLAB、Mathematica 和 Maple

在 20 世纪 80 年代中期，MATLAB 由 Jack Little、Steve Bangert 和 Cleve Moler 创建的 MathWorks 发布。

让我们回到 20 世纪 80 年代末，确切地说是 1987 年。西奥多·格雷开始开发 Mathematica 笔记本前端，一年后，它向公众发布。GUI 允许交互式创建和编辑笔记本文档，这些文档包含印刷精美的程序代码、格式化文本和一大堆其他内容，如排版数学、图形、GUI 组件、表格和声音。标准的文字处理功能已经具备，比如实时多语言拼写检查。您可以在幻灯片放映环境中输出文档进行演示。

当你观察这些笔记本的结构时，你会立刻注意到它们依赖于单元格的层次结构，这些单元格允许对文档进行概述和分段，这也是你现在在 Jupyter 笔记本中可以找到的。

同样在 20 世纪 80 年代末，1989 年，Maple 推出了他们的第一款笔记本风格的 GUI。它包含在 Macintosh 4.3 版中。X11 和 Windows 的新界面版本出现在 1990 年。

这些笔记本都将成为其他人开发“数据科学笔记本”的灵感。

## 数据科学笔记本的兴起

在现在广为人知的交互式数据科学之间有许多计算笔记本。本节将重点介绍在数据科学笔记本电脑的崛起中做出最显著贡献的笔记本电脑。

**圣人笔记本**

作为一个基于浏览器的系统，Sage notebook 最初是在 2000 年代中期发布的，然后在 2007 年，发布了一个更强大的新版本，有用户帐户，可以用来公开文件。它类似于谷歌文档的用户界面设计，因为 Sage 笔记本的布局是基于谷歌笔记本的布局。

Sage 笔记本的创建者已经证实他们是 Mathematica 笔记本和 Maple 工作表的狂热用户。当您考虑 Sage 笔记本的开发时，其他重要的动机或驱动因素是这样的事实:开发人员与 IPython 背后的团队有着密切的联系，他们经历了 IPython GUI 的失败尝试，以及“AJAX”= web 应用程序的兴起，这种应用程序不需要用户每次做某件事情时都刷新整个页面。

**伊普森&朱庇特**

2001 年末，也就是吉多·范·罗苏姆在荷兰国家数学和计算机科学研究所开始研究 Python 二十年后，费尔南多·佩雷斯开始开发 IPython。该项目深受 Mathematica 笔记本和 Maple 工作表的影响，就像 Sage 笔记本和随后的许多其他项目一样。

2005 年，罗伯特·克恩和费尔南多·佩雷斯都试图建立一个笔记本电脑系统。不幸的是，原型从来没有变得完全可用。

一晃两年过去了:团队一直在工作，在 2007 年，他们制定了另一个实现笔记本型系统的尝试。到 2010 年 10 月，有了一个网络笔记本的原型，在 2011 年夏天，这个原型被合并，并在 2011 年 12 月 21 日发布了 0.12。在随后的几年中，该团队获得了奖项，如 2013 年 3 月 23 日的费尔南多·佩雷斯自由软件进步奖和 Jolt 生产力奖，以及来自阿尔弗雷德·P·斯隆基金会的资助等。

最后，在 2014 年，Project Jupyter 作为 IPython 的一个衍生项目启动。

拆分前 IPython 的最后一个版本包含了交互 shell、笔记本服务器、Qt 控制台等。这个项目很大，工具越来越成为越来越多不同的项目，而这些项目恰好属于同一个项目。Jupyter 项目启动后，IPython 项目的语言无关部分，比如笔记本格式、消息协议、Qt 控制台、笔记本 web 应用等。被投入到朱庇特项目中。

这就是所谓的[大分裂](http://blog.jupyter.org/2015/04/15/the-big-split/)。

IPython 现在只有两个角色要完成:作为 Jupyter 笔记本的 Python 后端，也称为内核，以及一个交互式 Python shell。但这还不是全部:在 IPython 生态系统中，您还会发现一个并行计算框架。稍后您将会读到更多相关内容！

就像 IPython 一样，Project Jupyter 实际上是一系列项目的名称:它包含的三个应用程序是笔记本本身，一个控制台和一个 Qt 控制台，但也有一些子项目，如支持笔记本部署的 Jupyterhub，`nbgrader`用于教育目的，等等。你可以在这里看到 Jupyter 建筑的概述[。](https://jupyter.readthedocs.io/en/latest/architecture/visual_overview.html)

请注意，正是这个项目的发展解释了许多 Pythonistas 在谈到 IPython 和 Jupyter 时的困惑:由于一个来自另一个(最近)，有些人仍然难以为概念采用正确的名称。但是更复杂的因素可能是进化:由于一个来自另一个，IPython 和 Jupyter 笔记本功能之间有相当多的重叠，有时很难区分！

如何区分这两者将在本文的下一部分变得清晰。

如果你想知道更多关于 IPython 发展的细节，看看[费尔南多·佩雷斯](http://blog.fperez.org/2012/01/ipython-notebook-historical.html)和[威廉·斯坦](http://wstein.org/talks/2016-06-sage-bp/bp.pdf)关于他们笔记本历史的个人描述。

**R 笔记本**

R Markdown 和 Jupyter 笔记本共享可复制工作流的交付，将代码、输出和文本编织在一个文档中，支持交互式小部件和多种格式的输出。

然而，两者也有所不同:前者侧重于可再现的批处理执行、纯文本表示、版本控制、产品输出，并提供与 R 脚本相同的编辑器和工具。后者侧重于代码内联输出、跨会话缓存输出、共享代码和在单个文件中输出。笔记本强调交互式执行模式。它们不使用纯文本表示，而是使用结构化数据表示，比如 JSON。

这一切解释了 RStudio 笔记本应用程序的目的:它结合了 R Markdown 的所有优点和计算笔记本必须提供的好东西。

要了解更多关于如何使用 R 笔记本以及 Jupyter 和 R Markdown 笔记本在笔记本共享、项目管理、版本控制等方面的确切区别，请查看 DataCamp 的 [Jupyter 和 R: Notebooks with R](https://www.datacamp.com/community/blog/jupyter-notebook-r) 帖子。

当然，当你进入数据科学领域时，你可以考虑更多的笔记本电脑。近年来，数据科学家和数据科学爱好者找到了许多新的替代品:不仅仅是 Beaker Notebook、Apache Zeppelin、Spark Notebook、DataBricks Cloud 等。，还包括 Rodeo IDE 等其他工具，这些工具也使您的数据科学分析具有交互性和可重复性。

**数据科学笔记本的未来**

笔记本电脑似乎会一直存在下去。最近，新一代 Jupyter 笔记本已经引入社区:JupyterLab。Notebook 应用程序不仅支持笔记本，还支持文件管理器、文本编辑器、终端仿真器、运行 Jupyter 进程的监视器、IPython 集群管理器和显示帮助的寻呼机。

Jupyter Notebook 丰富的工具集已经有机地发展起来，并由我们的用户和开发人员的需求所驱动。JupyterLab 是支持所有这些工具的下一代架构，但是具有灵活且响应迅速的 UI，提供了可以将工具结合在一起的用户控制的布局。

点击阅读更多关于[的信息。](http://blog.jupyter.org/2016/07/14/jupyter-lab-alpha/)

![](img/ad3e5dd0897c01fbab1fe3e1f1b194fd.png)

# IPython 还是 Jupyter？

项目的发展和随之而来的“大分裂”是理解两者之间真正差异的基础。但是，由于这两者有着内在的联系，你有时会怀疑什么是什么的一部分。

下一节将介绍 IPython 生态系统或 Jupyter 项目的一些特性。

由您选择正确的答案，并了解每项功能的更多信息！

## 核

内核是 Jupyter 笔记本应用程序的一个特性。内核是一个运行和自省用户代码的程序:它提供计算和与前端接口的通信，比如笔记本。Jupyter Notebook 应用程序有三个主要内核:IPython、IRkernel 和 IJulia 内核。

因为“Jupyter”这个名字实际上是“Julia，Python 和 R”的简称，所以这并不令人感到意外。作为项目发展的结果，IPython 内核由 Jupyter 团队维护。

但是，你也可以在 Jupyter Notebook 应用中运行很多其他语言，比如 Scala、JavaScript、Haskell、Ruby 等等。那些是社区维护的内核。

## **部署**

部署笔记本是您在使用 Jupyter 笔记本时通常会发现或研究的内容。有相当多的软件包可以帮助您部署笔记本电脑，它们是 Jupyter 生态系统的一部分。

以下是其中的一些:

*   当你需要一堆 Jupyter 应用程序和内核作为 Docker 容器时，将会派上用场。
*   `[ipywidgets](https://ipywidgets.readthedocs.io/en/latest/)`提供交互式 HTML & JavaScript 小部件(如滑块、复选框、文本框、图表等。)用于 Jupyter 架构，该架构将前端控制结合到 Jupyter 内核。
*   `[jupyter-drive](https://github.com/jupyter/jupyter-drive)`允许 IPython 使用 Google Drive 进行文件管理。
*   `[jupyter-sphinx-theme](https://github.com/jupyter/jupyter-sphinx-theme)`将 Jupyter Sphinx 主题添加到您的笔记本中。这将使创建智能和漂亮的文档变得更加容易。
*   `kernel_gateway`是一个 web 服务器，支持不同的机制来生成 Jupyter 内核并与之通信。查看[这里的](http://jupyter-kernel-gateway.readthedocs.io/en/latest/uses.html)来看看这个包可以派上用场的一些用例。
*   `nbviewer`分享您的笔记本。点击查看画廊[。](http://nbviewer.jupyter.org/)
*   使用 Docker 容器创建临时 Jupyter 笔记本服务器。在这里亲自尝试一下[。](https://try.jupyter.org/)
*   `[traitlets](https://github.com/ipython/traitlets)`是一个框架，它允许 Python 类具有带类型检查的属性、动态计算的默认值和‘on change’回调。您还可以将该包用于配置目的，从文件或命令行参数加载值。`traitlets`为 IPython 和 Jupyter 的配置系统以及 IPython 交互小部件的声明式 API 提供支持。

## 系统外壳用法

使用**魔法**可以使 IPython 适应系统外壳的使用:以`!`开头的行被直接传递给系统外壳。比如`!ls`会在当前目录下运行`ls`。您可以使用语法`myfiles=!ls`将系统命令的结果分配给 Python 变量。

但是，如果您想将`ls`函数的结果明确地打印成一个带有字符串的列表，而不将其赋给变量，请使用两个感叹号(`!!ls`)或不带赋值的`%sx` magic 命令。

```
# Assign the result to `ls` 
ls = !ls # Explicit `ls` 
!!ls

# Or with magics 
%sx # Assign magics result 
ls = %sx
```

注意`!!`命令不能赋给变量，但是魔术的结果(只要它返回值)可以赋给变量。

IPython 还允许您在进行系统调用时扩展 Python 变量的值:只需用大括号(`{}`)将变量或表达式括起来。同样，在带有`!`或`!!`的 shell 命令中，任何带有`$`前缀的 Python 变量都会被扩展。在下面的代码块中，您将看到您回显了`sys`变量的`argv`属性。请注意，您还可以使用`$` / `$$`语法来处理来自系统输出的 Python 变量，稍后您可以使用这些变量来编写进一步的脚本。

要将文本`$`传递给 shell，请使用双精度`$$`。如果您想访问 shell 和环境变量，如`$PATH`，您将需要这个文字`$`:

```
# Import and initialize 
import math 
x = 4 # System call with variable 
!echo {math.factorial(x)} # Expand a variable 
!echo $sys.argv # Use $$ for a literal $ 
!echo "A system variable: $$HOME"
```

点击阅读更多[。](http://ipython.readthedocs.io/en/stable/interactive/shell.html)

请注意，除了 IPython 之外，还有其他内核具有魔力来确保代码行作为 shell 命令执行！

此外，您可以为系统命令定义**别名**。这些别名基本上是 bash 命令的快捷方式。一个别名是一个元组:(“showTheDirectory”，“ls”)。运行`%alias?`获取更多信息！提示:使用`%rehashx`加载所有的$PATH 作为 IPython 别名。

## 魔法？

如果你读过 DataCamp 的[Jupyter 笔记本权威指南](https://www.datacamp.com/community/tutorials/tutorial-jupyter-notebook)或者你已经和 Jupyter 合作过，你可能已经知道所谓的“魔法命令”。魔术通常由在底层语言中无效的语法元素和暗示命令的某种单词组成。在幕后，magics 函数实际上是 Python 函数。

您可能已经知道，IPython 内核使用了`%`语法元素，因为它在 Python 中不是有效的一元运算符。然而，以`%%`开头的行显示了一种单元魔力:在当前执行块中，它们不仅将当前行的其余部分作为参数，还将它们下面的所有行作为参数。细胞魔术实际上可以对它们接收的输入进行任意修改，甚至根本不需要有效的 Python 代码。它们将整个块作为单个字符串接收。

魔术是特定于内核的，由内核提供，旨在使您在 Jupyter Notebook 中的工作和体验更具互动性。魔法命令在特定内核中是否可用取决于内核开发人员和每个内核的内核。你已经看到了:魔法是一个内核特性。

当您使用 Jupyter 笔记本 IPython(也称为内核)的 Python 后端时，您可能希望利用以下技巧来获得使您的编程更快、更容易和更具交互性的功能。请注意，列出的这些并不意味着详尽无遗。查看[这个内置魔法命令列表](http://ipython.readthedocs.io/en/stable/interactive/magics.html)以获得完整的概述。

**带兵？**

IPython 内核的一个主要特性是能够显示作为运行代码单元输出的图形。内核被设计为与`matplotlib`数据可视化库无缝协作来提供这一功能。要使用它，请使用魔法命令`%matplotlib`。

因此，默认情况下，您的图将显示在单独的窗口中。此外，您还可以指定一个后端，如`inline`或`qt`，绘图命令的输出将内联显示或通过不同的 GUI 后端显示。你可以在这里阅读更多信息[。](http://ipython.readthedocs.io/en/stable/interactive/plotting.html)

**调试？**

接下来，每次出现未捕获的异常时，您还可以使用 magics 调用 Python 调试器`%pdb`。这将引导您浏览触发异常的代码部分，从而有可能快速找到 bug 的来源。

您还可以使用带有-d 选项的`%run` magic 命令在 Python 调试器的控制下运行脚本。它会自动为您设置初始断点。最后，您还可以使用`%debug`魔法来更容易地访问调试器。

**扩展？**

您可以使用`%load_ext`魔法通过模块名加载 IPython 扩展。IPython 扩展是修改外壳行为的 Python 模块:扩展可以注册魔法、定义变量，并且通常修改用户名称空间以提供在代码单元中使用的新特性。

以下是一些例子:

*   使用`%load_ext oct2py.ipython`从 Python 无缝调用 M-files 和 Octave 函数，
*   使用`%load_ext rpy2.ipython`使用一个接口来运行嵌入在 Python 进程中的 R，
*   使用`%load_ext Cython`使用 Python 转 C 编译器，
*   使用`sympy.init_printing()`自动打印基本对象，以及
*   要在交互式会话中使用 Fortran，可以使用`%load_ext fortranmagic`。
*   …还有很多！您可以创建一个 register your own IPython extensions，并在 PyPi 上注册它们:这也意味着还有许多其他用户定义的扩展和魔术！一个例子是`[ipython_unittest](https://pypi.org/project/ipython_unittest/)`，但是也检查这个[扩展索引](https://github.com/ipython/ipython/wiki/Extensions-Index)。

您应该关注的另一个扩展是`**sparkmagic**`，这是一组通过 Livy(Jupyter 笔记本中的一个 Spark REST 服务器)与远程 Spark 集群交互工作的工具。`sparkmagic`库提供了一个`%%spark`魔法，您可以使用它从普通的 IPython 笔记本上轻松地运行远程 Spark 集群的代码。

```
# Load in sparkmagic 
%load_ext sparkmagic.magics # Set the endpoint 
%manage_spark # Ask for help 
%spark?
```

点击[此处](https://github.com/jupyter-incubator/sparkmagic/blob/master/examples/Magics%20in%20IPython%20Kernel.ipynb)获取更多关于如何利用这些魔法与 Spark cluster 交互工作的示例。

请注意，除了`%load_ext`之外，IPython 还有另外两个魔法可以让您在 Jupyter 笔记本中管理扩展:`%reload_ext`和`%unload_ext`分别用于卸载、重新导入和加载扩展以及卸载扩展。

然而，在其他语言中，神奇命令中的语法元素可能有某种含义。

R 内核，IRKernel，没有魔法系统。例如，为了执行 bash 命令，您将使用 R 函数(如`system()`)来调用 OS 命令。例如`system("head -5 *.csv", intern=TRUE)`。请注意，通过包含`intern`参数，您指定了要将命令的输出捕获为 r 中的一个字符向量。

同样，Julia Kernel **IJulia** 也不使用“魔法”。相反，在 Julia 中，实现相同目标的其他语法更自然，可以在 IJulia 代码单元之外的环境中工作，并且通常更强大。然而，IJulia 内核的开发人员已经确保，无论何时您在 IJulia 代码单元中输入 IPython magic 命令，您都会看到一个带有帮助的打印输出，解释如何在 Julia 中实现类似的效果(如果可能)。

比如 IPython 的`%load`在 IJulia 的类比就是`IJulia.load()`。

另一方面，像 Scala 内核 **IScala** 这样的内核确实支持神奇的命令，类似于 IPython。然而，这套魔术是不同的，因为它必须匹配 Scala 和 JVM 的细节。魔术命令由百分号`%`组成，后跟标识符和魔术的可选输入。一些最著名的魔术是:

```
# Type Information 
%type 1 # Library Management 
%libraryDependencies 
%update
```

正如您在上面读到的，`sparkmagic`库还提供了一组 Scala 和 Python 内核，允许您自动连接到远程 Spark 集群，运行代码和 SQL 查询，管理 Livy 服务器和 Spark 作业配置，并生成自动可视化。这不需要任何代码！

例如，您可以使用`%%sql`轻松执行 SparkSQL 查询，或者通过`%%info magic`访问 Spark 应用程序信息和日志。

如果您正在使用另一个内核，并且您想知道是否可以使用神奇的命令，那么知道有一些内核构建在 [**元内核**](https://github.com/Calysto/metakernel/tree/master/metakernel/magics) 项目之上，并且在大多数情况下将使用与您在 IPython 内核中找到的相同的神奇命令可能会很方便。你可以在这里找到元内核魔法列表。元内核是一个 Jupyter/IPython 内核模板，它包含了核心的魔法函数。

一些例子:

这意味着，例如，当您使用 MATLAB 内核时，您将拥有以下可用的魔法:

```
Available line magics: %cd %connect_info %download %edit %get %help %html %install %install_magic %javascript %kernel %kx %latex %load %ls %lsmagic %magic %parallel %plot %pmap %px %python %reload_magics %restart %run %set %shell %spell Available cell magics: %%debug %%file %%help %%html %%javascript %%kx %%latex %%processing %%px %%python %%shell %%show %%spell
```

如果你看看上面打印的内容，你会发现其中一些神奇的命令看起来非常熟悉。对于那些不太了解魔术的人来说，将上面的块与 IPython 内核中默认可用的魔术进行比较，您会发现有些魔术是相同的:

```
Available line magics: %alias %alias_magic %autocall %automagic %autosave %bookmark %cat %cd %clear %colors %config %connect_info %cp %debug %dhist %dirs %doctest_mode %ed %edit %env %gui %hist %history %killbgscripts %ldir %less %lf %lk %ll %load %load_ext %loadpy %logoff %logon %logstart %logstate %logstop %ls %lsmagic %lx %macro %magic %man %matplotlib %mkdir %more %mv %notebook %page %pastebin %pdb %pdef %pdoc %pfile %pinfo %pinfo2 %popd %pprint %precision %profile %prun %psearch %psource %pushd %pwd %pycat %pylab %qtconsole %quickref %recall %rehashx %reload_ext %rep %rerun %reset %reset_selective %rm %rmdir %run %save %sc %set_env %store %sx %system %tb %time %timeit %unalias %unload_ext %who %who_ls %whos %xdel %xmode Available cell magics: %%! %%HTML %%SVG %%bash %%capture %%debug %%file %%html %%javascript %%js %%latex %%perl %%prun %%pypy %%python %%python2 %%python3 %%ruby %%script %%sh %%svg %%sx %%system %%time %%timeit %%writefile
```

本质上，您可以用一个问题来区分哪些是 IPython 特有的，哪些可以在其他内核中使用:这个功能是 Python 特有的，还是也可以在您正在使用的语言中使用的通用功能？

例如，`%pdb`或 Python 调试器或`%matplotlib`是 Python 特有的东西，当你使用 JavaScript 内核时，它们就没有意义了。然而，用`%cd`改变目录通常是非常通用的，应该在任何语言中都能工作，因为它是这样一个“通用”命令。所以，这可能是一个可以在其他内核中使用的魔法。当然，您仍然需要知道您的内核是否使用了魔法。

## **转换和格式化笔记本？**

转换和格式化笔记本是 Jupyter 生态系统中的功能。对于这些任务，您通常会找到两个工具`[nbconvert](https://github.com/jupyter/nbconvert)`和`[nbformat](https://github.com/jupyter/nbformat)`。

您可以使用前者将笔记本转换为各种其他格式，以熟悉的格式呈现信息，发布研究成果并将笔记本嵌入论文中，与他人协作以及与更多受众共享内容。

后者基本上包含 Jupyter notebook 格式，是理解 Notebook 文件是简单的 JSON 文档的关键，这些文档包含:元数据(如内核或语言信息)、Notebook 格式的版本(主要和次要)以及包含所有文本、代码等的单元格。已存储。

## 保存和加载笔记本？

保存和加载笔记本是 Jupyter 笔记本应用程序的一个特性。您可以加载保存为文件的笔记本。ipynb 文件扩展名，这是其他人通过下载并在 Jupyter 应用程序中打开该文件而创建的。更具体地说，您可以创建一个新笔记本，然后通过单击“文件”选项卡，单击“打开”并选择您下载的笔记本来选择打开文件。

相反，您也可以通过单击相同的“文件”选项卡并选择“下载为”来保存您自己的笔记本文件，或者您也可以选择保存文件并设置检查点。当你想做一些小的版本控制，或者恢复到笔记本的早期版本时，这是非常方便的。当然，您的修改会每隔几分钟自动保存一次，所以并不总是需要明确地执行这个操作。

请注意，您也可以选择不保存对原始笔记本的任何更改，方法是制作一份副本并将所有更改保存到该副本中！

## 键盘快捷键和多光标支持？

选择多个单元、切换单元输出、插入新单元等。对于所有这些操作，Jupyter 笔记本中都有键盘快捷键。你可以在顶部的菜单下找到键盘快捷键列表:进入“帮助”选项卡，选择“键盘快捷键”。

此外，多光标支持是 Jupyter 笔记本的一个特性！

## 并行计算？

并行计算网络是 IPython 项目的一部分，但是从 4.0 开始，它是一个名为`[ipyparallel](https://ipyparallel.readthedocs.io/en/latest/)`的独立包。这个包基本上是用于控制 Jupyter 集群的 CLI 脚本的集合。

尽管它是独立的，但它仍然是 IPython 生态系统的一个强大组件，这一点通常被忽略了；它如此强大是因为它允许你在许多机器上启动许多分布式内核，而不是运行一个 Python 内核。

`ipyparallel`的典型用例是，例如，您需要多次运行模型来估计其输出的分布或它们如何随输入参数变化。当模型的运行是独立的时，您可以通过在集群中的多台计算机上并行运行它们来加速该过程。想想[分布式模型训练](http://activisiongamescience.github.io/2016/04/19/IPython-Parallel-Introduction/)或者[模拟](http://davidmasad.com/blog/simulation-with-ipyparallel/)。

![](img/2cb699f62460bdf1ce92ec3cb2308705.png)

## 终端？

这个特性是 Jupyter 生态系统的一部分:你有 Jupyter 控制台和一个 Jupyter 终端应用程序。然而，从一开始，IPython 就被用来表示 Python 最初的交互式命令行终端。它提供了一个增强的读取-评估-打印循环(REPL)环境，特别适合科学计算。这是 2011 年之前的标准，当时笔记本工具被引入，并开始为 Python 提供现代而强大的 web 接口。

接下来，您还有 IPython 控制台，它启动了两个进程:原始的 IPython 终端 shell 和默认的概要文件或内核，如果没有特别说明，它们就会启动。默认情况下，这是 Python。

IPython 控制台现在已经过时，如果您想启动它，您需要使用 Jupyter 控制台，这是一个基于终端的 Jupyter 内核控制台前端。此代码基于单进程 IPython 终端。Jupyter 控制台在终端提供 IPython 的交互式客户端体验，但是能够连接到任何 Jupyter 内核，而不仅仅是 IPython。

这让您可以测试您可能已经安装在终端上的任何 Jupyter 内核，而不需要为它启动一个完整的笔记本。控制台允许与其他 Jupyter 内核进行基于控制台的交互，比如 IJulia、IRKernel。

最后，Jupyter Notebook 应用程序还有一个终端应用程序:一个运行在浏览器中的简单 bash shell 终端。当您启动应用程序并从下拉菜单中选择一个新的终端时，您可以很容易地找到它。

## Qt 控制台？

Qt 控制台曾经是 IPython 项目的一部分，但现在已经转移到了 Jupyter 项目中。这是一个轻量级的应用程序，很大程度上感觉像一个终端，但提供了许多只有在 GUI 中才有的增强功能，如内嵌图形、带语法突出显示的正确的多行编辑、图形呼叫提示等等。Qt 控制台可以使用任何 Jupyter 内核。

今天的博客文章是对 DataCamp 权威指南的补充，更详细地介绍了计算笔记本的历史，以及 IPython 和 Jupyter 项目的一些主要特性，以便您可以更清楚地了解两者的发展和差异。我们的目标是看到，如果你不考虑这两个项目的历史观点，这两者之间的区别有时是很难的。在某些情况下，有一个灰色地带，一个“中间地带”，这是不容易归类的。

*最初发表于*[*【www.datacamp.com】*](http://www.datacamp.com/community/blog/ipython-jupyter)*。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)