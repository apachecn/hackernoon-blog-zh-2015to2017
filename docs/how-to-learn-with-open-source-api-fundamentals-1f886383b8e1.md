# 如何学习开源 API 基础知识

> 原文：<https://medium.com/hackernoon/how-to-learn-with-open-source-api-fundamentals-1f886383b8e1>

## 观察流行的开源项目如何记录它们的公共 API，以及它如何应用于闭源项目

![](img/1ba714a9204315054ec5447dd856cffb.png)

A design artwork showing interconnected plugs with an engine in the center

除了像 Linux 这样的大成就之外， [**开源**](https://hackernoon.com/tagged/open-source) 生态系统充满了可重用的项目，这些项目处理各种各样的技术问题，如框架、库、服务器、数据库等。正如鲍勃大叔在[【清洁建筑】](https://8thlight.com/blog/uncle-bob/2012/08/13/the-clean-architecture.html)上所说，它们只是“细节”。它们代表了开发人员不需要考虑的问题，如果其他人构建的合适的解决方案已经可用的话。

大多数盈利性组织也以一种**闭源**的方式坚持相同的可重用项目原则。当开源不适合时，他们建立内部项目来处理内部问题。他们为某个问题构建内部项目的原因可能各不相同。这可能是因为没有开源项目解决方案来解决他们的问题，也可能是因为这个问题对于他们的领域来说太具体了，对于开源项目来说没有意义。

然而，有一样东西对开源和闭源组织都很有价值:公共 API 文档。

我们从一些流行的开源项目中了解到，库的[内部不应该向使用它的人公开](/@fagnerbrack/library-internals-do-not-exist-1ff50a30e3b0)。其原因与我们在系统内部构建抽象的原因是一样的。我们将复杂性限制在具有高内聚性的小模块和组件中，并遵循软件工程的几个原则来保持它们的可维护性。

对于开源或闭源 API 也可以这样做。在这种情况下，抽象以人类可读文档的形式存在，而不是强迫消费者依赖组成 API 的类或函数的原始定义。一些开源项目，如蓝鸟、httpie 和 pageres，似乎遵循相同的哲学，因为它们的文档质量很高。

> 公共 API 文档的目的类似于[编程](https://hackernoon.com/tagged/programming)语言的[封装构造](https://en.wikipedia.org/wiki/Encapsulation_(computer_programming))的目的，即通过公开可供开发人员使用的人类可读接口来抽象复杂性

有很多方法可以使用一个可重用的 API。然而，它越缺乏人类可读的形式，就越难有效地消费它。以下是我们在使用这样的 API 时的选择，按照从最有效到最不有效的顺序排列:

1.  **阅读人类可读的文档，**例如，可以从允许生成可浏览 API 的源代码或自述文件中构建。
2.  阅读记录项目支持的测试。如果它们以人类可读的形式清楚地描述了内部的行为(比如当使用 BDD——行为驱动开发——编写测试时),这是很有用的。
3.  **视察公共成员。通过这种方式，我们可以根据 API 的高级目的以及公共成员的定义和命名方式来推断支持什么。**
4.  **寻找网上例子或询问其他消费者。**对于开源 API，我们可以在网上寻找例子。然而，对于一个闭源 API，我们需要询问项目中使用过该 API 的其他成员，因为这样的信息不太可能存在于网上的任何地方。

以上每种方法都有积极和消极的影响。出于本文的目的，我将关注社区通常认为交流 API 最有效的方式，即**“人类可读文档”**。

> 公共 API 越缺乏人类可读的文档，就越难有效地使用它

在**开源**中，作者倾向于不太关心人类可读的 API。除非项目已经很受欢迎或者得到了盈利组织的支持，否则编码是在开发人员的无薪空闲时间完成的。在空闲时间工作的开发人员通常更关心的主要目标是**构建有趣和有用的东西**而不是为别人编写文档。

然而，这种心态可能会违背自己的主要目标。

创建开源项目的一个主要原因是为了减轻其他人所经历的痛苦。这个项目被公开，希望其他开发者发现这个解决方案**有趣并且有用**。通过这种方式，他们可以开始使用它并为之做出贡献，从而使它更加健壮。如果没有人类可读形式的公共文档，除非项目非常受欢迎，否则消费者不会知道什么是受支持的 API。这将减少有人花时间贡献或使用它的机会，这会使项目看起来**不那么有趣或有用**。

而且，这使得作者无法理解哪些特性是受支持的，哪些是不受支持的。这使得重构和改进变得非常困难，因为每一个改变都需要被认为是一个突破性的改变，这是由于在特性和消费者之间缺乏契约。

> 没有人类可读形式的公共 API 会降低吸引贡献者或用户加入开源项目的机会

在**的封闭源代码**中，作者也倾向于不关心人类可读的 API。

一个项目开始的时候，是从一个沟通摩擦低、代码基数小的小团队开始的。即使在小范围内，在一个单独的可重用项目中分割一个关注点的需求仍然存在。然而，由于项目的规模和团队的规模，开发人员倾向于不优先考虑人类可读的 API 文档，而更喜欢专注于编写代码。

这种心态也可能违背营利性组织的主要目标。

当项目扩展时，很难找到空间来记录没有记录的内容。随着时间的推移，由于未记录的 API 使用，项目变得越来越僵化。即使团队在以后开始记录东西，也有可能开发人员是不同的，因此什么是受支持的，什么是不受支持的都在那些已经离开的人的脑子里。这可以通过所谓的[卡车系数](https://en.wikipedia.org/wiki/Bus_factor)来衡量。卡车因素定义了当重要的知识不能在参与项目的人员之间共享时的风险因素，这会妨碍其他人有效地继续工作。

> 时间流逝得越多，就越难改变一个未记录的项目，因为很难追踪它是如何被消耗的

缺乏人类可读的 API 文档带来的另一个问题是新开发人员加入项目的问题。如果没有清晰的文档，对于从未参与过该项目的人来说，在没有大量帮助的情况下，开始改变或使用它将花费更多的时间。他们需要理解内部机制，看看代码是如何被使用的，哪些 API 是隐式支持的，哪些是不支持的。

> 当一个项目没有文档记录时，新成员的成本会相当高

在**开源**中，如果目标是吸引贡献和被消费，投入大量精力以清晰易读的形式记录公共 API 是必须的。即使项目的代码解决了一个大问题并且非常有用，糟糕的文档也会降低它的潜力，因为消费者很难意识到它的有用性。除非它非常受欢迎，否则它不太可能达到相当高的相关性水平来吸引所需的受众。

在一个盈利组织的**闭源**环境中，目标是产生利润，因此有理由想象一个可重用的内部 API 可以在没有文档的情况下存在一段时间。这些情况通常是当项目仍然是一个廉价的原型，只有少数开发人员关心它，并且没有感觉到交流的摩擦。然而，当项目开始变大时，如果我们想继续关注“利润”目标，就有必要添加文档。否则，随着时间的推移，沟通的摩擦将会增加，这将开始损害其交付功能或缺陷修复的能力。

作为一般的实践，我们可以认为[原型倾向于成为永久的解决方案](https://hackernoon.com/how-to-prevent-falling-in-the-trap-of-the-next-big-project-618fe7cc4ce9)，因此我们可以通过不推迟文档来更早地行动以防止这种情况发生。然而，我们需要承认，在营利性组织中，当项目处于初始阶段时，没有建立适当的文档不会产生直接的成本。然而，在开源领域，从一开始就没有文档会直接损害吸引贡献的目标。

> 对于闭源社区来说，文档是非常重要的，大部分是大规模的。然而，对于开源来说，这是必不可少的。

关于文档的一个难题是，项目的作者通常对系统如何工作和支持什么有完整的心理背景，因此他们更有可能在为公共 API 文档提供有用的改进方面失败。解决这个问题的最佳方式是反馈和消费者可能遇到的问题。一个项目的作者，不管是开源的还是闭源的，都应该鼓励社区成员帮助改进文档，而不是给出问题的答案。[这个问题](https://github.com/js-cookie/js-cookie/issues/177)就是一个很好的例子，是一个消费者的问题引发了一个改进项目文档的行动。

为公共文档提供反馈的最佳个人不是那些使用它的人，而是那些**刚刚开始使用它的人。他们没有项目内部的知识，因此他们更有可能发现作者不会发现的问题，而其他消费者在开始使用公共 API 时会发现。这意味着新人的文档贡献应该和项目的代码贡献一样有价值，如果不是更多的话。**

> 新人的文档贡献应该和项目的代码贡献一样有价值，如果不是更多的话

现在让我们结束这一切。

对于盈利组织来说，在闭源项目中不记录公共 API 是有意义的。然而，在开放源码中，从一开始就没有记录公共 API 可能会真正损害项目的主要目标。

当参与一个盈利性组织的开源或闭源项目时，要知道公共 API 文档可能和代码本身一样重要。帮助改善它对于吸引贡献者或降低团队的整体卡车系数是极其有价值的。

由于他们的目标，许多开源项目已经记录他们的 API 很长时间了。我们能做的是找到将这些想法应用到类似环境中的方法。也许如果我们在一家公司使用这种思维方式，我们也能够提高知名度，将[大教堂转变为一个合作和自给自足的集市](http://www.catb.org/~esr/writings/cathedral-bazaar/cathedral-bazaar/)。

营利性组织应该从开源中学习，并且理解对于协作和知识共享的目标来说，文档可能比它看起来更重要。

你也可以使用类似的技术[隐藏显而易见的泄漏抽象](/@fagnerbrack/repairing-the-leaky-abstraction-e726baab91b5)。

感谢阅读。如果您有任何反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。