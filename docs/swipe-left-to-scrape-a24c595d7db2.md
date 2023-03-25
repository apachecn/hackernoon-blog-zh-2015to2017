# 向左滑动以刮擦

> 原文：<https://medium.com/hackernoon/swipe-left-to-scrape-a24c595d7db2>

从 Tinder 中提取个人信息

[推文](https://twitter.com/share)

![](img/5782fb9b2100a9b145619b8d1091d5e7.png)

2017 年 4 月 28 日，安全论坛报道了[新闻](https://techcrunch.com/2017/04/28/someone-scraped-40000-tinder-selfies-to-make-a-facial-dataset-for-ai-experiments/)一名人工智能研究人员发布了一个数据集，其中有 4 万张照片是从约会应用 Tinder 上收集的。目的只是提取一个真实世界的数据集，用于训练卷积神经网络( [CNN](https://en.wikipedia.org/wiki/Convolutional_neural_network) )，以区分男女之间的差异。这看起来足够天真，尽管作者选择将[变量命名为](https://www.theregister.co.uk/2017/05/01/people_of_tinder_data_disappears_amid_uproar/)引起了一点轰动。故事爆出后不久，他很快就把“锄头”这个别名改成了“主题”。显然，这个最初的命名是从 [Tinder Auto-Liker](https://github.com/jaungiers/Tinder-py_auto_liker/blob/master/tinder-py_auto_liker.py) 代码继承而来的。

这已经不是第一次发生了。Tinder 滥用 API 的历史由来已久:

*   据称是私有的 Tinder API 已经被逆向工程和完整的文档[在这里](https://gist.github.com/rtt/10403467)。这种知识使得开源 API 客户端易于使用。例如[这个](https://github.com/charliewolf/pynder)和[这个](https://libraries.io/github/hhuang25/tinder-scrape-api)都使用 Python，任何人都可以很容易地下载它们，并根据他们认为合适的任何目的扩展它们。
*   回到 2015 年 2 月，一名来自温哥华的软件开发人员将他的 Tinder 体验自动化。“像许多流行的应用程序一样，这个约会应用程序的内部[私有 API](https://www.programmableweb.com/news/long-live-private-api/analysis/2015/02/06) 被第三方逆向工程和使用。Tinder 的 API 的未授权用户通常使用它来创建与该服务和其他用户互动的 Tinderbot，但贾斯汀·朗的 Tinderbot 看起来是最雄心勃勃的 Tinder bot 创作之一。”这个机器人甚至可以开始最初的信息对话，并尝试判断情绪是否良好。
*   已经有大量的火绒箱被编写和开源。一些很好的例子，比如“[用 Python 构建 Tinder Bot](https://johnpatrickroach.com/2016/09/08/building-a-tinder-bot-in-python/)”和“[用 Eigenfaces 自动化 Tinder](http://crockpotveggies.com/2015/02/09/automating-tinder-with-eigenfaces.html)”。
*   Swipebuster 是一项付费服务，可以让你发现你认识的人(也许是你爱的人)是否在使用 Tinder(也许你认为他们不应该使用)。

Tinder [隐私政策](https://www.gotinder.com/privacy)(奇怪的是它说最后一次更新是在未来的一周内)声明了以下“与其他用户共享的信息”。当你注册成为 Tinder 的用户时，你的个人资料将会被其他用户看到。其他用户(以及在 Tinder 上可用的任何共享功能的情况下，Tinder 用户可能选择与之共享您的个人或应用程序)将能够查看您向我们提供的信息……”。很公平，如果你注册了 Tinder，你就把你的信息公开了。但我相信大多数 Tinder 用户会从明显的意义上理解这一点，即使用 Tinder 应用程序的其他真实用户将能够看到信息，并根据他们选择的滑动动作做出反应。他们不会想到，对于任何人来说，编写一个简单地复制他们的信息并按照他们认为合适的方式处理的软件会如此容易。我相信大部分用户都没有想过那种可能性。他们不需要这么做。对于 Tinder 的用户来说，期待对他们的信息有一个基本的保护责任，让如此大规模的数据提取变得至少有点困难，这肯定是合理的吗？毕竟这是非常私人的内容。

访问 Tinder API 只需要一个访问令牌。这太令人震惊了。如这里所解释的，你只需要注册成为 Tinder 用户。这是一个相当低的准入门槛，实际上是匿名的。python 代码提供了一个“Tinder Android Version 3.2.0”的用户代理字符串。不是当然，是在 PC 上运行的脚本。用户代理字符串绝对不能保证调用者的身份。甚至不需要 API 键。正如我们在 CriticalBlue 在之前讨论过的[，这不一定是保护 API 的一个很大的障碍，但至少这是一个开始，并迫使 Tinder 应用程序被逆向工程以提取密钥。在我们的](https://www.approov.io/blog/simple-app-authentication.html)[移动 API 安全技术系列](https://hackernoon.com/mobile-api-security-techniques-682a5da4fe10)中，我们将广泛介绍许多更先进的技术。除此之外，我们的[approv](https://www.approov.io/)产品提供完整的软件认证，专门针对这种类型的[自动化](https://www.approov.io/anti-automation.html)移动 API 抓取进行保护。

API 实现中可能存在速率限制。不滥用很难分辨。然而，如果有，那么它是相当无效的。面部刮刀代码似乎只是在下载每个主题的照片之后，在有效地向左滑动之前，添加了一些小的随机延迟(这可能使交互更像人类)。关于向左滑动的要点是没有每日限制，我怀疑一些真正的用户以惊人的速度向左滑动。设定一个向左滑动的限制，而不降低一些用户需要向他们的潜在对手展示的鄙视率，肯定很难。发布的代码充分展示了这种自动化可以走多远。它显然可以从同一个 IP 地址使用同一个用户 ID 提取 40，000 张图像。从代码来看，似乎平均每几秒钟就能提取一张新图像，所以这不到一天就能完成。这肯定会击败平台上最不喜欢权力的人。最终，限速并不能解决问题。它所能做的只是减慢和复杂化脚本。你总是可以创造足够多的[假用户](https://en.wikipedia.org/wiki/Sockpuppet_(Internet))，他们分布在足够多的 IP 地址上，在任何限速系统的雷达下飞行。所需要的是一种协同的尝试，将对 API 的访问锁定为仅应用程序或其他经批准的软件客户端。当然，我们可以尝试让这些自动化，但是这很难实现，也很容易被发现。

鉴于 Tinder API 滥用的悠久历史，至少应该采取一些对策。也许大多数用户并不关心这些事情，但这种大规模的个人资料搜集和重新发布变成一个更大更丑陋的故事似乎只是时间问题。这可能真的会损害品牌，让潜在客户在注册和让他们的个人数据被窃取之前三思而行。

【https://www.approov.io/blog/swipe-left-to-scrape.html】最初发表于[](https://www.approov.io/blog/swipe-left-to-scrape.html)**。**

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*