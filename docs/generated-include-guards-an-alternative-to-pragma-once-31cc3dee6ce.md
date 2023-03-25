# 生成包括守卫:一次 pragma 的替代

> 原文：<https://medium.com/hackernoon/generated-include-guards-an-alternative-to-pragma-once-31cc3dee6ce>

![](img/470a332a6acbe537bb926dd76f4d8edf.png)

Photo by [Toa Heftiba](https://unsplash.com/photos/PxM8hw4j3ZY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 介绍

在 C++中，没有什么能阻止程序员多次包含头文件。这可能会导致定义重复，这是一个错误。由于很难确保头文件只包含一次，一个常见的策略是只进行第一次包含计数。这可以使用“include guard”来完成，这是一小段预处理器逻辑，如下所示:

## 它是如何工作的？

关于第一个包含，由于`HEADER_HAS_BEEN_INCLUDED`没有定义，所以我们定义了`foo`。在后续的收录中，`HEADER_HAS_BEEN_INCLUDED`已经被定义了，所以我们就略过内容。

例如，如果我们有这个 C++文件:

然后，它将扩展到以下内容:

预处理器完成后，我们会看到:

这是惯用的方法，但它有一些局限性:

1.  需要三行锅炉板代码
2.  第 1 行和第 2 行的变量名必须与*完全匹配*
3.  多个文件中不得使用相同的变量名
4.  我们要记住`#endif`，它位于`#ifndef`文件的另一端

## 祈祷文怎么样？

`#pragma once`旨在克服这些问题。这是 C++编译器的一个非标准但被广泛支持的特性。概念很简单:任何包含`#pragma once`的文件实际上只包含*一次，即使程序员多次包含它。*

*使用`#pragma once`，我们的示例变成:*

*看起来不错，对吧？可悲的是`#pragma once`带来了许多问题。*

*根本原因是`#pragma once`关心的是*(有些代码在*处)而不是它的*内容*。如果您可以通过多个路径访问同一文件的两个副本，那么它将被包含两次。而且，如果您有两条路径*看起来*不同，但实际上是相同的，那么编译器可能不会发现这一点。更重要的是，它不是标准的，所以编译器实现不必尊重它的语义。*

# *一种可能的解决方法*

*`#pragma once`的问题源于这样一个事实，即它在文件的位置工作，而不是它的内容。如果我们只使用内容呢？*(当然，记录每个标题的所有内容会很慢，但是我们可以通过记录内容的散列来优化)。**

*这个过程将是:*

*1.当包含头文件时，对其进行哈希处理
2。如果以前见过哈希，则忽略 include
3。否则，按正常方式包括标题*

*这将是一个健壮的解决方案，因为它根本不关心找到文件的路径，只关心它的内容。*

# *实施变通办法*

*向 C++标准中添加一个新命令会花费相当多的时间，但幸运的是，我们可以使用脚本和预处理器来实现这个逻辑。*

*基本想法是这样的:*

*例如这个标题:*

*具有阿沙-256 哈希:*

*所以生成的头可能是:*

*虽然单个文件的转换很简单( [Python 脚本](https://github.com/njlr/buck-include-guards/blob/84c3a67dcb5eb6d9924cacc4f2c33723beb6245a/scripts/add-include-guard.py))，但是我们仍然需要管理转换过程。我们需要确保:*

*   *为每个文件运行转换*
*   *新文件会自动转换*
*   *已删除文件的转换会自动移除*
*   *只有当文件发生变化时，转换才会重新运行*
*   **好处:转换可以安全地放入共享的网络缓存中**

*使用 [Buck build](https://buckbuild.com/) ，我们可以很容易地将这个逻辑编码到项目的构建脚本中。*

*让我们从单个文件的构建规则开始，然后进行归纳:*

*巴克中的一个`genrule`很像马克中的一个目标。我们定义输入文件、输出文件名和要执行的命令。这个目标使用我们的 Python 脚本来生成 include guard，并在`add.hpp`上运行它。与 Make 不同，Buck 将隔离并缓存进程的输入散列。*

*现在我们有了一个工作的文件，我们可以把这个过程推广到`n`文件。为此，我们创建一个 Python 函数，为给定的文件创建一个`genrule`:*

*为了获得头文件集，我们运行一个 glob 表达式。例如:*

*把这一切结合在一起:*

*你可以在 GitHub 上找到完整的[工作示例。](https://github.com/njlr/buck-include-guards)*

*现在，我们的头文件可以在不包含守卫或`#pragma once`的情况下编写:*

*Buck 中的这个设置非常适合使用:*

*   *头文件中的零锅炉板*
*   *Buck 会自动检查新的头文件，因此构建总是最新的*
*   *Buck 将删除过时生成的报头*
*   *因为它理解目标图，Buck 将并行地生成头*
*   *Buck 将缓存生成的头，以便只在需要时才计算它们*
*   *我们不再依赖于人的准确性(包括警卫)或非标准特征(`#pragma once`)*

# *既然你在这里…*

*我们创建了 [Buckaroo](https://github.com/LoopPerfect/buckaroo) 来使集成 C++库变得更容易。如果你想尝试一下，最好从[文档](https://buckaroo.readthedocs.io/en/latest/)开始。你可以在 [Buckaroo.pm](https://www.buckaroo.pm/) 上浏览现有套餐，或者在[愿望清单](https://github.com/LoopPerfect/buckaroo-wishlist)上申请更多套餐。*

*[](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7) [## C++依赖管理的方法，或者我们为什么要建立 Buckaroo

### C++是一种不寻常的语言，因为它还没有一个占主导地位的包管理器(我们正在努力！).结果是…

hackernoon.com](https://hackernoon.com/approaches-to-c-dependency-management-or-why-we-built-buckaroo-26049d4646e7)*