# Mob 编程如何让你更有效率🔊

> 原文：<https://medium.com/hackernoon/how-mob-programming-will-make-you-more-effective-590a1b7e0418>

## 但是它必须以正确的方式用于正确的原因

![](img/99b4dea532fa4b782b4b548d7070e027.png)

A picture of Nasa's Mission Control Center.

Listen to the audio version!

一年前，我写过关于[结对编程](/@fagnerbrack/pair-programming-8cfbf2dc4d00)的文章。我描述了[如何找到应用它的最佳语境](/@fagnerbrack/how-to-find-the-best-context-for-pair-programming-711b8fdcd259)、[在实践中的样子](/@fagnerbrack/the-power-of-pair-programming-lies-on-the-execution-a27938447994)，以及[如何不正确地做](/@fagnerbrack/how-to-do-pair-programming-wrong-dab72fd15bef)。

类似于结对[编程](https://hackernoon.com/tagged/programming)，还有另一种[软件开发](https://hackernoon.com/tagged/software-development)的方法叫做 [Mob 编程](https://en.wikipedia.org/wiki/Mob_programming)。

***注:*** *本帖假设所有参与者都在同一个房间使用同一台电脑的情况下进行 Mob 编程的实践。这并不意味着它不能在其他环境下工作，例如当你远程工作时，使用开源软件，Philippe Bourgau 在这个有用的评论中指出。*

每当我谈到乌合之众编程，我的脑海中就会浮现出 Woody Zuil。对于那些不知道的人来说，Woody 是“ [Mob 编程——整个团队方法](https://www.agilealliance.org/wp-content/uploads/2015/12/ExperienceReport.2014.Zuill_.pdf)”的作者我很幸运在 2016 年 YOW 之后见到了他，当时他应[的邀请，从](https://medium.com/u/12a33066ce5d?source=post_page-----590a1b7e0418--------------------------------) [Pragma 团队](https://pragma.team/)来给我们[做这个演讲](https://www.youtube.com/watch?v=sLEsWB1wZMA):

The YOW! 2016 Talk: Woody Zuill — Mob Programming, A Whole Team Approach.

Mob 编程非常优秀，但并不适用于每个团队。它要求每个参与者至少有相同的结对编程心态。如果做得正确，在正确的环境中，出于正确的原因，它可以更快地推动团队前进。然而，如果做得不正确，在错误的背景下或出于错误的原因，它可能会产生严重的负面后果。

根据我的经验，有一些事情可能会使 Mob 编程无法尽可能高效地工作。但是，请注意，这里提到的所有事情都不是决定团队行为的借口。如果人们觉得他们是被迫的，那么群居或结对编程是行不通的。

> Mob 编程是指整个团队——包括测试人员、产品负责人和设计人员——在同一台计算机上同时完成一项任务。

在乌合之众中，如果你注意到一点，你不明白正在做的事情的目的，并保持沉默，随着时间的推移，缺乏理解只会增加。如果越来越多的成员保持这种状态，那么 Mob 编程的效果就越差。

每个人都需要了解正在做什么。如果看起来有人有你没有的假设，问一些问题来重新获得上下文。问一个“愚蠢的问题”是没有成本的，只要尽早问，并且与正在进行的任务相关。

这就是为什么 Mob 编程并不适用于所有团队的原因。为了提高效率，你需要在所有团队成员面前自如地提问。

> 如果看起来你失去了上下文，尽早提问。

就像[结对编程](/@fagnerbrack/pair-programming-8cfbf2dc4d00)一样，有驱动程序和导航程序。司机操作键盘，导航员帮助司机指路。区别在于只有一个司机，其他人都是导航员。

一个团队开始做群体编程是很常见的，因为有人想向整个团队展示一些东西。一个人同时扮演司机和领航员的角色。其他人都在看着向他们学习。然而，这忽略了让每个人都积极参与工作的要点。

> 看着某人做工作不是暴民编程。

如果您暂时离开会话，稍后再回来，您将会丢失在此期间已经完成的内容。团队解释一切的成本将比结对更重要。随着越来越多的人频繁地离开会话，以及随着他们离开后时间的推移，成本会增加。

当然，人们因为很多原因不得不休息，比如喝杯咖啡或者去洗手间。然而，你们可以作为一个团队达成一致，在某人离开和回来后，他会自动成为司机。司机在键盘上敲几分钟后就能明白这个小组想要达到的目标。

作为驱动者重新加入人群意味着破坏的成本为零，流量是恒定的。

> 更愿意作为司机重新加入黑帮。

每个人都要时刻注意，这很重要。此外，正如在[之前的一篇文章](https://medium.freecodecamp.org/the-benefits-of-typing-instead-of-copying-54ed734ad849)中提到的，拥有键盘并完成工作(直接和有目的的体验)比看着某人做某事(语言和视觉符号)能有更好的知识保持率。

出于这些原因，持续共享键盘是有意义的。验证专注的一个好模式是随机选择谁成为驱动者。这样，就不太可能有人会因为被叫去的时候看起来毫无准备而不注意。

当然也有例外。还是那句话，这取决于团队。

例如，如果团队专注于需要主动编码的任务，产品负责人不需要成为驱动者，但是如果团队朝着[正确的方向](https://hackernoon.com/the-journey-for-the-right-question-c3f5b9e90035)前进，他们可以提供输入。同样，工程师也可以在设计师或产品负责人的指导下使用 Photoshop。

Mob 编程的效率最大化，因为团队的每个成员都是多学科的，并且能够做其他任何人的工作。然而，在今天的市场上，很难(如果不是不可能的话)找到一个像那样熟练的团队成员。

> 为了更有效，Mob 编程需要每个人都经常触摸键盘。

暴民编程挑战一切。

并不是对每个人都有效。每个团队成员都需要有一些特定的行为属性，类似结对编程。

正确运行它并尽早发现它是否不起作用是非常重要的。如果做不到这一点，可能会招致巨大的成本，并对那些由于过去执行不力的经历而已经对其有负面看法的人来说，成为一种[确认偏见](https://en.wikipedia.org/wiki/Confirmation_bias)。

Mob 编程是一个非常强大的工具，它可以增加团队的整体吞吐量，并完全删除正在进行的工作。

它让你和你的团队更有效率，只要你尽量避免一些常见的错误。

你准备好了吗？

感谢阅读。如果你有一些反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。