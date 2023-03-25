# 如何登录 Apache Spark

> 原文：<https://medium.com/hackernoon/how-to-log-in-apache-spark-f4204fad78a>

任何应用程序的一个重要部分是我们整合到其中的底层日志系统。日志不仅用于调试和跟踪，还用于商业智能。在我们的应用程序中构建一个健壮的日志记录系统，可以作为我们正在解决的业务问题的一个很好的见解。

# Apache Spark 中的 Log4j

Spark 使用 ***log4j*** 作为自己日志记录的标准库。Spark 内部发生的一切都会记录到 shell 控制台和配置的底层存储中。Spark 还为应用编写者提供了一个模板，因此我们可以使用相同的 ***log4j*** 库来添加我们想要的任何*消息*到 Spark 中现有的和适当的登录实现中。

# 配置 Log4j

在***SPARK _ HOME/conf***文件夹下，有一个***log4j . properties . template***文件，它是我们自己的*日志*系统的起点。

基于这个文件，我们创建了***log4j . properties***文件，放在同一个目录下。

***log4j . properties***看起来如下:

基本上，我们希望隐藏 Spark 生成的所有日志，这样我们就不必在 shell 中处理它们。我们将它们重定向到文件系统中进行记录。另一方面，我们希望将我们自己的日志记录在 shell 和一个单独的文件中，这样它们就不会与来自 Spark 的日志混淆。从这里开始，我们将把 *Splunk* 指向我们自己的日志所在的文件，在本例中是***/var/log/sparku . log .***

这个(***log4j . properties***)文件是在应用程序启动时由 Spark 提取的，因此除了将它放在上述位置之外，我们不需要做任何事情。

# 写我们自己的日志

现在我们已经配置了 Spark 管理日志所需的组件，我们只需要开始在我们的应用程序中写日志。

为了展示这是如何做到的，让我们编写一个小应用程序来帮助我们进行演示。

我们的应用程序:

运行这个 Spark 应用程序将证明我们的日志系统是有效的。我们将能够看到 ***Hello demo*** 和***I done***消息是如何被记录到 shell 和文件系统中的，而 Spark 日志只会被记录到文件系统中。

到目前为止，一切似乎都很容易，然而有一个问题我们还没有提到。

类***org . Apache . log4j . logger***不是*可序列化的*，这意味着我们不能在对 Spark API 的某些部分进行操作时在*闭包*中使用它。

例如，如果我们在应用程序中:

在 Spark 上运行时，这将失败。Spark 抱怨说*日志*对象不是*可序列化的*，所以它不能通过网络发送给 Spark workers。

这个问题其实很好解决。让我们创建一个类，它在进行大量日志记录的同时对数据集做一些事情。

*映射器*接收一个*RDD【Int】*并返回一个*RDD【String】*，它还记录它正在映射的值。在这种情况下，请注意*日志*对象是如何被标记为 ***@transient*** 的，这使得序列化系统可以忽略*日志*对象。现在， *Mapper* 正在被序列化并发送给每个 worker，但是当 worker 需要日志对象时，它会被解析，这解决了我们的问题。

另一个解决方案是将*日志*对象包装成一个*对象*构造并到处使用。我们宁愿在我们要使用的类中有 *log* ,但是替代方法也是有效的。

此时，我们的整个应用程序如下所示:

# 结论

我们的日志现在显示在 shell 中，也存储在它们自己的文件中。Spark 日志在 shell 中被隐藏起来，并被记录到它们自己的文件中。我们还解决了在尝试登录不同的 workers 时出现的序列化问题。

我们现在可以基于我们自己的 Spark 日志来构建更健壮的 BI 系统，就像我们今天处理其他非分布式系统和应用程序一样。商业智能对我们来说非常重要，拥有正确的见解总是件好事。

如果你觉得这篇文章有用，请推荐它，这样其他人也能从中受益。

*阅读下一条:*

[*如何登录 Apache Spark，功能方法*](/@anicolaspp/how-to-log-in-apache-spark-a-functional-approach-e48ffbbd935b?source=linkShare-4d3a2d851128-1469361893)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！