# Node.js 流的作用

> 原文：<https://medium.com/hackernoon/node-js-streams-in-action-1495c22fafec>

本文展示了如何将 [Node.js](https://hackernoon.com/tagged/nodejs) [Stream](https://nodejs.org/api/stream.html) 和一点反应式[编程](https://hackernoon.com/tagged/programming)应用到一个真实的(tm)问题中。这篇文章旨在高度实用，面向中级读者。我有意省略了一些基本的解释。如果你错过了什么，试着检查一下 [API 文档](https://nodejs.org/api/stream.html)或者它的复述(例如:[这个](https://github.com/substack/stream-handbook))

所以，让我们从问题描述开始。我们需要实现一个简单的 web scraper，它从一些 REST API 获取所有数据，以某种方式处理数据并插入到我们的数据库中。为了简单起见，我省略了关于实际数据库和 REST API 的细节(在现实生活中，它是一些旅行费用聚合网站和 Pg 数据库的 API)

假设我们有两个函数(IO 模拟器函数的代码，另一个商品代码是[，这里是](https://gist.github.com/kharandziuk/e823707bf71fba9a4cdf944216773f58)):

对于本文，我们将忽略可能出现的错误。也许我会在下一篇文章中描述它。

对数据处理也有一些要求。假设我们需要删除 id 中包含数字 3 的所有项目。我们需要用当前时间戳扩展 id 包含数字 9 的所有条目(例如:`{id: 9} -> {id: 9, timestamp: 1490571732068}`)。这些需求很傻，但与真实的 web 抓取器中出现的真实需求相似

是时候动手了！一些简单的实现可能是这样的:

这样的代码有什么问题？

1.  乍一看，很难理解这些代码是干什么的。我们可以用一些注释来改进它，但是最好在代码中表明我们在一个地方读取，在另一个地方编写。
2.  太具体了。很难编写一些逻辑来处理这些值。我们如何将处理逻辑从 I/O 逻辑中分离出来？
3.  它不是有效的。我们的生产者一直等到消费者使用一部分数据，而不是缓冲一部分数据并一次写入所有数据。

因此，您可能已经猜到有一种更好的方法来解决节点流的这个问题。让我们把问题分成三部分:输入、输出和处理。

因此，我们的可读流可以是这样的:

看起来有点重，但这是一个标准接口。作为一个数据块，我们将使用一个包含一千个条目的列表。这里重要的事情是`objectMode: true`——我们想要操作对象而不是二进制数据。

现在是输出部分。我们需要实现可写的流。大概是这样的:

这里有一些重要的信息:

1.  `objectMode` —我们想要操作对象
2.  `highWaterMark` —我们的缓冲区的大小…以对象为单位。你应该小心使用它，因为数量和字节的实际大小之间没有直接的联系。在我们的例子中，一个对象是一个有一定大小的列表
3.  `_writev` —“解释”它应该如何一次从一个缓冲区写入多个块。

现在我们可以把它们联系起来:

[https://gist.github.com/9e3174a05adb55a5fbb750b5e972bc0e](https://gist.github.com/9e3174a05adb55a5fbb750b5e972bc0e)

在我看来，代码不需要任何注释。我们显示数据流，隐藏实现的细节，直到你问。而且，它绝对有效:

1.  它不会阻塞事件循环
2.  它广泛使用标准库原语。它们应该是有效的
3.  它使用缓冲和背压

对于甜点，我们将实现数据处理。我们可以手动实现一个转换流，但是这并不有趣。我们将使用一个名为 [Highland.js](http://highlandjs.org/) 的库，它使我们能够使用众所周知的函数式编程原语(。过滤器，。地图等。)随着我们的溪流。其实 Highland.js 比 just 大很多。地图和。过滤器但我不想静音的文章范围。因此，转换看起来像这样:

与简单的 JS 列表非常相似。我们需要`.flatten()`和`.batchWithTimeOrCount(100, 1000)`，因为我们的流操作的是项目列表，而不是单个项目。

所以，就这些了。希望这篇文章能给你一些学习 Node Stream 和 Highland.js 的动力

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！