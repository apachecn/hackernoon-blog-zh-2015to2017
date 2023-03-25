# 从 JavaScript 跳到 PureScript

> 原文：<https://medium.com/hackernoon/make-the-leap-from-javascript-to-purescript-5b35b1c06fef>

![](img/1b68b9f44fe9fd816b12cece2a2db058.png)

> 注意:这是对**从 JavaScript 到 PureScript** 系列教程的介绍。我将大约每月发布一次教程。所以要经常回来，还有很多这样的事情要做！
> [索引](/p/index-make-the-leap-from-javascript-to-purescript-a1566d657e9c) | [教程 1 >](/@kelleyalex/create-linear-data-flow-with-container-style-types-box-702a3808ad2c) [教程 27 > >](/@kelleyalex/build-a-data-flow-for-a-real-world-app-8971f0c72b58)

# 介绍

“从 JavaScript 到 PureScript 的飞跃”采用了 JavaScript 中一些最流行的函数式编程(FP)抽象，并演示了如何在 PureScript 中实现它们。我在 egghead.io 课程[的许可下借用了系列大纲和 JavaScript 代码示例](https://egghead.io/courses/professor-frisby-introduces-composable-functional-javascript)[Frisby 教授介绍了 Brian Lonsdorf](https://github.com/DrBoolean) 的可组合函数 JavaScript 。我要感谢 Brian(pure script 的热心程序员)对这个项目的支持。

# 这个系列的动机

在过去的几个月里，我注意到一些好奇的 JavaScript 程序员出现在 PureScript 论坛上(例如 [gitter.im](https://gitter.im/purescript/purescript) ),询问关于 FP 和 PureScript 的问题。作为回应，经验丰富的函数式程序员强烈推荐 Brian 的课程，作为熟悉 FP 的第一步。然后，一旦他们舒适地理解了 JavaScript 的基本抽象，他们就会被鼓励回来开始使用 PureScript。我非常同意这个建议！同时学习 FP 抽象和一门新的编程语言已经够难的了。但是，随着时间的推移，如果您坚持使用 JavaScript 中的 FP，那么您可能会发现很难不偏离，然后您就又回到了您的旧的和命令式的方式。或者，PureScript 是一种真正的 FP 语言，可以编译成 JavaScript！所以我写了这些教程来帮助你从 JavaScript 过渡到 PureScript，向你展示如何实现 Brian 的课程中教授的相同概念。

# 为您导航

如果你是 FP 的新手，或者你还没有攻克 Brian 的课程，那么我强烈建议你首先这么做(我确实这么做了，它对我帮助很大)。然后，一旦你理解了基本的抽象，回来，我将用 PureScript 向你展示它们。我已经尽力解释了这些概念，但布莱恩是教授它们的大师。此外，这些教程并不能取代传统的 PureScript 入门教程。因此，在一头扎进这些教程之前，花点时间学习一点 PureScript 语法。

# 使用 PureScript 代替 JavaScript 的优势

您可以放心了，因为 JavaScript 支持 FP 的一些最重要的特性，包括一流的匿名函数和闭包。然而，JavaScript 必须服务于多个主人，包括面向对象和命令式编程。因此，在使用 JavaScript for FP 时会有一些限制和妥协。特别是，它缺少静态类型系统、强制纯度和不变性。其中一些差距可以通过添加静态类型检查器(参见 [TypeScript](https://www.typescriptlang.org/) )、不可变集合(参见 [Immutable.js](https://facebook.github.io/immutable-js/) )和 FP 抽象库(参见 [RamdaJS](http://ramdajs.com) )来缓解。尽管如此，你在 JavaScript 中完成 FP 主要是按照惯例，通常是用我上面提到的“创可贴”拼凑起来的。一个函数式 JavaScript 程序员必须时刻保持敏锐，以创建避免副作用的纯函数。但我认为这给程序员带来了太多的认知负担，最终会干扰应用程序的编码。谢天谢地，有合适的 FP 语言可以消除编译优化 JavaScript 时的这种摩擦——有人说过 PureScript 吗？

PureScript 被单独设计为一种 FP 语言。它是一种小型的强类型语言，可以编译成人类可读的 CommonJS 和其他语言。因此，您已经用一种语言涵盖了客户端和服务器端应用程序——这再好不过了！您还会发现所有您听说过或读到过的 FP 语言结构的表示，包括 currying、模式匹配、尾调用优化、高阶和高阶类型。最后，PureScript 没有运行时系统添加到您的下载足迹中，并且(**鼓点请**)有一个不复杂但非常强大的与 JavaScript 相互作用的 FFI！因此，如果您还没有找到对您最喜欢的 JavaScript 模块中的函数的支持，那么自己包含它们并不困难(我将在教程中向您展示一些例子)。

# 教程布局

我已经创建了一个 GitHub 存储库，其中包含这些故事的降价版本(即 README.md)以及代码示例。你可以在这里克隆它[，然后](https://github.com/adkelley/javascript-to-purescript)[获取上游](https://help.github.com/articles/syncing-a-fork/)以备将来更新。

每个教程都放在一个名为“tut##”的单独文件夹中，其中##是一个数字(例如“tut01”)，对应于 Brian 的[课程](https://egghead.io/courses/professor-frisby-introduces-composable-functional-javascript)中的视频##。这里有附带的教程 markdown(如 tut01/README.md)，说明了从 Brian 对应的视频(如 video1)中抽象出来的内容，以及如何在 PureScript 中实现。

我已经设置了文件夹，这样您就可以使用`npm install`第一次运行 PureScript 代码示例，它将 Spago 和 PureScript 作为依赖项安装。然后，键入`npm run all`，它将编译并运行源代码。此后，键入`npm run exec`再次运行它。还有个别脚本`npm run clean`、`npm run install`、`npm run build`、`npm run exec`；分别用于清理、安装、构建和执行教程示例。

当然，所有这些都假设您已经安装了`npm`包管理器。这些脚本也依赖于`rimraf`(节点的 UNIX 命令`rm -rf`)和`Spago` (PureScript 的包管理器和构建工具)。我在下面解释这一切。

# 配角

PureScript 社区现在已经决定使用 [Spago](https://github.com/purescript/spago) 包管理器和构建工具。Spago 是在[pure script](http://www.purescript.org/learn/getting-started/)入门中给出的建议，我建议您在继续之前通读一遍。根据我目前的经验，Spago 是 PureScript 工具的一个受欢迎的补充，它提供了一个很好的 UX，具有最小的依赖性和可重复的构建。

# 在 PureScript 中启动并运行

每个教程中的`package.json`文件包含`spago`和`purescript`作为开发依赖项。因此，没有必要全局安装它们。在本教程修订时，Spago 的最新 npm 版本是`0.20.3`而 PureScript 是`0.14.3`

# 安装编辑器插件

大多数编辑器都有插件来支持语法高亮、构建支持、REPL(读取、评估、打印、循环)和自动完成。你可以在这里找到为你喜欢的编辑器安装这些插件的信息。

# 运行您的第一个 PureScript 程序

假设您已经安装了 PureScript 和 Spago，那么使用 PureScript 开始您的函数式编程之旅再容易不过了:

```
$ mkdir purescript-hello 
$ cd purescript-hello
$ npm install -D spago purescript 
$ spago init 
$ spago build
$ spago run
```

# 我最喜欢的 PureScript 工具和参考

1.  [PureScript by Example](https://book.purescript.org) 是 PureScript 的官方书籍，最初由 Phil Freeman(pure script 语言的作者)编写，现在由[社区 fork](https://github.com/purescript-contrib/purescript-book) 维护。
2.  Pursuit 是 PureScript 文档的发源地；很快会成为你最好的朋友
3.  [尝试 PureScript](http://try.purescript.org/) 允许你在浏览器中尝试 PureScript 的关键例子。你也可以自己创造。
4.  psc-ide(随编译器分发)提供编辑器支持，包括 [atom](https://github.com/nwolverson/atom-ide-purescript) 、 [emacs](https://github.com/epost/psc-ide-emacs) 、 [vim](https://github.com/FrigoEU/psc-ide-vim) 和 [visual studio](https://github.com/nwolverson/vscode-ide-purescript)
5.  [论述](https://discourse.purescript.org/)讨论 PureScript。
6.  [FP Slack](https://functionalprogramming.slack.com/#/) 有#purescript 和# pure script-初学者频道。

# 前进！

[> **教程 1**](/@kelleyalex/create-linear-data-flow-with-container-style-types-box-702a3808ad2c)

当我写教程的时候，我会添加额外的链接。但是如果你想向前看，那么 Brian 的[视频](https://egghead.io/courses/professor-frisby-introduces-composable-functional-javascript)中的大部分代码样本已经被移植到 PureScript，并且可以在 [Github](https://github.com/adkelley/javascript-to-purescript) 上获得。但是我可能会在编写附带教程时修改它们。

*编辑于 2021 年 8 月 14 日，碰撞到 Spago 0 . 20 . 3&pure script 0 . 14 . 3，删除了对鲍尔*的引用

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)