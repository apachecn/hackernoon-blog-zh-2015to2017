# PyTorch 和 Tensorflow 有什么不同？

> 原文：<https://medium.com/hackernoon/how-is-pytorch-different-from-tensorflow-2c90f44747d6>

PyTorch 早期发布版本于昨天 1/19 公布。PyTorch 目前由[亚当·帕兹克](https://apaszke.github.io/)、[萨姆·格罗斯](https://github.com/colesbury)和[苏密特·钦塔拉](http://soumith.ch/)维护。想到的第一个问题是**py torch 到底是什么？用制作者的话说，PyTorch 给出了**

> GPU 张量、动态神经网络和深度 Python 集成。

这是一个 Python 优先库，与其他库不同，它不像 C-Extensions 那样工作，具有最小的框架开销，与英特尔 MKL 和 NVIDIA (CuDNN，NCCL)等加速库集成，以最大限度地提高速度。

让我们在这里暂停一下，并试图意识到，直到最近几个月，人们还在假设深度学习图书馆生态系统正在稳定，但这与现实相去甚远。该生态系统中的尖端技术确保了对动态计算图形和 PyTorch 的有效支持，这是所有方面的优势。

![](img/fcdab3e90e6af3177714b06646bc6fe3.png)

> 每当需要完成的工作量变化时，动态计算图就会出现。这可能是当我们处理文本时，一个例子是几个单词，而另一个是文本的段落，或者当我们对可变大小的树结构执行操作时。这个问题在特定的子领域特别突出，比如自然语言处理，我大部分时间都花在那里。

PyTorch 受[链轮](http://chainer.org/)和 [DyNet](https://github.com/clab/dynet) 的影响很大。用 Chainer 的话说，这是“定义并运行”框架和“定义并运行”框架之间的区别。TensorFlow 是一个“定义并运行”的框架，其中可以在图结构中定义条件和迭代，而在 comparison Chainer、DyNet、PyTorch 中都是“定义并运行”的框架。在这种情况下，系统在运行时生成图形结构。这更接近于用任何语言编写代码，因为代码中的 for 循环在图结构中也表现为 for 循环。TensorFlow 不能很好地处理动态图，尽管有一些不那么灵活的，坦率地说，非常有限的原始动态结构。

请在 twitter 上关注我，你也可以注册一个由我维护的小而不常见的 T2 邮件列表。如果你想了解深度学习，就去看看这篇[中帖](/@debarko/supervised-deep-learning-in-image-classification-for-noobs-part-1-9f831b6d430d#.rvo9n9os5)。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！