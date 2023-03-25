# 夏娃远大理想的坚实基础

> 原文：<https://medium.com/hackernoon/the-rock-solid-foundation-for-eves-big-vision-225b80b91e11>

欢迎来到我关于 [Eve](http://witheve.com/) 的系列文章的第五部分，这是一种令人兴奋和着迷的新编程语言。

![](img/5d00ee5f811d19fdacd64945cf7dc021.png)

*   [*I .*Eve 如何统一你的整个编程栈](https://hackernoon.com/how-eve-unifies-your-entire-programming-stack-900ca80c58a7)
*   [*二世。*当逻辑编程遇上 CQRS](https://hackernoon.com/when-logic-programming-meets-cqrs-1137ab2a5f86)
*   [*三世。*挣脱我们的镜链](https://hackernoon.com/throwing-off-our-scope-chains-7567beb2d0b6)
*   [*四世。* Smalltalk 和蛋白质编程](https://hackernoon.com/smalltalk-and-protein-programming-4da245ac93e2)
*   ***五、*夏娃远大理想的坚实基础**
*   [*六。*为什么 Eve 是实时应用的完美选择](https://hackernoon.com/why-eve-will-be-perfect-for-realtime-apps-92b965b80ad)

> 还有很多事情要做，还有很多事情要弄清楚，但幸运的是，我们有一个良好的基础，我们可以利用它来确保事情不会偏离轨道太远。很高兴知道，只要我们可以将我们的语义解析回 X，我们就可以保持 X 的属性。这是一件非常困难的事情，但结果似乎是这样的:)

— [在](https://medium.com/u/2826fea8bb47#1 和#2 之间的一个尴尬的中间地带:你将状态块从你的 UI 视图中分离出来，并将它们隔离在一个只支持函数式编程的区域中。)</p><p id=)[第一部分](https://hackernoon.com/how-eve-unifies-your-entire-programming-stack-900ca80c58a7)中，我指出 Eve 中的一个`bind`块类似于 MobX 中的一个`computed`表达式。直觉上，两者都类似于电子表格公式:

![](img/a73be6a4994c14da17a86f518e284c87.png)

@computed function B1() {return (Math.round(A1) / 100) * 100}

有了 Eve，您可以享受语言级的依赖跟踪，在代码中的任何地方使用`nearestHundred`，就像在电子表格中的任何地方使用单元格`B1`一样。

# 运行时调试

## 观察表情

![](img/5a60e1571f9ee08c7bd99a3d4a9b3254.png)

运行时调试时使用观察表达式的频率有多高？这通常不值得努力，因为状态与作用域链[紧密耦合。](https://hackernoon.com/throwing-off-our-scope-chains-7567beb2d0b6)

在 Eve 中，你也不需要手表表情，因为一个`search`段已经*就是*一个手表表情了。如果你想使用一个`search`块进行调试，你所要做的就是添加一个`commit`或者`bind`段来写入`@view`数据库。

## 热重装

热重载通常是一种奢侈品，必须煞费苦心地将其融入大多数语言和框架中。但是 Eve 在第一天就发布了它，这要感谢该语言的热重加载友好的构建模块。

在编辑器中，每个代码块都可以用勾号激活或停用，一切都会神奇地调整。

## 可串行化状态

正在运行的 Eve 程序的整个状态很容易序列化到一个文件中。您的用户可以将“状态文件”附加到他们的错误报告中，并且您可以重现他们的确切状态:

![](img/0f2d7c67a8db8477aa61fb2c26a550bd.png)

[https://www.youtube.com/watch?v=TWAMr72VaaU](https://www.youtube.com/watch?v=TWAMr72VaaU)

## 更智能的 UI 检查工具

web 浏览器的 inspector 工具很好地向您展示了 UI 的状态，但是它不理解您的代码如何与该状态相关联。Eve 理解为什么 UI 看起来是这样的。

![](img/e3e1d0273bd0dc75380cd779b754ab41.png)

演示视频已经展示了令人印象深刻的 GUI 特性，用于在检查 UI 时跳转到相关的代码块。

## …以及更多

除了 Eve 对现有调试工具的改进之外，我们可以期待 Eve 社区构思出比我们以往更好的调试工具。事实上，我敢想象 Eve 会让主机打印过时。

对了，你知道 Eve *没有*的什么调试功能吗？堆栈跟踪。

![](img/41c882ef70a413154d08d3011c18bd6b.png)

“Focusing on the machine and ignoring the human factors of software engineering have led us down a difficult road.” — [witheve.com](http://witheve.com)

# 单元测试

在传统的单元测试中，通常很难伪造整个基于范围链的状态。此外，有时很难隔离一个模块化单元进行测试。使用 Eve，您可以随时测试单独的代码块，并清楚地了解它们的数据流。

# 性能优化

如果您曾经使用过 CPU 分析器来找出如何优化某个地方的循环，那么您就会知道，理解哪一级调用堆栈是真正的罪魁祸首是一件痛苦的事情。比方说函数 A 调用函数 B，函数 B 调用函数 c，如果 A 在大量使用 CPU，那不一定说明 A 写得不好；也可能是 B 或者 c。

Eve 没有调用栈；代码块不能直接调用其他代码块。因此，如果某个东西很慢，您可以很容易地隔离哪个代码块是负责任的。

如果您曾经使用过内存分析器来改善内存泄漏，那么传统的内存泄漏在 Eve 中是不可能的！过时状态没有办法陷入错综复杂的闭包链中，所以不需要内存分析器。

扪心自问，你处理过多少次内存泄露？您遇到过多少次将大量记录插入数据库的错误？好极了，因为你的 Eve 程序占用大量内存的唯一方法是你故意把大量记录放入本地数据库。

# 分布式数据和计算

很难管理并行发生的事情的混乱，无论是数据还是计算。最初基于[迪达勒斯](https://databeta.wordpress.com/2010/01/05/introducing-dedalus/)的语言[布鲁姆](http://bloom-lang.net/features/)的网站解释了这个问题:

> 传统分布式编程中的许多痛苦来自这种不匹配:程序员被期望从有序的编程模型过渡到执行他们代码的无序现实。

夏娃是一种“无序的语言”。Eve 唯一的排序保证是**同步块。**在每个时间步，所有代码块(每个代码块都有一个或多个`search` / `bind` / `commit`段)读入该时间步的世界快照，它们都写入下一个时间步的世界快照。有点像 [Smalltalk 和蛋白质编程](https://hackernoon.com/smalltalk-and-protein-programming-4da245ac93e2)。

Eve 有可能让分布式数据和计算变得更容易访问:

*   编写简单的 Eve 代码来管理大规模的复杂数据事务，这种事务需要锁定多个遥远的碎片。
*   通过编写与单核机器相同的代码，使用 Eve 将计算密集型任务分散到多核 CPU 或云中的大型服务器群。

Eve 团队将这一愿景称为“**世界规模的计算机**”。

**下篇:** [*六*。为什么 Eve 是实时应用的完美选择](https://hackernoon.com/why-eve-will-be-perfect-for-realtime-apps-92b965b80ad)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)