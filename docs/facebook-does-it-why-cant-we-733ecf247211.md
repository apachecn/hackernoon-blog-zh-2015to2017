# 脸书做到了。为什么我们不能？

> 原文：<https://medium.com/hackernoon/facebook-does-it-why-cant-we-733ecf247211>

![](img/dfacd36bb79a457142664e70554101a9.png)

在我的职业生涯中，我与非工程师之间的一些分歧源于一种假设，即因为别人在做，我们也能做。虽然这听起来合乎逻辑，但并不总是现实的。为什么不呢？

# 他们拥有数据

正如我在[上一篇关于 API](https://wtfismyengineertalkingabout.com/2017/04/17/wtf-is-an-api/)的文章中所讨论的，公司拥有他们完全控制的专有数据。他们可能有一个 API，允许你访问这些信息，但是只能访问公司希望你访问的信息。[脸书的](https://hackernoon.com/tagged/facebooks)朋友图对他们公司来说非常有价值，但是他们只通过他们的 API 公开了一部分。

例如，脸书允许你获得一个用户的朋友列表*，如果这些朋友也是你的用户*。您无法访问从未使用过您的应用程序的朋友。例如，这允许 Spotify 向你展示你的朋友正在听的音乐，但 Spotify 不知道你的非 Spotify 用户朋友是谁。这使得您无法实现一些脸书能够自己实现的功能。

# 他们拥有这个平台

如果你的产品在另一家公司的环境中运行，比如谷歌的 Android 操作系统，苹果的 iWatch，或者亚马逊的 Alexa，那么你只能做他们让你做的事情。

我最近建立了一个 Alexa 应用程序(查看[捕梦网！](https://www.amazon.com/Adam-Berlinsky-Schine-Dream-Catcher/dp/B06XX1BTQ3))了解他们的新平台。我发现它把开发者束缚得很紧，不让我们使用 Alexa 的许多强大功能，这些功能是它自己内置的技能。例如，用户必须通过说“Alexa，请[你的应用程序名称]去….”来调用你的应用程序而不是告诉它你想要什么。我可以问“Alexa，今天天气怎么样？”因为天气是亚马逊内部开发的，他们拥有这个平台，但如果我在开发一个类似的应用，用户必须通过说“Alexa，问问 Adam 的最棒的天气应用今天天气如何？”

有些公司很有创意。Snapchat 不拥有 iOS 或 Android，那些平台也不允许应用开发者阻止用户截图。对于一个声称自己出名的应用程序来说，这似乎是一个根本性的问题，因为帖子是暂时的。因为他们的 A 计划(我猜)是不可能的，所以他们使用了平台所公开的机制之一:知道用户何时截屏的能力。因此，他们建立了一种机制，如果收件人截屏，就会提醒发件人，因为他们不能完全阻止截屏。(声明:我年纪有点大了，不能真正理解 Snapchat)。

# 他们有数千名工程师

好吧，也许这在技术上是可能的，但是你的 3 个工程师组成的团队无法竞争。在很多方面，小团队比大公司更灵活，行动更快；这就是为什么我喜欢为初创公司工作。但是对于一些大规模的工程挑战，你确实需要一个大团队来完成大规模的项目。除非你有一个庞大的开发团队，否则你无法构建自己的谷歌地图内部版本。

# 他们有更多的钱

计算资源变得非常便宜，像 AWS 这样的云服务使得租用大量硬件变得非常容易。尽管如此，一些任务——尤其是大数据处理任务——对于您的小公司来说可能过于昂贵。在这些情况下，你的工程师应该能够计算出估计的成本，这样你们就可以一起决定是否继续。

# 他们拥有专利

在我的职业生涯中，我还没有遇到过这种情况，但在某些情况下，您的工程师可能在技术上能够做一些事情，但由于专利，他们在法律上被禁止这样做。软件专利是一个敏感的话题，许多软件专业人士认为它们被授予得太宽松了。甚至许多大公司，包括那些拥有有价值的专利库的公司，都同意这一点，一些公司承诺只利用他们的父母进行防御(例如，对方挑起战争)。由于一些专利可以被非常宽松地解释，很多公司正在侵犯他们甚至不知道的专利。如果你的小应用程序成为某个拥有相关专利的大公司的强大竞争对手，那么研究一下你是否会暴露是个好主意。

*希望这篇文章对你有用！别忘了在 Medium 上关注我这里，我的博客* [*WTF 是我的工程师在说*](https://wtfismyengineertalkingabout.com) *，* [*脸书*](https://www.facebook.com/WTFengr) *，*[*Twitter*](https://twitter.com/wtfengr)*，或者*[*LinkedIn*](https://www.linkedin.com/in/atomos)*。并请通过电子邮件* *发送反馈和题目建议* [*。*](https://mail.google.com/mail/?view=cm&fs=1&tf=1&to=wtfengineer@gmail.com)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)