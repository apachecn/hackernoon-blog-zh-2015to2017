# consul2dogstats，带维度标记

> 原文：<https://medium.com/hackernoon/consul2dogstats-with-dimensional-tagging-b55ade84e654>

如果你同时使用 Consul 和 Datadog，你会想要这个。

我写了一个小的[程序](https://hackernoon.com/tagged/program)，它维护一个名为 [consul2dogstats](https://github.com/zendesk/consul2dogstats) 的数据中心咨询服务计数列表。它定期轮询 Consul 服务目录和健康端点以收集清单，然后将结果发布给 [Datadog](https://hackernoon.com/tagged/datadog) 。这是 Apache 2.0 许可的。

# 关于维度标记的一个旁白

Datadog 最强大的功能之一是*维度标签*。简而言之，维度标签是键值对，您可以将其附加到时间序列指标上，以便进行查询和聚合。

假设您想要跟踪组织中 web 服务器的请求率。让我们称度量的名称为`http_requests`。我们可以应用的一些有用的标签是:

*   服务器主机(如`app1`、`ip-1-2-3-4`)
*   网络服务(如`frontend`、`api`、`gatekeeper`)
*   环境(如`production`、`test`)
*   响应代码范围(如`2xx`、`3xx`、`4xx`、`5xx`)

在 Datadog 中，你可以给它们分配一个*维度键*。在上面的例子中，这些可能是`hostname`、`http_host`、`env`和`http_response_type`。

一旦你开始标记指标，聚集和过滤它们就是小菜一碟。您可以执行强大的查询来回答*实时*中的问题，例如:

*   在整个生产过程中，我每分钟收到多少`5xx`回复？
*   在我的生产环境中，我每分钟为`gatekeeper` web 服务发送多少`2xx`响应？

与除了服务器主机之外没有其他维度的旧解决方案相比，这在功能和灵活性方面是一个巨大的飞跃(服务器主机本身在云环境中是一个越来越没用的维度，因为主机现在通常是短暂的，它们的规范名称毫无意义)。

考虑传统的点分隔形式的指标名称，如`requests.production.frontend.2xx`或`requests.test.gatekeeper.4xx`。这些给分析师带来了一些重大挑战。

首先，选择要查询的指标是一个 *O(n)* 操作，因为指标名称是不透明的(系统不知道您选择的命名结构的语义)。如果您正在收集大量的指标(您应该这样做！)，那可是一个*非常*昂贵的操作。

第二，添加新的维度是一场管理噩梦，因为如果您想要添加维度，您必须创建一个新的度量标准。假设我们想在上面的指标中增加一个像`shard`这样的新维度。在支持开箱即用的维度标记的系统中，这没有问题:任何以前的图形都将像以前一样继续呈现。但是如果你使用平面指标，你将开始创建新的指标，比如`requests.production.frontend.shard42.2xx`和`requests.tests.gatekeeper.shard1.4xx`。一旦您用新的指标替换旧的指标，以前的指标数据的历史将会结束，这将产生一个不连续性，使执行时态分析更具挑战性。(当然，您可以继续以旧的名称提交指标，同时提交新的、更细粒度的指标。但是您将会重复计算，这可能会导致分析错误，并且向系统提交比需要的更多的指标，这可能会导致容量问题。)

好消息是，即使你更喜欢自己管理指标，而不是使用 SaaS 提供商如 Datadog，现在也有支持维度标记的开源指标系统。没有必要再忍受扁平的度量标准了。

# 维度反模式

通常，运营指标系统旨在显示您的系统和应用程序的实时状态和运行状况。为了保持良好的性能，重要的是不要使公制尺寸*过于精细。**一个好的经验法则是确保特定键的可能值少于您拥有的服务器数量。**因此，按环境、主机名或功能标记指标是好的。但是用客户或用户 ID 来标记它们是一个非常糟糕的主意；它将在后端创建如此多的表，性能将受到严重影响。*

直截了当地说:**不要用你的运营指标体系作为你的分析体系。**有很多优秀的分析数据库和提供商，包括 [Vertica](https://www.vertica.com/) 、 [Redshift](https://aws.amazon.com/redshift/) 、 [Clickhouse](https://clickhouse.yandex/) 和 [BigQuery](https://cloud.google.com/bigquery/) 。

# 充满希望的风景

Datadog 是第一个平台，无论是商业平台还是其他平台，它提供了一个支持尺寸度量标签的引人注目的产品。但是现在有更多的选择。查看它们，找出最适合您的一款:

**商业**

*   [数据狗](https://datadoghq.com)
*   [信号 Fx](https://signalfx.com)
*   [波前](https://www.wavefront.com/)

**开源**

*   [普罗米修斯](https://prometheus.io/)
*   [OpenTSDB](http://opentsdb.net/)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿美族家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！