# 优秀编码的 13 条简单规则(根据我 15 年的经验)

> 原文：<https://medium.com/hackernoon/few-simple-rules-for-good-coding-my-15-years-experience-96cb29d4acd9>

![](img/425873bd29b113ddc0f7e84862cece44.png)

嗨，伙计们，我从事程序员工作超过 15 年，使用过许多不同的语言、范例、框架和其他东西。我想和你分享我写好代码的规则。

1.  优化与可读性。去他妈的优化。
    总是写简单易懂的代码，让开发者能够理解。因为花费在硬可读代码上的时间和资源将比你从优化中得到的要高得多。
    如果需要优化，那么就用 DI 把它做成独立的模块，100%的测试覆盖率，至少一年内不被触及。
2.  **建筑第一。我看到许多人在说“我们需要快速做事，我们没有时间做建筑”。而其中大约 99%的人都是因为这样的思维而出了大问题。写代码而不考虑它的架构是没有用的，就像你梦想你的愿望却没有实现它们的计划一样。
    在编写第一行代码之前，你应该了解它将做什么，如何做，它将使用什么，模块、服务将如何相互协作，它将具有什么结构，它将如何测试和调试，以及它将如何更新。**
3.  **测试覆盖率。测试是好东西，但是它们并不总是负担得起的，并且对项目有意义。
    需要测试的时候:
    ——写模块的时候，至少一个月不碰的微服务。
    ——当你在写开源代码的时候。
    -当你在编写涉及金融渠道的代码时。
    ——当您在代码更新的同时拥有更新测试的资源时。
    不需要测试的时候:
    -当你是一家创业公司的时候。
    -当你有一个小团队并且代码变化很快的时候。当你写的脚本可以简单地通过它们的输出进行手工测试时。
    记住，测试写得不好的代码比没有测试的代码更有害。**
4.  [**保持简单，愚蠢。**](https://en.wikipedia.org/wiki/KISS_principle)不要写复杂代码。越简单，错误就越少，调试它们所需的时间就越少。代码应该只做它需要做的事情，没有大量的抽象和其他 OOP 狗屎(特别是它涉及 java 开发人员)+ 20%的事情可能需要在未来以简单的方式更新它。
5.  **评论。** 注释显示不良代码。好的代码应该不用一行注释就可以理解。但是如何做才能为新开发者节省时间呢？—编写简单的内联文档，描述方法的内容和工作方式。这将为理解节省很多时间，甚至更多——它将为人们提供更多的机会来更好地实现这个方法。这也将是全球代码文档的良好开端。
6.  **强耦合与弱耦合。** 总是尽量使用微服务架构。单片软件可以比微服务软件运行得更快，但只能在一个服务器的环境中运行。微服务让你不仅可以在许多服务器上有效地分发你的软件，有时甚至可以在一台机器上分发(我指的是进程分发)。
7.  代码审查。
    代码评审能有多好就有多坏。只有当开发人员理解 95%的代码，并且能够监控所有的更新而不浪费太多时间时，你才能组织代码评审。在其他情况下，这将只是浪费时间，每个人都会讨厌这一点。
    关于这一部分有很多问题，请更深入地描述一下。
    许多人认为代码评审是一种很好的方式，可以教会新成员，或者从事不同部分代码工作的队友。但是代码评审的主要目标是维护代码质量，而不是教导。让我们想象一下，你的团队正在为控制核反应堆或太空火箭发动机的冷却系统编写代码。你在非常难懂的逻辑中犯了巨大的错误，然后你把这个交给新人进行代码审查。你认为发生事故的风险有多大？—在我的实践中超过 70%。
    好的团队是每个人都有自己的角色并负责具体工作的团队。如果有人想理解另一段代码，他就去找负责这段代码的人，问她。不可能知道所有的事情，除了 30%的人，不可能更好的理解一小部分代码。
8.  **重构不起作用。在我的职业生涯中，我多次听到“不要担心，我们将来会重构它”。在未来，这将导致巨大的技术债务或删除所有代码并从头开始编写。所以，除非你有钱从头开始开发你的软件，否则不要负债。**
9.  **累了或者心情不好的时候不要写代码。当开发人员疲惫时，他们会比精力充沛时多犯 2-5 个错误。所以多工作是非常不好的做法。这就是为什么越来越多的国家考虑 6 小时工作制，其中一些国家已经这样做了。脑力劳动和用二头肌工作是不一样的。**
10.  不要一次写完所有的东西——让开发反复进行。
    写代码前分析预测，你的客户/客户到底需要什么，然后选择你能在短期内高质量开发出来的 MVF(最有价值的特性)。使用这样的迭代来部署高质量的更新，而不是在不合理的愿望和牺牲质量上浪费时间和资源。
11.  **自动化与手动。** 自动化是长期 100%的成功。因此，如果你现在有资源来自动化某件事，那就应该去做。你可能会想“这只需要 5 分钟，为什么我要自动完成？”。但是让我们来计算一下。例如，这是一个 5 人开发团队的日常任务。5 分钟* 5 天* 21 天* 12 个月= 6 300 分钟= 105 小时= 13.125 天~ 5250 美元。如果你有 40 000 名员工，这将花费多少？
12.  **走出去，获得爱好。** 工作分化增加心智能力，给予新的新鲜想法。所以暂停一下，出去透透气，和朋友聊聊天，弹弹吉他，等等。
13.  有空的时候学习新东西。当人们停止学习时，他们就开始堕落。

如果你能在评论中分享你关于写好代码的想法和实践，我将非常感激。

**我的新故事:**

[](/@a.nikishaev/how-to-forge-a-man-out-of-yourself-story-of-my-life-d9121e01c05c) [## 如何把自己锻造成一个男人？我的人生故事。

### 今天我想和你们分享一些帮助我成为一个更好的人的事情。这不是什么快速超级…

medium.com](/@a.nikishaev/how-to-forge-a-man-out-of-yourself-story-of-my-life-d9121e01c05c) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)