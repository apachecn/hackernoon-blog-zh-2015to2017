# 用镜头驯服流数据

> 原文：<https://medium.com/hackernoon/taming-streaming-data-with-lenses-by-landoop-aa7055117c08>

![](img/51e131711afa9b229b2d998dd0e13693.png)

在我担任 Splunk 产品经理期间，我有机会会见了几位财富 500 强客户，他们希望创建一个中央数据传输主干网。他们的目标是能够交换或添加新的数据目的地，如仓库、应用程序消费者或 Splunk 等解决方案。这样的主干将允许 IT 或其他利益相关者混合和匹配不同的技术，而无需对管道中最可怕的部分(摄取层)进行任何更改。大多数情况下，这个支柱是 Apache Kafka，它的使用得到了几家知名科技公司的支持，如 LinkedIn(该项目的创始人)或网飞。

与任何开源软件(OSS)项目一样，Apache Kafka 的开箱即用版本，甚至其商业发行版，并不检查所有的框。OSS 需要人力投资才能变得有用，而用户生产力并不是它最大的优势。我们可以有把握地说，大多数现代企业已经选择在 OSS 上标准化他们的堆栈。一些成功的公司，如 Cloudera、Elastic、RedHat、Mesosphere、DataStax 或 Docker 承担了使 OSS 更易于使用和管理的任务。目前，这是世界上几乎所有软件基础设施提供商的核心价值主张。OSS 允许开发者在没有 IT 和采购部门许可的情况下进行创新；因此，它成为采用新技术的驱动力。

[Antonis](https://uk.linkedin.com/in/antwnis) 和 [Christina](https://uk.linkedin.com/in/chdask) 于 2016 年创立了 [Landoop](http://www.landoop.com/) ，踏上了帮助企业采用和利用流媒体数据的征程。为流媒体平台使用、部署、管理和构建应用仍然是一项单调乏味的劳动密集型任务。他们接触的企业越多，就越能意识到他们必须制造大部分缺失的部件。这一转变的核心技术是流行的 OSS 项目 [Apache Kafka](https://kafka.apache.org/) 。

他们确实建造了。 [Stream Reactor](https://github.com/Landoop/stream-reactor) 标准化 Kafka 上的 ETL，Kafka 是面向开发人员的全包式[快速数据开发](https://hub.docker.com/r/landoop/fast-data-dev/)容器， [Kafka Topics UI](https://hub.docker.com/r/landoop/kafka-topics-ui/) 允许开发人员动态查看他们的数据， [Schema Registry UI](https://hub.docker.com/r/landoop/schema-registry-ui/) 为管理员带来理智；如今，Apache Kafka 社区中许多最流行的工具都是由 Landoop 制造的。

该团队继续与更大的客户接触，如 SAS 或 British Gas。他们很快发现，采用这项技术的主要障碍是缺乏在这些新概念方面训练有素的操作员和开发人员。对于资金雄厚的科技初创公司来说，这种情况可能不太严重，但对于世界各地的大公司来说，这是一个严重的瓶颈。亚洲的电信公司、伦敦的银行、中西部的 agtech 传感器网络或意大利的公用事业公司都无力雇佣和培训构建流媒体数据平台及其应用所需的所有人员。阿帕奇卡夫卡必须人性化，其管理必须自动化。

兰道普为阿帕奇卡夫卡输入[镜头。当我第一次听说这家公司的时候，我的眼睛转动了一下。尽管如此，几天后我看到的演示还是让我措手不及，安东尼斯、克里斯蒂娜、](http://www.landoop.com/kafka-lenses/)[安德鲁](https://www.linkedin.com/in/datamountaineer/)和[斯蒂芬](https://uk.linkedin.com/in/stepi)，他们加入公司稍晚，带我看了早期版本的镜头。通过使用镜头，任何开发人员甚至商业分析师都可以使用 Kafka，而不必学习所有的原语、特质和活动部分。Landoop 将 SQL 语义带到了流媒体世界，允许用户重新利用其现有技能来获取数据，然后查询、分析、转换并最终将它们发送到选择的目的地。Lenses 会为您做所有繁重的工作。它可以通过 [Kubernetes](https://kubernetes.io/) 或 [Kafka Connect](https://www.confluent.io/product/connectors/) 扩展查询的执行，允许操作员微调权限并保持[合规性](http://www.landoop.com/blog/2017/12/apache-kafka-gdpr-compliance/)，或者连接到超过 25 个不同的源。Landoop 创建了一个独特的成熟产品，帮助企业轻松采用和利用最先进的流媒体技术。

[镜头](http://www.landoop.com/kafka-lenses/)就是这种把几个人物角色带入同一个房间的产品。由于 API 集成，开发人员可以调试他们的流，并在镜头上构建功能。Ops 可以监控和横向扩展集群。分析师和企业主可以利用信息流获得真正的实时情报。我们认为，透镜是企业标准化的关键产品之一。

Apache Kafka 的[镜头的可扩展性、可管理性和成本效益不仅及时，而且至关重要，因为目前各公司正在利用流媒体技术快速构建新的基础设施和应用。我们对投资于这一彻底转变的领导者感到兴奋。不过，最重要的是，我们相信这个团队有能力打造一款产品和一家能够定义自己空间的公司。](http://www.landoop.com/kafka-lenses/)

如果你是一名对 DevOps、CI、容器、编排、Scala 和 Akka 有浓厚兴趣的工程师，请确保查看[空缺职位](https://landoop.workable.com/)。

*原载于* [*马拉松创投*](https://marathon.vc/taming-streaming-data-with-lenses-by-landoop/) *。*