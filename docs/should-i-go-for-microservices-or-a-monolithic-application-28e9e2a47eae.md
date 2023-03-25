# 我应该选择微服务还是单块应用？

> 原文：<https://medium.com/hackernoon/should-i-go-for-microservices-or-a-monolithic-application-28e9e2a47eae>

![](img/68f39da3bc57724c00825f408ffdf751.png)

Building a product that works is like climbing a mountain. You go step by step, and enjoy the journey.

# **微服务简介**

微服务已经存在了一段时间，是构建应用程序和数字服务的一种众所周知的模式。简单地说，一个*微服务体系结构*将服务的各个组件解耦，基本上每个组件都变成了自己的应用程序。所有的微服务都使用定义好的*API*连接起来，这样就可以让自治的团队处理应用程序的各个部分，而大多数情况下不会破坏彼此的功能。[例如，网飞](/refraction-tech-everything/how-netflix-works-the-hugely-simplified-complex-stuff-that-happens-every-time-you-hit-play-3a40c9be254b)表示，它使用大约 700 个微服务来提供它们的流媒体服务。

关于从现有的单芯片应用程序转换为微服务的选项(例如，使用[自给系统](https://www.elastic.io/breaking-down-monolith-microservices-and-self-contained-systems/)，或 [Martin Fowler](https://www.martinfowler.com/bliki/StranglerApplication.html) 所描述的[扼杀者应用程序模式](https://www.michielrook.nl/2016/11/strangler-pattern-practice/)，以及微服务的[好处](https://www.readitquik.com/articles/in-premises/hy-microservices-is-the-future-of-software/))已经有很多著述，关于如何[构建和设计微服务](https://www.amazon.com/Building-Microservices-Designing-Fine-Grained-Systems/dp/1491950358/)的书籍也有很多著述。

对于支持敏捷实践的体系结构来说，微服务现在听起来可能像是圣杯(在某种程度上确实如此)，但您应该将它应用于正确的情况，用于正确的问题，同时牢记目标结果和环境。因此，本文引入了产品开发和产品生命周期的视角，从理念到[可扩展性](https://hackernoon.com/tagged/scalability)。

# 在通往微服务的旅程中

当您阅读有关转换为微服务体系结构的成功案例时，您将认识到，其中大多数都来自产品公司，这些公司都有一种业务模式，其核心依赖于对其大部分收入负有责任的数字产品/资产。

考虑到这一点，我们将提醒自己，这些公司已经在他们的产品和业务模式上经历了数百次迭代的旅程。

然后他们来到这个架构，因为它解决了他们的一些需求。在大多数情况下，当你证明你正在解决一个问题时，你就开始从整体服务转向微服务。当[产品上市时](http://andrewchen.co/when-has-a-consumer-startup-hit-productmarket-fit/)。正如[马克·安德森](http://web.archive.org/web/20070701074943/http:/blog.pmarca.com/2007/06/the-pmarca-gu-2.html)所说，“产品/市场匹配意味着在一个好的市场中拥有一种能满足该市场需求的产品”。

这允许他们拥有独立的团队，致力于个人服务，扩大团队和产品，而不会在速度上有太大的妥协(想想[贝佐斯的两个披萨法则](http://blog.idonethis.com/two-pizza-team/))。

他们都有共同的目标和愿景，这与产品的成功以及商业模式紧密相关。由于工作和努力与业务的成功紧密相连，当您朝着每个产品迭代交付最大商业价值的方向茁壮成长时，持续投资于产品和交付产品的团队是很自然的。

那么微服务所有的解耦性、自治性、可扩展性都可以在更短的时间内带来更多的价值。这是对长期博弈的投资。

> 微服务不是免费的午餐——本杰明·伍顿

# 微服务和早期国家公司

另一方面，微服务本身会增加复杂性和运营开销。你需要合适的人、稳定的团队和合适的技能。[微服务不是免费的午餐](http://highscalability.com/blog/2014/4/8/microservices-not-a-free-lunch.html)，正如[本杰明·伍顿](https://twitter.com/benjaminwootton)所说。Martin Fowler 说得对:“[你必须这么高才能使用微服务](https://martinfowler.com/bliki/MicroservicePrerequisites.html)”，基本上是在倡导一种方法，随着你建立内部专业知识和团队，你可以慢慢过渡到微服务。

所有这些开销会在初始阶段减慢你的速度，在[寻找可扩展的商业模式](https://steveblank.com/2010/01/25/whats-a-startup-first-principles/)，在这个阶段你试图达到 MVP，并让客户对你的产品感到兴奋。

在这个阶段，速度是关键。率先上市意味着尽早获得客户。更重要的是:你的[构建、测量、学习反馈周期](http://www.startuplessonslearned.com/2010/09/good-enough-never-is-or-is-it.html)越短，你就越能降低构建没人想要的东西的风险。

在早期产品阶段，首先要努力达到产品的市场适应性。[走捷径](https://blog.adioma.com/do-things-that-dont-scale-in-startups-infographic/)。[做不成规模的事情](http://paulgraham.com/ds.html)。手动做事。过早的优化会束缚那些可以提供更直接的客户价值的资源。问问你自己，你是想要一个还没有扩展的可行的商业模式，还是想要扩展一个不可行的商业模式。

> 做不可扩展的事情——保罗·格拉厄姆

# 那我现在该怎么办？

首先，确保你了解微服务的优点和缺点，并考虑你需要进行的投资。为自己制定一个计划并回答以下问题:

1.  你是否在打造一个作为你商业模式核心的产品？如果这是一个你后来交给别人的项目，你交付给别人，确保你同意他们对你的策略。
2.  你有一个稳定的团队吗？这个团队会继续开发这个产品，直到你放弃它？如果没有，增加的复杂性将会减慢你的速度，如果你重组团队，并把他们分配到其他项目或任务中，对团队的投资很可能得不到回报。
3.  你是否计划扩大你的团队和产品，因为市场需要你的产品成长和扩展？如果你计划只是发布它，然后看看会发生什么，而没有一个在发布日之后如何发展你的产品的计划，你为什么要过早地优化。
4.  你有合适的人和合适的技能吗？你将需要那些带来所有高级技能的人来管理、构建、设计和操作复杂的分布式系统。如果你不愿意围绕产品建立一个稳定的团队，你很可能不会投资这个团队。
5.  你有一个有效的商业模式并且你的产品是核心吗？如果你的商业模式还没有被证明，考虑你在一个阶段用可伸缩性换取速度，速度是关键，你想快速学习，快速迭代。一个复杂的架构可能会适应变化，但是引入它的初始投资可能会减慢你的速度。

# 想法，想法，评论？

我试图将业务建设的观点引入到微服务的讨论中。作为开发人员和企业家，我热衷于开发人们喜欢的产品。我认为，用精益敏捷的方法来构建产品、服务和公司，是应对当今商业挑战固有复杂性的一种方式。请在评论中留下你的想法和观点。