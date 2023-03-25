# jQuery 牺牲了性能和代码效率吗？

> 原文：<https://medium.com/hackernoon/does-jquery-sacrifice-performance-and-code-efficiency-468aa967ef04>

![](img/ac195c83d6dd62ee3cf87b7d71d6c94d.png)

Racing — La course by [Frédérique Voisin-Demery](https://www.flickr.com/photos/vialbost/)

*Ollie R* 写信给我，询问使用 [jQuery](https://hackernoon.com/tagged/jquery) 而不是纯 [JavaScript](https://hackernoon.com/tagged/javascript) 是否以代码效率和性能为代价。

简答；是的。在您编写的代码和最终执行的底层代码之间添加通信层时，几乎总是会牺牲性能。

然而，尽管如此，我们为什么仍然使用 jQuery 的问题是一个更有趣的问题。

你可以阅读下面 Ollie 的完整邮件，如果你的好奇心被激起，请阅读我在页面下方关于 jQuery 和 JavaScript 的更深入的讨论！

> 所以，正如你提到的，我最近一直在涉猎少量的 JQuery，到目前为止，我必须说我对它有着复杂的感情。
> 
> 正如包装上说的，JQuery 基本上只是 JavaScipt，但是经过设置，您可以用更少的代码做更多、更快的事情。
> 
> 我还听说最大的网络公司也在使用它，比如谷歌和 Youtube。
> 
> 但是我不明白的是，JQuery 实际上只是 JavaScript。使用 JQuery，难道不是为了编码方便而牺牲效率吗？
> 
> 我的网站上有一些我知道可以用多种方式完成的事情；例如，我希望元素的背景闪烁红色。JavaScript 可以做到，JQuery 的 UI 升级可以做到，甚至 CSS 都可以做尝试。我不知道该选哪个。
> 
> 为了简化这封邮件，我想问的是 JQuery 牺牲了性能和代码效率吗？
> 
> 感谢你能提供的任何帮助

## jQuery 存在的原因

jQuery 项目始于 2005 年，目的是简化使用 CSS 选择器在页面上查找 HTML 元素所需的代码。

在基础语言之上创建更简单的代码库已经司空见惯很久了。jQuery 只是为 JavaScript 创建的许多这样的库之一，它绝不是第一个。

jQuery 的主要目标之一是让使用 JavaScript *变得不那么冗长*，这意味着开发人员应该能够编写更小、更简洁的代码来实现相同的结果。

## 可观察到的性能影响

对于大多数项目，jQuery 和 JavaScript 之间的性能差异并不明显。如今，Web 浏览器和计算机的速度足够快，人们更容易注意到网站加载许多单个文件或大图像的延迟，而不是 JavaScript 执行速度的微小差异。

对于小型网站和简单的 web 应用程序，jQuery 的优势通常会超过最小的性能影响。

对于拥有数千行代码的大型网站和 web 应用程序，或者使用 JavaScript 处理大量数据的网站和应用程序，性能绝对是需要考虑的因素。

人们已经创建了其他 JavaScript 库来处理不同的用例。构建一个基于网络的游戏和构建一个聊天应用有着非常不同的需求。你应该根据你具体项目的需求来选择一个 JavaScript 库，而不仅仅是“因为其他人都在用它”。

有 JS 库可以进行大量基于浏览器的图像处理，用于 3D 图形、数据可视化、动画或更传统的应用程序。您也可能根本不使用库，而是选择使用普通的 JavaScript。你甚至可以混合使用这两者，为你知道会从底层控制中受益的任务编写普通的 JS。

## 可维护性优势

那么 jQuery 为什么如此受欢迎呢？奥利在邮件中提出了这样一个问题:

> "使用 jQuery，难道不是为了编码的方便而牺牲了效率吗？"

是啊！但我想我会避免使用“只是”这个词。易于编码是优秀软件开发的一个非常重要的方面。

当编写几行 JavaScript 来美化你的网站时，编码的容易程度并不是非常重要；你是唯一一个在你的站点上工作的人，你只有几行代码需要浏览，复杂度很低。在这种情况下，我会建议你完全根据个人喜好选择使用 jQuery 还是 JavaScript。用让你开心的东西！

在一个更大的项目中，开发人员快速浏览代码、进行更改和添加功能的能力直接转化为公司的收入和用户的利益。这也有助于开发人员为公司工作的快乐程度。

jQuery 的流行很大程度上是因为它简单而一致的 API。从 [jQuery](http://jquery.com/) 主页:

> [jQuery]通过一个跨多种浏览器工作的易于使用的 API，使 HTML 文档遍历和操作、事件处理、动画和 Ajax 变得更加简单。

jQuery 的目的是简化操作页面元素所需的代码，并观察可能触发这些变化的事件。随着时间的推移，它已经发展到支持其他常见用例，如 Ajax，这是一种从外部源加载数据的方法。

即使浏览器实现 JavaScript 的方式略有不同，它也能在不同的 web 浏览器上一致地工作。没错，这是一个东西！JavaScript 在每个浏览器上的工作方式并不完全相同，因为每个浏览器都有不同的编程方式，并且会随着时间的推移而发展。

jQuery 项目跟踪浏览器之间的这些差异，以便当您运行 jQuery 代码时，它们可以在后台为正确的浏览器运行正确的 JavaScript 代码。

你可能会注意到，jQuery 下载页面上写着“jQuery 2.x 与 jQuery 1.x 具有相同的 API，但*不支持 Internet Explorer 6、7 或 8”。*jQuery 2 发布时，他们决定停止支持低于 9 的 Internet Explorer 版本，这样他们就可以简化自己的代码，让 jQuery 在现代浏览器中尽可能快地运行。更少的“如果”语句，更轻的代码！

知道浏览器差异在几年前是一个更大的问题可能是有帮助的，随着 JavaScript 的实现变得更加一致，现在你可能不会有太大的麻烦。除非你支持非常老的浏览器，否则你应该不会有任何问题来编写与所有现代浏览器兼容的普通 JavaScript。

jQuery 仍然会为您处理一些浏览器错误，如果您想阅读一些技术书籍，这里有一个[的好文档](https://docs.google.com/document/d/1LPaPA30bLUB_publLIMF0RlhdnPx_ePXm7oW02iiT6o/edit)列出了所有这些错误。

## 概括起来

如今，网站或应用程序很少使用普通 JavaScript 而不使用额外的支持库或框架。也就是说，如果适合您项目的需要，单独使用普通 JavaScript 仍然是完全可以接受的。像 JavaScript 这样相对较小的库不太可能对网站的性能产生明显的影响。

框架和库的存在是为了更快更容易地创建软件，但是没有一个选择可以统治所有的软件。总是根据它们的优点、它们的预期用途以及手头项目的需求来选择框架。

为了更好地了解 jQuery 到底为你做了什么，看看网站“[你可能不需要 jQuery](http://youmightnotneedjquery.com/)”——向下滚动，你会看到一些 jQuery 代码与具有相同功能的普通 JavaScript 样本的比较。

## 参考

*   [jQuery 基础|历史](https://jquery.org/history/)
*   你可能不需要 jQuery
*   [jQuery 的浏览器错误解决方法](https://docs.google.com/document/d/1LPaPA30bLUB_publLIMF0RlhdnPx_ePXm7oW02iiT6o/edit)