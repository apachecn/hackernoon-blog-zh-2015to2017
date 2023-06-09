# 面向开发人员的文档速成班

> 原文：<https://medium.com/hackernoon/a-documentation-crash-course-45006a85c15c>

![](img/39fe860f05e5a11e88da59efd4428ae4.png)

[https://www.flickr.com/photos/writethedocs/29760161775/in/album-72157674031974295/](https://www.flickr.com/photos/writethedocs/29760161775/in/album-72157674031974295/)

本文基于我最近在柏林 LinuxCon 上的一次演讲。

**注意**:我将使用术语“项目”来表示应用程序、服务、产品、项目和您想要记录的其他活动。

## 为什么要写文档？

> 对于一个新项目，你首先会考虑什么？

当你花了很多时间在最新的伟大项目、库、服务或平台上工作时，你通常希望人们使用它。

无论人们是通过演讲、媒体、网络搜索还是口头传播了解你，人们通常会首先看到你项目的哪个方面？

**是的，文件**。

好的文档不仅能帮助开发人员使用你的产品，也是一种营销方式。首先是因为开发人员会判断你有什么，而不是其他选择，但也因为即使是技术文档也不仅仅是开发人员阅读的。做出商业决策(并支付账单)的人也会在某个时候查看它，所以确保他们看到的东西也是高质量的和可理解的是很重要的。

可悲的是，我相信我们都经历过寻找一个新项目或库的兴奋，深入到文档中发现它们不可读、过时、不准确，或者更糟的是，不存在。

我知道你们都很忙，而文档往往是你们宁愿忘记的东西，所以我将提供一个简短的速成课程，让你们的生活更轻松，也让那些试图使用你们作品的人的生活更轻松。

## 万维网

先说 3w，谁，什么，为什么？这听起来像是老生常谈，但是你已经在产品和用户故事计划中解决了它们，没有理由它们不能应用到文档中。

> 你为谁写作？
> 
> 他们想达到什么目的？
> 
> 你为什么要写这个？

这些问题很宽泛，取决于你写的是什么文档。如果你正在写一篇博文，读者可能对你的项目有部分了解。如果你正在编写 API 指南，那么也许读者对你的项目有更多的了解，并试图完成一些特别的事情。

我们经常假设我们做的一切都是‘标准的’、‘正常的’，每个人都能理解的。你会惊讶于一切实际上是多么的不同，你的方式是多么的独特，假设事情从来都不是一个好主意。

例如，你认为每个人**必须**安装的协议、技术或依赖关系是否和你想象的一样多？为什么不提反正又不会疼。

大家有过和你一样的编程和实现经历吗？不太可能，所以再次强调，不要假设你的知识和其他人一样。

首先用简单的术语解释你的项目是做什么的。如果你找不到一种方法来将你所做的事情背后的概念简化成一种“电梯推销”的形式，那就更加努力，完善你的想法，或者找一个能做到的人。我相信任何概念，无论多么复杂，都可以归结为一个任何人都能理解的简短概括。当然，它会错过所有的细节和你作品的深度，但这只是一个总结，可以帮助人们了解你的创作是否对他们有用。

## API 文档是不够的

令人震惊的是，API 文档通常是不够的。当他们描述你的产品是做什么的时候，他们并不总是描述某人试图实现和组装的东西。这取决于编程语言，例如 Java 开发人员经常直接分析 API 指南，而 JavaScript 开发人员更倾向于遵循教程。拥有它们很好，当然比什么都没有要好，但是除非你的 API 很简单，否则你将来会需要更多。

## 这不是手册

有些文化和人更有可能在开始做某件事之前从头到尾阅读说明，但现在人们通常会先看看如何开始，然后进行实验，然后再回来寻找他们正在寻找的特定主题。

此外，许多人会通过搜索找到您文档中的页面，所以不要假设您精心构建的入职流程会按照您希望的方式阅读。当然，除非这是绝对必要的(例如硬件项目)，在这种情况下，找到一种好的方式“鼓励”读者遵循正确的道路。

这意味着文档中的每一页都需要脱离流程和上下文。为人们提供一种方法，无论他们身在何处，都能获取他们需要了解的概念。这可以是一个始终可见的结构化菜单，或者是一个文档中的链接，它不必是任何复杂的东西。

## 交互性

这不是所有项目都能做到的，但是如果你能在你的文档中加入任何程度的交互性，这将有助于读者理解一个概念。这可以是嵌入式示例的形式，如 JSFiddle、交互式控制台、API 浏览器，甚至是视频或动画 gif。

*嵌入式代码示例*

*互动控制台示例*

*API 浏览器示例*

## 语言

有些人比其他人更擅长写作，有些人比其他人更擅长编码。清楚地解释概念是一项技能，但这里有一些对我有用的技巧。请记住，我擅长用英语写作，我确实花了很多时间来提高非母语英语，但我自己的写作技能牢固地植根于英语。我希望这些建议能有所帮助。

## 让读者参与进来

开发人员是一群持怀疑态度的人，有时很无聊，他们喜欢认为我们总是知道得更多。虽然用更加以用户为中心的方法写作可能会误入歧途，但如果做得好，这是有效的。

让我们看一个例子:

> *函数*接受参数 x 并返回值 y

这以清晰、实用的方式描述了一个函数的功能。但是关于:

> 你可以使用*函数*根据 x 返回 y 的值

这是一个很小的变化，但是切换读者而不是功能是一个快速而简单的方法，可以让读者在解释中感觉更投入。我们不是在技术文档中写虚构的东西，但是讲一个故事没有坏处。

还有更多的事情需要改进，比如添加一个用户试图实现的例子。这变得更加复杂，因为选择合适和一致的例子本身就是一个挑战，但是如果你想采取额外的步骤，就试一试。

## 被动与主动

这更像是一种语法风格，而不是要遵循的具体规则，但对我来说，它联系到最后一点，并且是对它的技术解释，所以值得包括在内。

再次举上面的例子，重新措辞:

> *函数*可以用来返回基于 x 的 y 的个数

这和前面说的基本相同，但是是被动语态的一个例子。正如我所说，这没有什么本质上的错误，但是(特别是在英语中)它可能会给人冷漠或孤僻的印象。这在技术交流中并不常见，但请考虑这个更“人性化”的句子:

> 假期批准将在适当的时候通知

你可以看到被动语态的感觉。你可能对“消极攻击性”这个术语很熟悉，它让人感觉作者在从某个人身上推卸责任，可能是故意的。

试着像上面的例子一样用“主动语态”改写那个句子，看看同一个句子感觉如何。大约 6 个月前，我开始用主动语态写作，起初我并不相信，但是一旦你习惯了，它会让你的写作感觉更流畅，更有感染力。

## 简短是最好的

> 如果我有更多的时间，我会写一封更短的信
> **帕斯卡，洛克，富兰克林，梭罗，西塞罗，威尔逊？**

我不会说“保持简单”，因为我们正在编写技术文档，它并不总是简单的。但是复杂并不意味着冗长、杂乱或不必要。我知道这很难，实际上写长的比写短的，简洁的要容易。这又回到了我之前的一个观点，如果你不能简洁地解释一个概念，那么也许你对自己的概念理解不够。

你可以通过减少你需要说的内容和不重复你的话来减少整个文稿的大小。你也可以通过使用更短的短语和单词在更小的层面上减少重复。我有一个列表，但是你很快就会知道它们，英语对于单词和短语的较短的对等词是很好的。作为一个旁注，我注意到非母语的语言学家群体在他们的英语中使用相同的非优化短语，但那是另一个故事。

## 结构

我前面提到过，读者可以在任何地方输入你的文档，这就更糟了。报告和眼球追踪实验表明，许多在线读者(尤其是技术内容的读者)会看标题和副标题，滚动以找到代码块，然后转到导航以找到另一项内容。这让阅读变得发人深省，我不是告诉过你一大堆提高写作的方法吗？现在我告诉你反正没人看？

嗯，在某些方面是的。但在其他方面，我说的是利用这些知识更好地组织你的文档。确保有常见的(和可识别的)标题、副标题、代码块和图片来吸引读者的注意。这让你写得好的文案有更好的机会被看到和阅读。

*这里有一个很好的醒目结构的例子*

## 一致性

我非常相信文档的一致性。你定义的一致性规则将取决于你和你(或你的组织)的风格指南。但是要坚持。对于读者来说，文档中不一致的文字可能会造成混乱和分散注意力。这并不意味着你(和你的团队)必须像没有字符的机器一样写作，在一致性和字符之间取得平衡是可能的。

## 工具

技术作者很像程序员，因为我们喜欢讨论、评估和争论工具。我不会在邮报上报道它😁，我还是留着下次吧。

## 为什么要努力呢？

如果你正在读这篇文章，那么我认为你对文档的价值至少有一半的了解，但是我想引用一句话来结束这篇文章，如果没有人能证明别人说过这句话的话。

> “文档不只是为开发人员准备的”

*有什么问题或意见吗？请在下面告诉我。*

# 喜欢你看到的吗？

*如果你欣赏我所做的，* [*那么我很乐意收到你的来信*](https://www.gregariousmammal.com/connect) *，或者* [*你可以支持我的工作*](https://www.gregariousmammal.com/support) *。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)