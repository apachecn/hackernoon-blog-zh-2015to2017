# 准备好你的分布式系统，这样你就不会在它失败时损失金钱

> 原文：<https://medium.com/hackernoon/prepare-your-distributed-system-so-you-dont-lose-money-when-it-fails-32c3195cd732>

当你将一个分布式[系统](https://hackernoon.com/tagged/system)投入生产时，你需要学习的第一件事就是:它最终会失败！你不相信我？我不怪你。

我们通常会忘记我们对网络的信任，这是造成严重系统故障的头号原因，因为我们不知道或者说我们不记得[分布式计算的八大谬误](https://en.wikipedia.org/wiki/Fallacies_of_distributed_computing):

*   [网络](https://hackernoon.com/tagged/network)可靠
*   延迟为零
*   带宽是无限的
*   网络是安全的
*   拓扑不变
*   有一个管理员
*   运输成本为零
*   网络是同质的

假设你在经营一家电子商务公司，今天是一年中最重要的周五。没错，就是这样，黑色星期五。你的网站上有 2 万名用户。您的系统允许用户添加到购物车的项目，它将验证股票稍后在结帐过程。你有 300 个用户在结账的过程中，你知道一个缓慢的经验在结账过程中可以有一个巨大的影响转化率。您的清单服务每分钟有 30.000 个请求，这减慢了您的结帐过程。您的平均订单价值为 400 美元，因此，如果您失去了在结账过程中的用户，您可能会损失高达 120.000 美元。

如果库存服务停止运行，您如何防止用户流失？**超时**和**断路器**在这种情况下是有用的技术。请记住，这不仅仅是一个技术决定，您应该询问业务部门在这种情况下会发生什么。我们应该阻止用户在未验证项目可用性的情况下订购项目，还是应该允许他们在流程的稍后阶段检查可用性？

跟我重复一遍: **it 将失败**。墨菲会确保这件事发生在最不恰当的时刻。最好的办法是在失败时做好准备。

*原载于*[*thrownewexception.com*](http://thrownewexception.com/2016/02/16/prepare-your-distributed-system.html)于 2016 年 2 月 16 日发布。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！