# 度量收集中的警告

> 原文：<https://medium.com/hackernoon/caveats-in-metric-collection-b92e31c39f3d>

我想讨论从分布式(微服务)系统收集指标的不同方法。

我见过的最常见的两种方法是结构化日志记录和指标发布。让我们开始吃吧。

# 结构化日志记录

结构化日志记录的思想是产生具有预定义格式(例如 JSON)的日志消息，并在每个消息的 JSON 字段中放置一些语义(结构)。

```
{"name":"ServiceA","hostname":"5a28d77e1096","pid":1,"level":"info","user":"ivan","msg":"Something is happening.","time":"2017-04-21T07:57:44.712Z"}
```

有了结构化的日志记录，就可以构建基于日志的指标。基于日志的指标允许您统计与给定的用户定义的过滤器相匹配的日志条目的数量。例如，所有具有带`error`值的`level`字段的日志。有了像[麋鹿](https://www.quora.com/What-is-the-ELK-stack)或 [Stackdriver](https://cloud.google.com/logging/) 这样的系统，这是非常容易的。事实上，它太容易了，以至于人们滥用它。为什么？这里有几个原因。

将大量数据写入 stdout/stderr 会对应用程序的性能产生影响。

对于少量数据，这可能可以忽略不计，但是随着数据量的增加，性能损失也会增加。还要注意，一些日志收集代理可能会利用节流，这会进一步降低应用程序的速度。仅供参考，haproxy 不支持记录到 stdin/stderr 或日志文件，我很确定这是决定的原因之一。如果您仍然决定这样做，请确保它不在处理应用程序热路径的线程中。

**写大量日志可能会影响日志堆栈的性能。**

让我们考虑麋鹿栈。在我见过的大多数情况下，麋鹿都有一个隐藏的 Q 在里面。Q 代表一个队列，如果流量突然增加，或者解析或存储层遇到混乱，该队列会缓冲消息。

![](img/473ccd9a3a2a9355a1b1b8b7b7178bc6.png)

ELK stack architecture

最后一种情况并不少见。毕竟这是另一个分布式系统。但是对于作为最终用户的你来说，这意味着你对系统状态的看法是滞后的。如果你的服务有问题，他们会产生更多的日志消息来提示问题——情况会变得更糟。

**写大量日志可能会影响底层系统的网络**

传输日志消息比传输单个度量值需要更多的带宽。我见过这样一种情况，对 PaaS 解决方案的日志子系统的性能测试几乎导致底层 IaaS 提供商的整个网段瘫痪。更不用说一些[监控](https://hackernoon.com/tagged/monitoring) [工具](https://hackernoon.com/tagged/tools)会在本地累积所有值的变化，并且只定期发送增量，这将减少消耗的网络带宽和性能损失。这种技术也用于追踪[2]。

**更差的开发体验**

没那么专业，但很重要。拥有一个由 5 个以上服务组成的本地开发环境，每个服务都疯狂地进行日志记录是一种可怕的体验。更重要的是，您可能会从您的试运行和/或生产环境中基于日志的指标中受益，但是将它们引入开发是一项艰巨的任务。为您所关心的事情发出度量标准解决了这两个问题——您可以专注于重要的日志消息，并且仍然可以观察系统的行为。

**一些系统可能会丢弃日志消息**

如果您依赖基于日志的指标来捕获所有这些重要事件，并且它们变得越来越多，您可能会丢失一些甚至没有得到通知(例如 Cloud Foundry，[1])。所以再说一次——只记录重要的和可操作的事件，对其他事情使用度量标准。

# 发射度量

就技术而言，这里的事情更加多样化，但从概念上讲，您正在捕获您的应用程序状态，并在某个时候将其发送给第三方系统。以下是一些衡量指标的要点。

**本地累积变化**

因为最终度量只是数字，所以可以在本地累积它们，并偶尔发出它们。在崩溃的情况下，可能会丢失累积的信息，但这大大减少了消耗的网络带宽。也可以反过来做——应用程序公开当前值，任何感兴趣的人都可以读取它们。(这基本上是推模式与拉模式的对比)

**源代码中的内涵是明确的**

收集指标需要检测您的代码。这需要一点工作，但是它清楚地表明了您的意图，这与日志消息不同。如果有人不小心更改了您基于日志的度量所依赖的日志消息的格式，您会在所有正在运行的服务实例更新之后才注意到这一点。为收集和发送指标准备单独的代码大大减少了有人意外删除它的机会。

# 描摹

追踪是最近的一个热门话题(至少对我来说)，它肯定能让你的系统更容易被观察到。这个想法是跨服务边界跟踪请求。当请求与多个服务交互时，这尤其有用。我不再赘述，只是想提一下。更多细节，可以从[2]开始。

# **没有银弹**

记录和度量发射都有它们的位置。只要确保下一次你选择一个特定的选项时，你明白它的利弊，并且知道所有的选择。

[1][https://docs . cloud foundry . org/dev guide/deploy-apps/streaming-logs . html](https://docs.cloudfoundry.org/devguide/deploy-apps/streaming-logs.html)

[2][https://static . Google user content . com/media/research . Google . com/en//pubs/archive/36356 . pdf](https://static.googleusercontent.com/media/research.google.com/en//pubs/archive/36356.pdf)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)