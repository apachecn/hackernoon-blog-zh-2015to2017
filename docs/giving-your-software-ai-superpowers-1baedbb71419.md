# 赋予你的软件人工智能超能力

> 原文：<https://medium.com/hackernoon/giving-your-software-ai-superpowers-1baedbb71419>

“添加人工智能”最简单的方法是从你的代码中调用一个 API，不管这个 API 是由本地的[软件](https://hackernoon.com/tagged/software)库还是通过网络公开的。

例如，这个 HTTP 请求通过 Web 向情感分析器发送一个句子:

`GET /api/sentiment?phrase=Airplanes+with+the+shape+of+hawks+are+cool`

你的代码会得到如下的答案，这意味着的情绪是积极的。更准确地说，你得到的答案是“这是一个英语句子，它有一个积极的(消极情绪有一个负分)，其“情绪强度”为 0.6，0 为弱，1 为强”。所以，如果你是一名飞机设计师，下次你拿出一张白纸时，我们可以建议你一个鹰的形状吗？

```
{
     "score": 0.6000000238418579,
     "magnitude": 0.6000000238418579,
     "sentences": [
         {
             "text": {
                 "content": "Airplanes with the shape of hawks are cool.",
                 "beginOffset": -1
             },
             "sentiment": {
                 "magnitude": 0.6000000238418579,
                 "score": 0.6000000238418579
             }
         }
     ],
     "language": "en"
 }
```

如果您的应用程序需要在没有互联网连接的情况下工作，您可以嵌入软件库以在本地调用。例如，您可以使用[斯坦福 CoreNLP 工具包](http://stanfordnlp.github.io/CoreNLP/)为您的软件添加语言处理功能，例如“词性标注”，它试图识别名词、动词、形容词、副词等在文本中的位置。

就像常规编程一样，如果你找不到一个预建的 Web 服务或库来做你想做的事情，你将不得不创建你自己的特殊函数。这些天来，最流行的方法是通过使用诸如 [scikit-learn](http://scikit-learn.org/stable/#) 或 [Spark 的 MLlib](http://spark.apache.org/mllib/) (用于广泛的机器学习技术集合)或 [Tensorflow](https://www.tensorflow.org/) 、 [Keras](https://keras.io/) 、 [Caffe2](https://caffe2.ai/) 或 [MXnet](http://mxnet.io/) (用于深度学习模型)之类的东西来训练带有标签数据的机器学习模型。在本指南的后面，我们将使用一个名为 [Clarifai](https://www.clarifai.com/) 的 Web 服务和 Google 的 TensorFlow 浏览几个例子。

在撰写本文时(2017 年 4 月)，高级 API 非常容易使用，但数量并不多。允许你建立自己的模型的低级 API 很难使用，有效地使用它们需要很多技巧。但是这个领域正在迅速发展，所有主要的公共云提供商(亚马逊、谷歌、IBM)都在努力开发更高级的 API，并改进用于将人工智能功能嵌入到自己代码中的工具。

# API vs .库 vs. SDK

当查看不同公司/团体提供的内容时，我们会看到有许多不同的首字母缩略词，并且经常使用重叠的首字母缩略词。其中，最常见的是:

*   **API。**API(应用编程接口)是一组用于构建软件的协议、功能和定义。传统上，API 定义抽象:软件的预期输入和输出，并以具体形式在*库*中实现。
*   **图书馆。**一个 API 的实际实现，一个库一般嵌入在使用该 API 的代码中，但也可能是独立的，由多个应用共享；这些被称为*动态共享库*，被 Windows 和 MacOS 等操作系统广泛用于为开发者提供不同类型的服务。
*   **SDK。**SDK(软件开发工具包)通常被认为在某种程度上比简单的库更高级，可能包括多个库，或使库更容易使用的不同工具，多语言绑定，或所谓的“包装器”,它们可以通过牺牲简单性来换取灵活性，从而向程序员隐藏使用库所涉及的一些复杂性。

首先，我们可以认为 API 是一组定义、协议、功能和能力，然后在一个或多个库中实现，这些库可以作为 SDK 的一部分发布。

需要注意的是，这是思考这些术语的一个很好的起点，但不是绝对的。特别是“API”已经成为一个有些超载的术语。例如，一家公司可能通过 web 服务提供一个“语音 API”*，然后提供可以用于不同环境和语言的附加库、绑定或 SDK。这种情况下的“语音 API”与我们上面讨论的更抽象的版本的不同之处在于，它可以通过发出特殊格式的 HTTP 请求来直接使用，而无需任何中间库；类似于 web 浏览器用来获取数据以呈现网页的请求。*

这些 API 大多使用 [JSON](http://www.json.org/) (少数使用 XML)作为来回传输数据的标准。这使得在不同的语言中使用(如果需要，创建)包装器变得很容易。

上面的伪代码示例遵循一种常见的模式:通过 REST 调用一个 API，传递文本、音频、图像或视频，并接收回包含远程服务执行的分析结果的 JSON 字典或数组。

从根本上来说，这些 API 是高级的、快速的、简单易用的。不利方面？对于您的需求来说，它们可能太不灵活，在这种情况下，您需要更深入地研究如何训练您自己的模型，我们将在后面看到。

Web APIs 的定价是基于使用的，通常是合理的。它们具有良好的可用性和正常运行时间，尽管它们很少向您提供 SLA 或其他关于它们将如何执行的保证。许多处于不同的开发状态:“beta”、“alpha”、“preview”等等。

就兼容性或标准而言，最后一点:没有。在不修改请求和响应处理的情况下，确实没有直接的方法将使用一个 API 的系统“移植”到另一个 API。另一方面，正如我们所看到的，调用和响应相当简单，因此切换服务对于小型系统来说并不难。

> 最初发表于[安德森·霍洛维茨](http://a16z.com/)的 [AI 剧本](http://aiplaybook.a16z.com/)。
> 
> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！