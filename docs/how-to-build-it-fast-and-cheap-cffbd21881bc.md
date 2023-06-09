# 如何又快又便宜地建造它

> 原文：<https://medium.com/hackernoon/how-to-build-it-fast-and-cheap-cffbd21881bc>

![](img/6c2df580423b1ef00b5422928f135c09.png)

几年前，我是团队中唯一的程序员。在后端、移动(android)、前端(javascript)和 DevOps 上工作极具挑战性。我被迫寻找新的做事方法。这里有一些帮助我的技巧。

# 存在检查

在你构建一些东西之前，检查一下开源的替代方案。你可能找不到完全匹配的，但有些相似。稍后，根据您的需要进行调整。

一年前，我做了一个体育新闻 app。当应用程序增长时，我需要一种方法来管理用户、添加投票等。需要一个管理面板。

我遇到了 [Flask-AppBuilder](https://github.com/dpgaspar/Flask-AppBuilder) 。我写了数据库模型。它自动生成视图。他们已经有了一个登录系统，我可以通过命令创建管理员用户。这是救命恩人。后来，这个应用变得更加复杂。我有更多的东西，比如标签、固定装置等等。我可以在几分钟内生成视图。

![](img/847e8f66234a8309af9bd80a7e81afc6.png)

这看起来不是很好，但是完成了任务。因为这只对员工开放，所以没关系。一些公司从零开始建立这样的东西。浪费时间和金钱。

# 权衡取舍

很少有框架能让你快速构建东西，但不能保证规模。[流星](https://www.meteor.com/)在它的初始阶段，没有承诺规模。很多人都有问题。然而，它有助于快速出货。我们可以在几天内完成通常需要几周的事情。一旦你完成了响应式 web 应用的开发，只需一个命令就可以打包一个 ios 或 android 应用。

![](img/025f8722e5a27df83ea57ae4db9ce75f.png)

我们快速制作了原型。东西以前会坏掉。然而，我们很快就让它运行起来了。

# 质疑传统手段

任务是获取新闻文章，直到最后一次同步。用 API 做这件事很痛苦。你必须遍历多个分页的结果，确保它不会占用太多的内存，交付速度非常快，这样用户就不必等待等等。

![](img/020118c79c63991e4abcf4f94ea6f61c.png)

这是我听说 Google Cloud Messaging (GCM)话题订阅的时候。这是一个简单的酒馆。我让客户端通过编程订阅主题“运动”。然后，我的解析器被设置为发布“体育”主题的所有新闻。

这是给客户端的推送通知。在客户端，我解析了通知内容并将其存储在数据库中。我不需要写任何 API(基于网络的)。最棒的是谷歌云消息是免费的。我可以不花一分钱就把它扩大到数百万。

# 先构建后优化

在创业的早期阶段，当你还在想事情的时候。你经历了快速原型制作。你越是设计它，当你不得不扔掉它时，它就越是令人心痛。除非你得到认可，否则我会说，这足以让它工作。当用户坚持你的产品，优化它。我制作的大约 90%的原型都变成了垃圾。

不要从第一天就过度设计，而是逐步优化。我犯了担心规模和安全性的错误。这不值得。我同意你的产品有可能会像病毒一样传播，也有可能会崩溃。但是如果你的产品有价值，人们就会回来。

# 采用不断发展的框架

有些人真的不愿意学习新东西。即使这能让他们的工作轻松很多。最初我确实很难设置 docker。后来，我的联合创始人不用麻烦我部署 CSS 更新。他可以用 git 按钮触发部署。

![](img/7af0efff1a45beadbe4d2f95472cb74f.png)

我看到一些员工制作他们自己的框架，而不是采用一个已经建立了良好社区的框架。问题是维护和升级成本很高。一个有良好社区的框架提供了免费的维护和升级。更糟糕的情况是这个人离开公司。如果产品很复杂，开发人员将很难推进产品。

# 东西少

在你建立之前进行协商(这在大多数创业公司是不可能的，除非你是首席技术官)。你的企业创始人可能从第一天起就需要很多东西。一个产品通过不断的迭代变得更好。告诉他，如果这个可行，我们会加上那个。而不是建造“这个和那个”,然后把它们都刮下来。

如果你对在云上制作应用感兴趣，可以在亚马逊上看看我的书 [*云是小菜一碟*](http://amzn.to/2n03pzO) *。也可以加入我的邮件列表* [*云计算故事*](http://eepurl.com/cHet9j) *。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)