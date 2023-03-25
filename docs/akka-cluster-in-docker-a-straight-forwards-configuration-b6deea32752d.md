# Docker 中的 Akka 集群。直截了当的结构。

> 原文：<https://medium.com/hackernoon/akka-cluster-in-docker-a-straight-forwards-configuration-b6deea32752d>

> 免责声明:我们不打算过 Akka 或 Docker 的细节，这只是我们缺少的指南配置 Akka 的 Docker。为了更好地理解，您应该回顾一下这两个平台的基础

在我的上一篇文章 [*中，我们讨论了如何使用 Scala 构建工具来创建 docker 文件和 Docker 图像。这在我们的组织中是必须的，因为我们可以使用我们习惯的工具 sbt 在 CI 环境中集成和自动化 Docker 流程。然而，正如你可能会怀疑的那样，这并不是故事的结尾。在我们今天要讨论的过程中，我们还遇到了其他 Docker 挑战。*](https://hackernoon.com/yet-another-sbt-docker-introduction-2d9fb99fe367#.u0rt2nduu)

我的团队有足够的信心使用 Akka 作为分布式工作的平台，并使用 Docker 作为在生产(或任何其他环境)中运行我们的 Actor 系统的平台，但这并不容易，至少在开始时是这样。

让我们以几周前开始的相同方式开始，一个简单的应用程序使用分布式发布/订阅 Akka 功能将消息发布到 Akka 集群。

让我们来看看发布者代码:

在这里，我们的应用程序加载配置(我们接下来将查看它)，用它创建一个 actor 系统，并启动***dumbtick publisher***actor，它每十秒钟向集群发布一条消息。

另一方面，我们需要从集群中读取消息(订阅)并对这些消息进行处理。

我们已经创建了一个新的应用程序，以便它订阅集群中的一些事件。订阅集群事件的参与者如下所示:

它的工作是监听一个主题( ***inputTopic*** )，通过该主题得到的所有内容都将使用函数 ***f*** 进行转换，并使用主题 ***outputTopic*** 发布回集群。

现在，我们需要启动 ***变压器*** 。

这段代码看起来与 publisher 上的代码非常相似，但是这里我们创建了 ***Transformer*** 的实例，而不是***dummtickpublisher。***

现在我们有两个应用程序，一个向集群发送消息，一个读取消息，所以我们的下一步是能够运行它们。

# 配置

这里是我们花了大部分时间的地方，主要是因为我们在网上的其他地方没有找到足够的信息。

让我们快速回顾一下配置。

你可以在任何在线例子中找到类似的东西，有很多这样的例子，但是仍然缺少在 Docker 上运行的部分。

使用当前的配置，我们实际上可以在两个不同的 JVM 上本地启动这两个应用程序，它们将开始相互通信。信息会从一个发送到另一个，一点问题都没有。

这就是你所需要的，将建立两个 Docker 图像，并创建一个可能看起来像这样的合成文件。

我们运行 ***docker-compose up*** ，两个容器将启动。我们将看到和以前一样，他们的工作。*没有真的*。

问题是，现在我们希望能够说:

***docker-compose scale C1 = 5***

但这是不可能的。尽管您会看到容器正在启动，但 Akka 日志说的完全不同。节点不再可见，参与者系统进入不可逆转的失败状态。

*问题是 Akka 需要一个特殊的配置才能在 NAT 之后或 Docker 容器中运行。*

您需要将 ***绑定主机名*** 和 ***绑定端口*** 添加到配置文件中。

经过多次尝试，我们找到了一种适合我们的配置。

为了让容器之间能够互相交谈，我们需要设置*到容器的 ip 和一个特定的 ***端口*** 。发送到此地址的消息将被翻译成 ***bind-**** 。*

*最后一部分是更新我们的合成文件，如下所示。*

*记住我们需要 Docker 容器 ip ( *CLUSTER_IP* )，所以我们传递" " with 暗示它将使用***inet address . getlocalhost . gethostaddress***来代替，如文档所示。此外，我们在 *CLUSTER_PORT* 中使用了端口 0 (cero ),因此 Akka 使用随机端口。*

*完成这些更改后，我们应该能够重建 Docker 映像(确保配置更改进入 Docker 映像)并运行:*

****docker-compose up****

****docker-compose scale C1 = 100****

*你会看到你的演员们是如何开始互相交流的，你会意识到你已经准备好了。*

*我们花了很多时间才找到这些小的配置细节。我们认为通过记录它们可以做得更好，特别是在像 Akka 这样广泛使用的平台上。*

*我们希望你能从我们在这件事上的工作和时间中受益。特别感谢御光团队为演员系统所做的工作。*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*