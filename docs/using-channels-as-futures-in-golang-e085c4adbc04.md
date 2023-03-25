# 在 Golang 中使用渠道作为未来

> 原文：<https://medium.com/hackernoon/using-channels-as-futures-in-golang-e085c4adbc04>

最近，我在 Go 中的 [AWS ECS](https://aws.amazon.com/ecs/) 之上构建了一个轻量级任务调度器。该应用程序通过 AWS ECS(一个构建在 EC2 和 Docker 之上的 orchestrator)将带有自定义参数和调度的批处理作业作为单个容器进行调度。调度器的一个关键需求是监控正在运行的任务的状态。为此，我选择使用 AWS ECS 的 [DescribeTasks](http://docs.aws.amazon.com/AmazonECS/latest/APIReference/API_DescribeTasks.html) 端点，它获取 ECS 任务 ID 并返回它们的状态、完成时的退出代码、停止的原因等。

由于速率限制，我不得不批处理 API 调用。最初，我认为我的代码应该是这样的—

似乎很合理，对吧？调度程序只需创建一个监视器，通过`Watch`提交任务，并轮询`GetStatus`直到目标任务完成。

…算是吧。`GetStatus`的第一个问题是调度程序必须知道监视器是批处理的，因为它返回所有任务的状态图。在 API 中公开实现细节通常是抽象漏洞的标志。

举第二个例子—

太好了！现在，`GetStatus`返回给定任务的状态。虽然只是一些额外的字符，但这是一个巨大的可用性改进，因为调度程序不再需要批处理任务。

然而，这段代码仍然存在一个问题。调度应该什么时候调用`GetStatus`？当然是循环了！

这是可行的，但是它并不优雅，对于更昂贵的操作来说也不够理想。我们可以做得更好。

如果你从 JavaScript、Scala、C#或其他有前途的语言开始，你可能会想“这些怎么翻译成 Go？”。好吧，围棋本身没有“期货”，但它也不需要*它们，因为渠道是如此强大。让我们来看一看！*

*嘣！*这是一种美丽、高效、简单的方式。*监视器。Watch(task)* 返回一个可“等待”的通道。

在围棋中用渠道作为[期货](https://hackernoon.com/tagged/futures)并不完美。首先，不可能在它们周围创建一个像[蓝鸟](http://bluebirdjs.com/docs/getting-started.html)那样的带有帮助函数的库，例如，由于 Go 缺乏泛型。此外，为每个[函数](https://hackernoon.com/tagged/function)调用创建一个通道并不节省空间，因为每个通道都有自己的互斥体、缓冲区等。因此，如果您正在处理性能密集型应用程序中的大量调用，这不是正确的决定。在这些情况下，您应该使用单一通道和阅读器，例如

…但这带来了更多的间接性，所以尽可能(大多数用例)尝试将通道作为一个可读、直接的 API 的未来！

如果你觉得这很有趣，可以考虑在 Twitter 上关注我，在那里我分享了我的围棋经验。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！