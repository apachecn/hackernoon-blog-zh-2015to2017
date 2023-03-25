# ION:智能实时分布式系统的数据格式

> 原文：<https://medium.com/hackernoon/ion-the-data-format-designed-for-intelligent-real-time-distributed-systems-d41d68b5a7a4>

![](img/06873350d82cf180cbb9ff7ef407ec33.png)

在我们之前的帖子[ [1](https://hackernoon.com/our-ambitious-quest-to-democratise-distributed-systems-act-2-ion-vs-json-ce69f4e5c4a9) ] [ [2](https://hackernoon.com/our-ambitious-quest-to-democratize-distributed-systems-act-1-protocols-b6bec5297213) 中，我们简要介绍了我们的数据格式 ION，它是 Nanosai.com 项目[的一个组成部分。在本帖中，我们将分享更多关于 ion 的轻量级技术信息，以及我们认为它如何帮助开发人员构建更好的分布式系统生态系统。](http://www.nanosai.com/)

但是首先我们需要澄清一些否则可能会引起混乱的事情。在 Amazon 公开发布他们的 ION 格式之前，我们就发布了我们的 ION 数据格式。因此，我们没有选择 ION 这个名字，因为它与 Amazon 的 ION 相似。事实上，直到我们设计并命名了我们的 ION 格式之后，我们才发现了相似之处。首字母缩写词 ION 源自“IAP 对象表示法”，其中我们的开放网络协议 IAP 代表“互联网应用协议”。一个直接了当的证据如下:

1.我们的联合创始人在 Infoq.com[发表了一篇关于 IAP 的文章，其中明确提到了 ION。](https://www.infoq.com/articles/IAP-Fast-HTTP-Alternative)

2.我们的黑客新闻公告[这里](https://news.ycombinator.com/item?id=11027319)关于 ION 与黑客新闻公告帖子[这里](https://news.ycombinator.com/item?id=11546098)关于亚马逊的 ION。你会注意到我们的黑客新闻公告比亚马逊的公告要早得多。

**什么是离子(我们的离子！)?**

简而言之，ION 是一种通用的二进制数据格式，可用于对各种数据进行编码。它的表达能力足以包含序列化对象(如 Java 或 C#对象)、CSV、JSON、XML、文本和二进制数据。

解析和生成它非常快，也相当容易，在网络上比 JSON 和 XML 压缩得更多，对于服务器、路由器和其他轻量级硬件(我们相信)来说也很容易处理。

如下图所示，ION 是我们开放分布式系统堆栈的核心部分之一。我们将 ION 设计为 IAP 的默认数据格式，因此，所有 IAP 消息都使用 ION 进行编码。

IAP 是一种通用的面向消息的网络协议，设计用于同步和异步通信，使 IAP 适用于许多不同的用例，如 RPC、文件交换、流、消息队列订阅。我们创建 IAP 是因为现有的协议如 HTTP 不能满足我们的通用性和高性能需求。

![](img/3491f7aaba702f9ee9b9e261e09cc8b6.png)

**智能实时分布式系统的定义**

在 Nanosai.com[我们相信以下两个定义中的条件是一个系统被归类为“智能实时分布式系统”所需要满足的最少且充分的条件。](http://www.nanosai.com)

**定义 1:** 让我们做一个分布式系统。我们说 S 是智能分布式系统，如果 S 至少满足下列条件；

(I)系统中节点之间的即插即用式交互。

(ii)多功能，这意味着它支持许多不同的用例及架构。

(iii)健壮，这意味着它可以在出现故障的情况下存活。

(iv)当故障条件固定时的自愈能力。

**定义 2:** 分布式系统 S 是实时分布式系统，如果它至少满足以下条件；

(I)通过快速网络协议进行通信。这在术语上意味着一个紧凑的二进制协议。

(ii)异步通信，如许多实时系统所要求的。

(iii)每秒至少可以处理 1，000 条消息(尽管我们真正的目标是每秒 10，000 到 100，000 多条消息)。

**为什么我们需要一种新的数据格式？**

首先，当在分布式系统中的节点之间交换数据时，使用快速、紧凑和通用的数据格式对数据进行编码是有利的。我们觉得现有的格式(如 Protobuf、CBOR、MessagePack、JSON)不够通用，也不够快，无法满足我们对未来分布式系统的需求。

其次，对于通信节点来说，快速数据格式读写(反序列化和序列化)更快。如果开发人员需要最高速度，甚至可以以二进制形式遍历 ION。

第三，紧凑的数据格式需要较少的字节来表示编码的数据。字节越少，需要的网络带宽就越少，因此可以更快地通过网络传输。在读写数据时，紧凑的数据格式也是一个优势。

最后，通用数据格式是一种可以用于尽可能多的用例的数据格式。这最大限度地减少了发明新数据格式的需要。当然，总有其他人想出比 ION 更好的数据格式的可能性。但本着开放创新的精神，我们对此表示欢迎！

**IAP 外的离子**

作为一种数据格式，ION 可以独立于 IAP 使用。开发人员可以使用 ION 作为数据文件、日志文件中的数据格式，作为通过 HTTP 等传输的二进制消息的数据格式。

ION 可以包含二进制数据，因此开发人员也可以在必要时在 ION 中嵌入其他格式。例如，MP3 文件、ZIP 文件、JPG 文件等。

**ION 与其他数据格式的比较**

ION 是一种数据格式，类似于 JSON 的二进制版本。在这方面，ION 类似于 MessagePack、CBOR 和亚马逊的 ION，但有一个有利的区别。

在文本[中，我们对 ion 与其他数据格式](http://tutorials.jenkov.com/ion/ion-vs-other-formats.html)和[的比较有更详细的描述。](http://tutorials.jenkov.com/ion/ion-performance-benchmarks.html)

**电流释放**

新的 [0.5.0 版本](https://github.com/nanosai/grid-ops-java/releases/tag/0.5.0)包含了对 IonObjectReader 和 IonObjectWriter 以及相关类的改进。这些类现在能够读写对象图，其中一个 POJO 类包含它自己类型的字段。例如，一个包含 Node child1 和 Node child2 实例变量的 Node 类。

在此版本之前，试图用引用自身的类创建一个 IonObjectReader 或 IonObjectWriter 会导致堆栈溢出异常。现在已经修复了。

在未来几周，我们将发布新的 ION 更新和更新文档、基准等。

最后，请在这里下载我们的首席技术官[创作的免费书籍。我们的开源 Java 工具包 Grid Ops 可在](https://leanpub.com/the-grid-vision) [Github](https://github.com/nanosai/grid-ops-java) 上获得。如果您希望在我们推出托管基础设施服务时收到测试邀请，请在此处[订阅。](https://goo.gl/forms/zXQe9k2es9iombdD2)

*由共同创始人*[*bambo rdébaldé*](https://medium.com/u/665359d135fa?source=post_page-----d41d68b5a7a4--------------------------------)*发布。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)