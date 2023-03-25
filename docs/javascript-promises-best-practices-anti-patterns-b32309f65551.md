# Javascript 承诺最佳实践和反模式

> 原文：<https://medium.com/hackernoon/javascript-promises-best-practices-anti-patterns-b32309f65551>

以下可能看起来像是自以为是的[文章](https://hackernoon.com/tagged/article)，也请给出你的意见。事实上，我发表这篇文章是为了验证我的想法。如果你同意，请推荐。
//发表完这篇文章后，我发现了另一篇由[诺兰·劳森](/@nolan_lawson)写的很棒的博文。
//此处查看[。
//您可以检查并返回到这里查看全局错误记录。](https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html)

# 改进一段糟糕的代码

所以让我们从坏的[代码](https://hackernoon.com/tagged/code)开始。这是一个没有逃脱回调地狱的人的例子。

这里所有的底层函数都调用 return Promises，所以不需要创建*新的 Promise* 而是直接使用 return:

现在，因为我们可以将一个函数的输出传递给另一个函数，所以我们可以像这样链接它们:

因为我们的函数很简单，我们甚至可以把它简化成这样:

简单易懂不是吗？

# 多重变量

现在让我们举一个需要两个变量的例子。比方说，你有一种香肠，你需要一种特别的面包。在这个例子中，我们需要两个承诺的响应，其中一个依赖于另一个。

你可以将内部函数提取到另一个函数中，但是你将在那里创建闭包，所以这是一回事。

还有一个使用 Promise.all 的选项，但我觉得这会使代码难以维护。但这是个人的选择。

# 错误处理

现在我们知道了如何链接承诺，让我们看看一些错误处理。你能区分这两者吗？

上面两个例子之间有一个非常重要的区别，那些最近才从 jQuery 迁移到 React/Redux Stack 并开始使用 Axios、Fetch 或其他类似库的人会觉得 React 开始吞噬他们的错误，而不是向前传播它们。

但这不是吃你的错误。

比方说，你在上面的`doSomething`函数中有一个运行时错误，在`requestA`流程中，你的错误会被捕获，然后`fallbackForRequestFail`会被调用，而实际上`fallbackForRuntimeError`应该被记录，这样你会得到通知，而不是神秘地消失。

所以本质上在`requestB`代码流中，只有函数会在两者之间被调用。任何其他错误(如运行时错误)都将提前传播，您可以单独处理这些错误，也可以将这些错误记录到您的错误记录服务中，并根据具体情况进行修复。

# 记录未处理的拒绝承诺

## 在浏览器中

## In Node.js

你可以在这里阅读更多关于处理拒绝承诺的信息[。](http://www.2ality.com/2016/04/unhandled-rejections.html)

如果你觉得我犯了一个错误，请评论。如果你觉得应该有更多的人知道这件事，请推荐。另外，点击查看[诺兰·劳森](/@nolan_lawson)的深度文章[。](https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！