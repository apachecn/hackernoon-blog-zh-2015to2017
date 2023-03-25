# JavaScript 疲劳如何简化编码并增加收入

> 原文：<https://medium.com/hackernoon/how-javascript-fatigue-simplifies-coding-and-increases-revenue-46925c7efd47>

![](img/779e32894f6a48feb4641b429924fab7.png)

我们以前来过这里。在 BASIC 和 Fortan 中，过程编程和 goto 语句风靡一时。为什么任何人都应该停止使用一个非常好的工具来迎合一种叫做 OOP 的新时尚呢？

如此巨大的转变是艰难的。过渡到面向对象编程使我们能够构建出令人惊叹的产品，如果没有面向对象编程，这些产品将极具挑战性。改变核心基本面需要摒弃旧的方法，给新想法在现实世界中发挥的机会。克服 JavaScrip 疲劳的转变尤其困难，因为新工具违背了 OOP 和 MVC 的规范。但是没有人停下来解释原因。为什么你作为开发人员的生活会更好？为什么你的公司会受益？

在过去的两年里，我发布了使用这种新堆栈的应用程序。他们以 MVC 和 OOP 无法做到的方式帮助了我。我不太担心破损，因为我的破损少了。我花更少的时间管理测试框架，花更多的时间启用和迭代功能，使我的应用程序更有用。这让我能够让设计师和研究团队更快地学习和迭代我们的产品。企业赢得了更多的客户，并更快地了解他们的客户。让我们来分解 4 个最重要的工具，以及为什么它们每一个都使开发更容易，并提高您公司的底线。

![](img/47c48905bd8d5677d3f7d096d05959bb.png)

# 1.反应

新架构最关键的部分是[反应](https://hackernoon.com/tagged/react)。React 改进了传统 UI 开发的许多问题，很难简洁地描述它的好处。React 好处的关键在于它在一个组件架构中使用 [***纯函数来渲染 UI***](http://bit.ly/pure-views) 。

纯功能视图允许 React to ***将业务逻辑从 UI*** 中分离出来。构建稳定的前端应用程序的问题在于用户。用户以随机的顺序做随机的事情。他们不懂用户界面。拯救用户的方法是将他们与业务逻辑代码隔离开来。React 通过使整个 UI 只是底层静态数据的简单反映来做到这一点，对视图使用纯函数意味着给定任何静态 JSON，它将总是以完全相同的方式呈现。

React 中业务逻辑和 UI 的分离使得代码库 ***更容易搜索。*** 在 React 中，包含多个抽象的复杂类集合变成了非常基本的函数。我开发的最后一个 enterprise React 应用程序，我们让一个新的 jr .开发者在第一天自己添加了一个新特性，第二天就发布了。构建前端应用程序并没有我们想象的那么难；我们只是有太多复杂的工具。

凡事都有权衡要考虑。React 也没什么不同。这可能是与现有的强制性的、基于可变的框架的斗争。有一些迁移途径，比如对现有应用程序的一部分使用 React，或者将旧应用程序封装在 React 组件中，以便退出 DOM 管理。最后，如果开发团队看不到现有复杂的 OOP/MVC 框架中的缺陷，这将是一个硬推销。当你全押时，反应最好。

![](img/e2dfab0f3dd6d5c98ab60e4e665a6bc2.png)

# 2.网络包

Webpack 极大地改善了开发人员的体验，同时也极大地改善了用户体验。Webpack with Babel 的设置和配置可能会很痛苦，但是有两个很好的理由说明 Webpack 的重要性。

Webpack 对于开发最大的特点就是活编码。 ***现场编码一点也不像现场重装。如果你曾经使用过像 Java 或 C 这样的编译语言和使用像 JavaScript 这样的截取语言，你就会体会到其中的不同。实时编码使得在编辑器中输入并立即看到结果变得轻松。不得不打开 Chrome DevTools 或 alt-tab 打开浏览器并点击鼠标，这几乎是一种负担。只需更改一行代码并在浏览器中看到即时结果会更快。***

Webpack 改善了开发者的体验，对最终用户的影响更大，它将增加公司的收入。谷歌、微软、沃尔玛和亚马逊都有[案例研究](http://www.globaldots.com/how-website-speed-affects-conversion-rates/)显示，页面加载速度哪怕提高 1 秒，他们的转化率和收入都会增加 2%到 4%。在沃尔玛的案例中，他们会留下 2.4 亿美元。Webpack 只需要几个设置就可以极大地提高页面负载。在代码分割和使用 responsive image(使用 resize-image-loader)之间，我们将页面加载时间从 20 秒减少到了 2 秒。

![](img/3c303b036dfaedcea8fa94a4caac4c0a.png)

# 3.Redux

Redux 是一个基于函数式编程范例的状态管理工具。对于 ***减少 bug，减少浪费时间试图回购 bug***来说是天赐良机。Redux 之所以这么做，是因为它严格控制依赖关系。通常依赖关系被认为是导入。但是一个更完整的观点包含了任何可以*隐含*改变状态的东西。这将包括引用 *this* 的类，局部范围之外的对象，或者甚至创建一个对象并返回它，以便有人能够改变它的状态。简化和内心的平静来自于对突变的更好限制。Redux 使用静态数据和更容易进行单元测试的纯函数，减少了对模拟的需求，并提供了更高水平的防破坏保证。

静态数据为我们提供了整个应用程序在任一时间点的可序列化快照。这让 Redux to ***记录并稍后重放生产*** 中发生的每一个 bug。堆栈跟踪和 QA 的复制步骤忽略了如何找到程序错误的原因，即程序的状态。修复 Redux 中的一个 bug，只需要开发者下载用户的状态，重放和倒带检查所有的状态转换，就可以看到 bug 的原因。

Redux 的缺点是它是一种非常规范的业务逻辑建模方式。这些模式并不总是直截了当或切实可行的，特别是对于异步、协调离线和在线以及透明地从 web 服务获取数据。还有其他工具和插件可以帮助解决这些问题。Redux 需要重写您的代码库，但带来了一些巨大的好处。

![](img/ac8474c0941fb7a85632491f2fe76b0f.png)

# 4.ClojureScript

ClojureScript 可能是列表中最后一个核心工具，也是最强大的一个。ClojureScript 减少了许多开发人员认为编码固有的脆弱性。在开始使用 ClojureScript 时，我的主要建议是，在你真正使用它之前，不要做任何判断。我听到的对 ClojureScript 的常见反应是“我不喜欢你的脸/语法”。如果有机会的话，它的语法优于点符号是有充分理由的。Clojure 已经存在 10 年了。工具、语言和社区都非常成熟。ClojureScript 结合了优秀的工具、更好的核心原语、异步模式及其对静态类型的改进，使其优于其他选项。

## 不可改变。

ClojureScript 中的所有原语永远都不能更改。 ***突变导致 bug 所以 ClojureScript 默认是不可变的。*** 不可变的数据使得不经意间代码库中某个部分的 bug 很难影响到其他地方的东西。正常的推回是不可变数据浪费大量内存，速度慢。在 ClojureScript 中，它比大多数可变数据使用模式*更快，并且更好地使用内存。更好的核心原语提供不同的性能特征。抵制微优化一切的冲动，因为这不像 OOP。

## 可重构性和可读性。

最好的特性是可重构性和可读性。 ***函数式编程优化提升开发者效率。学习曲线很高，但回报更高。仅用了 3 周时间，我就从对 ClojureScript 一无所知变成了一个可以投入生产的应用。关于 ClojureScript 代码库最令人惊奇的事情是，当我几个月后回到它的时候，我仍然可以阅读它。更令人吃惊的是，我没有重构旧代码的强烈愿望。与 OOP 重构相比，ClojureScript 中的典型重构非常简单。我的大部分重构只是将功能转移到其他文件，或者将数据结构与转换数据分离开来。我工作过的 Clojure 代码库是清晰的、声明性的和简洁的。他们很少需要跳来跳去理解数据流。***

## 类型。

静态类型不是目标；目标是在编写、编译和运行代码时有更好的错误处理。***ClojureScript one ups static typing system with Spec。*** Spec*通过查看整个数据验证问题，不仅仅是类型。Flow 或 TypeScript 只提供编译时检查。当你点击一个 web 服务或者访问一个不是你创建的 JS 库的时候，你不能保证类型。规范是处理编译时类型检查、运行时数据验证、运行时断言以及处理硬错误和软错误统一方法。它还可以为您的测试创建存根数据，为您编写测试，甚至在测试中断时告诉您它在代码中的确切位置和原因。是的，你没看错:Spec 会为你生成测试。它还将测试你以前从未考虑过的条件。在使用 Spec 之后，其他测试实践感觉像是在建造沙堡；它们需要更长的时间来维持，而结果却更差。

## 异步。

ClojureScript async 看起来很有趣。承诺并不适用于所有类型的异步工作。 ***ClojureScript core.async 就像《超级马里奥兄弟》中的绿色曲速管*** 在《超级马里奥兄弟》中，一根绿色曲速管就能把马里奥送到关卡、世界或游戏中的其他任何地方。这就是 ClojureScript 的通道为您的代码库的异步工作所做的事情。它使用 Go Lang 风格的通道，可以在一个地方创建，并在代码库中的任何其他地方轻松共享。它提供了 Rx 的所有优点，而没有任何复杂的函数耦合和流合并问题。Channels 使异步代码读起来像线性代码，这减少了读者的认知超载，因此有更少的复杂的抽象错误。凭借所有这些令人惊叹的功能，ClojureScript also one ups 还可以通过在通道上添加传感器来扩展通道。

## 传感器。

这是一个很大的词，但它们基本上只是一个有一个参数的函数。*(用户)= >用户，名字*可用作传感器。 ***传感器使用简单的、不可知的函数以高性能的方式处理多个变换。使用不可知函数意味着可重用性更高。任何对干方法论有共鸣的人(不要重复自己)都会喜欢换能器。***

为了更好地理解传感器，让我们花一点时间回到超级马里奥对通道的类比。在游戏中，我们有一个功能，当马里奥触摸火花时，他会变成火马里奥。我们可以把同样功能放在一个绿色的曲速管“通道”上，当马里奥从管子里出来时，他就会解雇马里奥。这是相同的功能，但用在不同的上下文中。我们还可以在代码库中的另一个地方添加一个超级 Tanooki 传感器到同一个“通道”, Mario 将作为 Fire Tanooki Mario 出现。将马里奥的所有朋友都通过这个通道，他们只需要简单的、不可知的函数就可以得到相同的变换。

如果你已经读到这里，那么剩下的唯一一件事就是花 5 分钟自己尝试 ClojureScript。一个命令(见下文)将安装您使用 Atom 编辑器开始实时编码 ClojureScript 所需的一切。在您看到 ClojureScript 工具有多棒之后，请查看[试剂对 ClojureScript 的介绍](http://reagent-project.github.io/)和 [Andrew 的教程系列，从 JS 到 ClojureScript](https://www.niwi.nz/cljs-workshop/) 。如果您有任何问题，请查看 Clojurian Slack 频道或在 Twitter @puppybits 上给我发消息。

## ClojureScript 在一个命令中设置

```
apm install parinfer && \
brew cask install java && \
brew install leiningen && \
lein new figwheel hello-cljs -- --reagent && \
cd hello-cljs && \
atom src/hello_cljs/core.cljs && \
lein figwheel && \
open http://localhost:3449
```

## 附录

[1/沃尔玛页面加载速度提升对话](http://www.globaldots.com/how-website-speed-affects-conversion-rates/)
[2/Simple made Easy—Rich Hickey](https://www.infoq.com/presentations/Simple-Made-Easy)
[3/不可变数据—David Nolen](https://www.youtube.com/watch?v=mS264h8KGwk)
[4/Spec:Agility&Robustness—Stuart Halloway](https://www.youtube.com/watch?v=VNTQ-M_uSo8)
[5/clo jure Help](https://clojuredocs.org/)
[6/clo jure Docs](http://clojure.org/api/api)
7[/Intro to ClojureScript with Reagent](http://reagent-project.github.io)
8

感谢[阿米莉亚·施密特](https://medium.com/u/e9dc50ed7c76?source=post_page-----46925c7efd47--------------------------------)和[克里斯·奥克曼](https://medium.com/u/5540d0047484?source=post_page-----46925c7efd47--------------------------------)。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)