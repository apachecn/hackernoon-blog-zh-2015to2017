# 函数式编程带来的意想不到的快乐

> 原文：<https://medium.com/hackernoon/unexpected-joy-from-functional-programming-ed9d3adca77a>

我认为我们中许多花大量时间在前端解决方案上的人已经将功能性的 T2 编程技术融入到了我们的日常生活中。我开始这样做主要是基于信念——一种“有烟必有火”之类的东西。

起初，我很感激能够减少一些样板代码。例如，我很少再创建计数器变量了。而不是一个“ **for(让 I = 0；我<100；i++)** “我用一个“ **array.forEach( () = > )** ”。实际上，整个“for (let i=…)”结构通常用于从集合中过滤出对象，或者对集合中的对象进行某种转换。因此，我几乎不使用 array.forEach，我更多地使用 **filter** 、 **map** 和 **reduce** 。

我喜欢减少样板文件，这确实是它自己的奖励。然而，我意外地发现，我比以前更信任自己的代码。事实上，当我回头看我一年前写的代码时，我觉得它几乎是残缺无力的。我有点夸张，但你明白我的意思了。

不变性为这种信任提供了基础。如果你创造的所有东西都是一次性的，并且从不改变，那么意外副作用的风险就会大大降低。在 JS 中，我们不能消除那种事情，但是它降低了风险。很安慰。

Richard Eng(并不是一个 JavaScript 迷)写了这篇关于他有多爱 SmallTalk 的文章( [Smalltalk 毁了我的生活——快乐上瘾的代价](/@richardeng/smalltalk-ruined-my-life-aaf2190f6f16#.ho0m2rmz2))。读完之后，我意识到这种从 FP 中获得的新乐趣对我的影响有多大。例如，今天早上我看着 reddit 帖子背后的代码，心想——“他真的应该对大多数变量使用**常量**”。我在看别人有趣的多人游戏，其中有一段代码从集合中提取了一个玩家对象，然后想，“他真的应该使用 reduce，将 15 行代码和 2 个循环减少到一行，并去掉那些无用的循环变量”。

如果你正在寻找一些养成函数式编程习惯的理由，考虑“信任”这是一种真正的安慰，并为这位开发人员的编程生活增添了几分欢乐:)。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！