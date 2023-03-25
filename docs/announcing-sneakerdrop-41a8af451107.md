# 宣布 Sneakerdrop！

> 原文：<https://medium.com/hackernoon/announcing-sneakerdrop-41a8af451107>

几个月前，随着美国政治局势变得白热化，我和一位同事开始探索加密通信工具。有很多很棒的东西，但我们发现一个不足的地方是在一个分散的群体中匿名消息传递。想想那些对匿名有着基本需求的活动家、抗议者和研究人员。

今天，我正在开源 [Sneakerdrop](https://hackernoon.com/tagged/sneakerdrop) ，一个结合了 dead drop 和 sneakernet 的软件，用于在一个已建立的群体中进行安全、匿名、异步的交流。

# 它是做什么的？

Sneakerdrop 将加密消息写入分类帐。消息可以广播，也可以一对一。广播消息以不加密的明文形式写入，但由发送方签名。一对一的邮件是加密的，因此只有收件人可以阅读。

然后账本带外同步——想想 u 盘，或者分组无线电，甚至电子邮件。使用您的同步分类帐，您可以阅读广播消息并解密发送给您的消息，但您不能阅读任何不是供您观看的内容。不可解密的消息会保留在你的账本中并被同步，所以一条消息最终会到达网络中的所有节点。

# 我如何使用它？

该源代码目前可以在位于 https://github.com/adrianpike/sneakerdrop 的[的 GitHub 上获得。那边有如何开始的说明。](https://github.com/adrianpike/sneakerdrop)

# 下一步是什么？

很高兴你问了！我将与一些团体、朋友和同事讨论关于采用和限制的问题。我有一个粗略的精神路线图，但我真的希望它是一个社区的努力，所以如果你有任何想法或想法，我洗耳恭听。我的电子邮件是 adrian@adrianpike.com，我会关注 GitHub 的问题。❤️

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！