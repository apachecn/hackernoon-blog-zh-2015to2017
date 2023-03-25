# 不必要的承诺包装

> 原文：<https://medium.com/hackernoon/unnecessary-promise-wrapping-4ab64b2e985f>

假设你正在开发一个有函数`isPi`的数学库。

现在，您可能会说—我们不想关心将数据放在哪里(数据库、网络等)。所以，这个功能应该得到一个承诺:

现在我们可以像这样使用函数:

很酷，是吗？**号**

你没有“包装任意的用户[代码](https://hackernoon.com/tagged/code)在承诺中，不必关心用户的代码是同步的还是异步的”，但是一个承诺是在某个时刻会用一些数据解决或者用一些错误拒绝的东西

所以，我可以删除一些不必要的代码

你可以问:*好吧，初始代码是多余的，但为什么是坏的？*添加一些错误处理的时候问题就出现了。您可以使用下面的代码轻松完成:

这是错误的，因为`isPi`实现对承诺的来源有太多的了解。重写这段代码的正确方法如下:

所以，这里有两条经验法则:

1.  尽可能“靠近”错误的来源来处理错误
2.  将异步操作与同步操作分开。将一切都视为异步操作会导致耦合代码。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！