# Spark 数据源 API。扩展我们的 Spark SQL 查询引擎

> 原文：<https://medium.com/hackernoon/extending-our-spark-sql-query-engine-5f4a088de986>

在上一篇文章[Apache Spark as a Distributed SQL Engine](/@anicolaspp/apache-spark-as-a-distributed-sql-engine-4373e254e0f9#.x4kyh8jqr)中，我们解释了如何使用 SQL 查询存储在 Hadoop 中的数据。我们的引擎能够从分布式文件系统中读取 **CSV** 文件，从文件中自动发现模式，并通过 *Hive* 元存储将它们公开为表格。所有这些都是为了能够将标准的 SQL 客户端连接到我们的引擎，并在不手动定义文件模式的情况下探索我们的数据集，从而避免 ETL 工作。

Spark 提供了一个可以扩展的框架，我们将通过扩展它的一些功能来进一步提升它的能力。

# Spark 数据源 API

*数据源 API* 允许我们管理任何格式的结构化数据。Spark 已经内置了一些标准结构，比如 Avro 和 Parquet，但是第三方已经通过扩展这个 API 为 **CSV、JSON** 和其他格式创建了新的*阅读器*。今天我们要创造我们自己的。

我们有两个理由来扩展 API。

首先，我们想要一个能够读取我们的*遗留*格式的库，并将我们当前的数据源转换成一个更易于使用的新数据源。

其次，我们希望在使用我们数据的所有应用程序之间共享这个库，避免为了实现相同的目标而需要共享的应用程序的复杂打包。

# 数据源

我们的数据源由文件集合组成，其中每个文件本身就是一个实体。为了这个例子，我们定义了一个简单的格式，其中每个文件都是一个包含用户信息的文本文件，每个字段一行。让我们看一个文件的例子。

```
pepe
20
Miami
Cuba
```

这个文件代表一个名叫“pepe”的用户，今年 20 岁，住在迈阿密，出生在古巴。

在现实世界中，格式可以像我们想要的那样复杂，但我们要解释的过程不会改变。

每个文件都有相同的格式，我们有数百万个这样的文件。我们还想公开它们，以便在 SQL 中进行查询。

# 我们的实施

为了扩展*数据源 API* ，我们需要从 Spark 框架中实现某些类，这样我们的自定义阅读器就可以被加载和使用。

让我们首先创建一个 Spark 应用程序作为我们示例的入口点。我们可以通过跟踪帖子 [SBT、Scala 和 Spark](/@anicolaspp/sbt-scala-and-spark-6a57c0a2623a#.kqiew1f4r) 来实现。

一旦创建了应用程序，我们需要做的第一件事就是链接正确的 Spark 库。我们将在 Spark *1.5.1* 上运行示例，我们的 **sbt** 文件定义如下。

# 创建我们的模式

*数据源 API* 的起始扩展点是**关系提供者**类。 **RelationProvider** 类将用于创建我们数据的必要关系。

我们还需要混合 **SchemaRelationProvider** 特征、，这允许我们创建我们想要的模式。

我们需要创建一个名为 **DefaultSource** 的类，Spark 将在给定的包中寻找它。 **DefaultSource** 类将*扩展* **RelationProvider** 并混合 **SchemaRelationProvider**

到目前为止，我们的代码如下所示:

在代码中，我们基本上是在创建一个 **LegacyRelation** 对象，它定义了我们想要创建的*关系*。把关系想象成一个具有已知模式的元组集合。

让我们看看我们的*关系*类是如何实现的。

这里我们用*覆盖了*的*模式*函数，因此它返回了我们想要的模式。在这个例子中，我们知道数据的模式，但是在这里，我们可以做任何我们想做的事情来获得所需的模式。如果数据是 **CSV** ，我们可以使用文件头来推断模式，或者执行我们需要的任何其他操作。

注意，我们只想要*名称*和*年龄*字段，而不是实体的全部内容。

下一步是测试我们是否得到了正确的模式，我们可以通过将下面的代码添加到我们的*应用*中来完成。

这段代码从中创建了一个 **SparkContext** 和一个 **SQLContext** 。使用 **SQLContext** 我们通过传递*包名*来设置格式(记住 Spark 会在这个包中寻找 **DefaultSource** 类)。然后我们*使用我们的提供者将指定路径中的数据加载到**数据帧**中。*

将打印我们定义的模式，输出应该如下所示。

此时，我们只创建了我们想要的模式，但是没有说明如何准备数据以及如何将数据组织到我们定义的模式中。

# 将数据读入我们的模式

为了读取我们的数据源，我们的**legal relation**类需要混合 **TableScan** 特征。 **TableScan** 有一个我们需要用以下签名实现的方法:

方法 *buildScan* 应该从我们的数据源返回所有行。在我们的特例中，每一行都是每个文件的选定内容。让我们看看我们的*构建扫描*的实现。

这里我们使用了 *wholeTextFiles* 方法来读取整个文件(每个文件都是一个实体)，读取前两行(我们需要的唯一字段)并从每一行创建一行。结果是一个行集合，其中每一行都是仅使用我们关心的文件部分创建的。

这将足以修改我们的*应用程序*，使其打印出我们数据源的内容。*应用程序*现在看起来如下。

即使我们将所需的格式读入数据框，也没有关于数据字段类型的信息。我们的模式定义支持不同的数据类型，但我们并不强制它们。

让我们修改我们的 *buildScan* 方法，以便它在创建每一行时推断类型信息。

这里，唯一的变化是我们将从文件中读取的每个值转换为正确的类型，这是从 *schema.fields* 对象中推断出来的。在我们的特例中，我们只对*名字*是一个*字符串*和*年龄*是一个*整数*感兴趣，但是同样，在这一点上我们可以非常有创意。

现在，我们最后的 **LegacyRelation** 类将如下所示。

现在我们可以将我们的数据加载到一个 **DataFrame** 中，并注册它供 SQL 客户端使用，正如我们在上一篇文章中解释的那样。我们的 *app* 和我们展示的一样简单。

我们已经展示了足够多的将自定义格式读入数据帧的内容，因此我们可以利用 *DataFrame API* 的优势，但还有更多的事情可以做。

*数据源 API* 不仅提供读取数据的功能，还提供以自定义格式写入数据的功能。如果我们想将一个数据集从一种格式转换成另一种格式，这个功能非常强大。让我们看看如何将这些功能添加到我们现有的驱动程序中。

# 编写格式化程序

假设我们想要保存我们的数据，以便可以从其他标准系统中读取。我们将加载我们的自定义数据源，并从它创建一个 **CSV** 样的输出。

为了支持来自 API 的*保存调用*，我们的 **DefaultSource** 类必须与**CreatableRelationProvider**特征混合。这个 trait 有一个方法叫做 *createRelation* 我们需要实现，我们来看看。

我们基本上是将数据框保存为一个类似于 **CSV** 的文件，然后返回一个带有已知模式的关系。

*saveAsCsvFile* 方法用我们格式化为 ***CSV*** ，的数据创建一个 **RDD【字符串】**，然后*it*保存到给定的路径。为了简单起见，我们没有在输出文件中包含头文件，但是请记住，我们可以按照我们需要的格式输出数据。**

**我们的 **DefaultSource** 类的完整代码如下。**

**为了将我们的原始数据保存为类似 **CSV** 的格式，我们对我们的 *app* 做了如下修改。**

**注意，每次我们读/写我们的数据时，我们需要指定我们的 **DefaultSource** 类所在的包名。**

**我们现在可以打包我们的库，并将其包含在任何需要使用我们描述的数据源的项目中。许多其他库正在被创建，以支持我们可以想象的所有可能的格式，现在您可以创建自己的库来为社区做出贡献，或者只是在您自己的项目中使用。**

# **结局**

**我们已经看到了如何使用 Spark *数据源 API* 将数据从定制格式加载到数据帧中。我们还回顾了过程中涉及的类，特别是 Spark 如何使用我们包中的 **DefaultSource** 来执行所需的操作。我们还实现了一个输出格式化程序，这样我们的数据帧可以被保存，就像我们希望的那样。**

**使用*数据源 API* 我们可以做更多的事情，但是根据我的经验，找到正确的文档是相当困难的。我相信可以创建一个更好的文档，特别是那些在扩展 API 时非常有用的部分。**

**尽管我们的示例展示了如何扩展*数据源 API* 以支持简单的格式，但是可以修改它来读写更复杂的类型，比如二进制编码的实体。**

**将我们自己的数据类型集成到 Spark 中的能力使它成为数据处理的顶级框架之一。**

**在 Hadoop 世界中，我们可以找到许多共享目标和功能的工具，但没有一个像 Spark 一样灵活和通用。这使得 Spark 在这个领域非常受欢迎。如果我们对能够在无限环境下工作的处理框架感兴趣，那么 Apache Spark 是一个不错的选择。**

**如果你觉得这篇文章有用，请推荐它，这样其他人也能从中受益。**

***阅读下一条:***

**[*Apache Spark 作为分布式 SQL 引擎*](/@anicolaspp/apache-spark-as-a-distributed-sql-engine-4373e254e0f9#.ttfkjcemk)**

**[*MapR 如何提高我们的生产力，简化我们的设计。*](/@anicolaspp/how-mapr-improves-our-productivity-and-simplify-our-design-2d777ab53120#.w22b0x8md)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**