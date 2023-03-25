# Spark 包，从 Xml 到 Json

> 原文：<https://medium.com/hackernoon/spark-packages-from-xml-to-json-ab6b5bb1f988>

# Spark 包，从 Xml 到 Json

Apache Spark 社区在扩展 Spark 方面付出了大量努力，因此我们都可以受益于它为我们带来的计算能力。

最近，我们对将 XML 数据集转换成更容易查询的数据很感兴趣。我们的主要兴趣依赖于在我们每天获得的数十亿次交易的基础上进行数据探索的能力。

XML 是一种众所周知的格式，但有时使用起来会很复杂。例如，在 Apache Hive 中，我们可以定义 XML 的模式结构，然后使用对我们很重要的 SQL 查询它们。

然而，我们很难跟上 XML 结构的变化，所以前面的选项已经被放弃了。

我们正在使用 Spark 流功能将这些事务引入我们的集群，我们正在考虑在 Spark 中进行必要的转换。然而，同样的问题仍然存在，每次 XML 结构改变时，我们都要改变 Spark 应用程序。

一定有别的办法！

社区中有一个 Apache Spark 包，我们可以用它来解决这些问题。

让我们加载 Spark Shell 并查看一个示例:

在这里，我们只是将 xml 包添加到我们的 spark 环境中。这当然可以在编写 Spark 应用程序并将其打包成 Jar 文件时添加。

使用这个包，我们可以将任何 XML 文件读入数据帧。当加载 DataFrame 时，我们可以指定数据的模式，但这是我们最关心的问题，所以我们让 Spark 来推断。数据帧模式的推断是一个非常强大的技巧，因为我们不再需要知道模式，所以它可以随时改变。

让我们看看如何将 XML 文件加载到数据框中:

打印 DataFrame 模式可以让我们了解推理系统做了什么。

此时，我们可以使用 Spark SQL 使用任何 SQL 工具来查询我们的 XML。请阅读此贴( [***Apache Spark 作为分布式 SQL 引擎***](/@anicolaspp/apache-spark-as-a-distributed-sql-engine-4373e254e0f9#.w77z4ml3r) )了解更多 Spark SQL。

更进一步，我们可能会使用读取 JSON 格式的工具。如果你有类似 Apache Drill，from [*MapR*](https://www.mapr.com/) *的东西，拥有 JSON 数据集特别有用。*

正如我们所料，有了 Spark，我们可以进行任何类型的转换，但是没有必要编写一个花哨的 JSON 编码器，因为 Spark 已经支持这些特性。

让我们将 DataFrame 转换成 JSON 并保存到我们的文件系统中。

当将 *toJSON* 函数应用于 DataFrame 时，我们得到一个***【RDD】【字符串】*** ，用 JSON 表示我们的数据。然后我们将 RDD 的*保存为一个纯文本文件。*

*现在，我们可以使用 Drill 来读取和查询我们的新数据集，当然，如果我们需要做一些更复杂的操作/转换，我们可以随时回到 Spark。*

## *结论*

*在 Spark 中，将我们的数据集从 XML 转换成 JSON 是一项简单的任务，但是 JSON 相对于 XML 的优势是巨大的。现在，我们可以放心，XML 模式更改根本不会影响我们，我们已经消除了为每次 XML 更改而更改应用程序的负担，我们还可以使用强大的工具以无模式方式查询我们的 JSON 数据集，如 Apache Drill，同时我们的客户可以使用 SQL 报告我们的数据。*

**感谢社区正在开发的令人惊叹的工具。**

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*