# 活动流入门

> 原文：<https://medium.com/hackernoon/getting-started-with-activity-stream-d7d5a528394c>

今天，每个应用程序都间接使用活动流的概念，但是人们不知道有一个由社区专门为此目的定义的协议。

**那么，什么是活动流呢？**

活动流是一种协议(规范),用于以适当的结构化方式记录业务级事件。例如:脸书的新闻提要，Github 的活动提要等。

在任何标准系统中，通常都有很多我们想要坚持的活动。我们可以以协同编辑平台、社交平台、订单管理系统等为例。

最好在活动流之上使用状态机来对系统施加规则。这两件事一起帮助你建立一个健壮的系统。

我们先来讨论一下规格:

在活动流中，你将事件分解成一个类似英语的句子，句子中有`**Actor Verb Object Target**`

例如这个事件:`CustomerA placed an order for hotelA.`所以我们可以把它分解成`**Actor** **verb** **object** **target**`的格式

**Actor** :客户 id

**动词**:放置/状态

**对象**:订单标识

**目标**:酒店标识

通常，我们用活动的名称创建**集合/表**，其中我们有以下字段/列:actor、verb、object、target、created_at/published_at。

然后，我们将业务事件/活动分解成动词，如:**放置、交付、发送、分派**等。

现在把我们的**对象**想象成**订单/朋友请求/演示请求**等等。

最后是**目标**组，它可以保存一个**单引用或组引用**如:**朋友 id、酒店 id、文档 id、组 id 等**。

因此，通过这种方式，我们可以在集合中记录每个业务级别的事件。

使用活动流的首要原则是你永远不要覆盖任何事件/活动。

你避免在活动集合的基础上做决定。您只是将它作为只读文件使用。并将该信息用于后面的分析。

您可以绑定这些事件来通知目标客户或向他们显示活动日志等。

因此，使用上述方法，你可以很容易地建立一个新闻提要，活动管理器等。

有许多开放源码包可供使用，您可以轻松地将其与您的系统集成。

对于 [java](https://hackernoon.com/tagged/java) 和 [python](https://hackernoon.com/tagged/python) 你可以参考这些链接:

【https://github.com/OpenSocial/activitystreams 

[http://django-activity-stream . readthedocs . io/en/latest/streams . html](http://django-activity-stream.readthedocs.io/en/latest/streams.html)

# 结论:

我希望我把一切都解释得足够清楚，让你明白。如果你有任何问题，请随意提问。你可以在推特 *上找到我 [*。*](https://twitter.com/rohitkhatana3)*

请务必点击下面的“绿心”并关注我，了解更多关于技术的故事:)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！