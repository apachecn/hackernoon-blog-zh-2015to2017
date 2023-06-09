# 缓解哈斯克尔的恐吓目光

> 原文：<https://medium.com/hackernoon/easing-haskells-intimidating-glare-2341095e88aa>

这篇文章最初发表在 2017 年 1 月 23 日的[周一早晨哈斯克尔博客](https://mmhaskell.com/blog/2017/1/23/easing-haskells-intimidating-glare)上。查看[博客](https://www.mmhaskell.com)了解更多 Haskell 内容！

我以前的所有文章都讨论了 Haskell 的基本语言特性和概念。我的希望是给新的 Haskellers 提供合适的开始材料来帮助他们开始。即使只是作为一种语言，Haskell 也是复杂的。学习它需要克服许多技术挑战和范式转变。对于那些来自命令式语言的人来说尤其如此。

然而，本文将关注同样重要的东西。众所周知，大多数人认为 Haskell 不仅仅是一种在技术上难以学习的**语言，还是一种令人生畏的**语言。这无疑存在心理障碍。人们放弃 Haskell 的比率似乎比大多数其他语言都要高。通过命名这些问题，我们可以克服它们。

# 学术语言

人们一直认为 Haskell 主要是作为一种研究语言。它建立在 lambda 演算之上，可能是最简单、最纯粹的编程语言。这让它与抽象数学中的酷概念有了很多联系，主要是教授和博士的领域。学生。这种联系是如此优雅，许多数学思想都可以在 Haskell 中很好地表达出来。

但是这个连接在可访问性上有一个**代价**。重要的 Haskell 概念包括函子、单子、范畴等。这些都很酷，但是没有数学学位的人很少对这些术语的含义有任何直觉。将这些与其他语言中的术语进行比较:类、迭代器、循环、模板。这些术语更加直观，因此使用它们的语言在可访问性方面有着自动优势。

除了这个术语，巨大的学术兴趣是一件好事，而不是坏事。然而，在生产方面，工具还不够。维护一个大规模的 Haskell 项目实在是太难了。结果，公司没有动力使用它。这意味着学术影响力很少甚至没有平衡。

# 知识分布

哈斯克尔学术领先的最终结果是一个扭曲的知识基础。学术界的本质是相对较少的人在相对较少的问题上花费大量的时间。考虑另一个学术领域，比如病毒学。有些专家对病毒的了解达到了极高的水平，而我们其他人却知之甚少。没有业余爱好的病毒学家。不幸的是，这种分布不利于向新的人介绍一个话题。

自然，人们必须向那些知道得比他们多的人学习。但事实是他们不希望他们的老师好太多。向不久前处于你的地位的人学习会有很大的帮助。他们更有可能记得他们早期遇到的陷阱和挫折，所以他们能够帮助你避免它们。但是当分布向极端倾斜时，就没有**中产阶级**。能够以最佳方式教授新学员的人越来越少。除了不记得以前的错误，专家们还倾向于使用过于复杂的术语。新人们可能会对此感到恐惧和绝望。

# 扭转生产趋势

上述生产工作的缺乏在很大程度上造成了这种差异。许多其他语言，如 C++，有强大的学术追随者。但是由于许多公司在生产中使用 C++，它不像 Haskell 那样面临知识分布的问题。使用 C++的公司别无选择，只能培训人们使用这种语言。这些人中的许多人坚持使用这种语言足够长的时间来训练下一代。这就创造了一条看起来更加**正常的**知识分布曲线。

对 Haskell 来说，好消息是在过去几年中有了重大的工具改进。这带来了语言的复兴。越来越多的公司开始在生产中使用它。更多的聚会正在发生；越来越多的人正在为最关键的任务编写库。如果这种趋势继续下去，Haskell 将有望达到一个**临界点**，使知识分布曲线正常化。

# 关键的洞察力

如果你对学习 Haskell 感兴趣，或者过去曾经尝试过学习 Haskell，有一件关键的事情需要知道。虽然抽象的数学很酷，但是理解它是不必要的。单子是必不可少的，这是不可否认的。但是范畴理论对于你将要解决的大多数日常问题来说是多余的。几十种语言扩展可能看起来令人生畏，但是您可以一个一个地学习它们。

在上次 Haskell 交流中，来自渣打银行的 Don Stewart 做了一个关于公司使用 Haskell 的演讲。他解释说他们很少使用普通 Haskell 结构之外的东西。**他们就是不需要**。比方说，你能用镜头做的任何事情，没有它们你也能完成。

Haskell 不同于大多数其他编程语言。它以那些语言没有的方式约束你。但是这些限制并不像看起来那么有约束力。不能使用 for 循环。所以用递归。你不能重新分配变量。因此，为表达式创建新的名称。你只需要一步一步来。

# 采取行动

如果这启发了你开始使用 Haskell，看看这个[清单](https://www.mmhaskell.com/checklist)来学习你开始使用所需的工具。

请务必查看我们关于[函子](/@james_32022/the-easiest-haskell-idiom-2dacf217ad2d#.d955wjwo0)的下一篇文章，了解更多信息！

如果你已经熟悉了基础知识，并想更进一步，你应该看看我们的[工作簿](https://www.mmhaskell.com/workbook)。您将学习递归，并尝试 10 道练习题来测试您的技能！

# 注意

*   至少对于他们正常的 Haskell 代码来说是这样。他们的一些代码是用他们的专有语言 Mu 编写的，Mu 是建立在 Haskell 之上的，但显然不同。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！