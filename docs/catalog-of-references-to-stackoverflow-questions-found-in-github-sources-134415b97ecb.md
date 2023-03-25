# 用 Google BigQuery 再分析一下 GitHub 和 StackOverflow 数据

> 原文：<https://medium.com/hackernoon/catalog-of-references-to-stackoverflow-questions-found-in-github-sources-134415b97ecb>

**TL；我建立了一个网站，你可以在这里探索在 [Github](https://hackernoon.com/tagged/github) 的源代码中提到的堆栈溢出问题。在 [http://sociting.biz](http://sociting.biz) 查看**

**动机**
我是 [Google](https://hackernoon.com/tagged/google) 云平台的忠实粉丝，尤其喜爱其名为 BigQuery 的数据仓库实现。2016 年夏天，Github 和谷歌在 BigQuery 上向所有人开放了开源数据，以下是令人难以置信的数字:

> 这个 3TB+的数据集包含了迄今为止最大的 GitHub 活动发布源。它包含超过 280 万个开源 GitHub 存储库内容的完整快照，包括超过 1.45 亿个唯一提交，超过 20 亿个不同的文件路径，以及 1.63 亿个文件的最新修订版的内容，所有这些都可以使用正则表达式进行搜索。

从那以后，我从未厌倦探索这些数据，揭示有趣的模式或极端的样本，并发表关于我的发现的文章。
2016 年 12 月，谷歌团队再次唤醒了我的“内部研究员”——他们已经将 Stack Overflow 的问答历史添加到 BigQuery 的公共数据集集合中。在实践中，这意味着世界上最受欢迎的编程聊天现在可以用谷歌云平台的力量进行分析，例如，人们可以对堆栈溢出数据运行[情绪分析](https://medium.freecodecamp.com/always-end-your-questions-with-a-stack-overflow-bigquery-and-other-stories-2470ebcda7f#.bhdeolgrt)，并发现 Python 开发者发布的负面评论比例最低！不过最让我兴奋的是将堆栈溢出数据与其他公开可用的数据集结合起来的能力。例如，人们可以通过使用来自 NOAA 数据集的数据来尝试找出天气是否会影响堆栈溢出问题被回答的概率。
在栈溢出数据可用性的[介绍](https://cloud.google.com/blog/big-data/2016/12/google-bigquery-public-datasets-now-include-stack-overflow-q-a)中 [Felipe Hoffa](/@hoffa) 提供了连接 GitHub 和栈溢出数据的示例，以找出哪些是 Github 代码中引用最多的栈溢出问题——具体来说就是 Javascript。它引起了我的注意，因为我注意到了一些限制:
*该查询仅在源代码中搜索 stack overflow . com/questions/([0–9]+)/pattern。但是，引用问题还有其他形式:它可以是简短的 stack overflow . com/q/([0–9]+)/也可以是对其中一个答案的直接引用，比如 stack overflow . com/answers/([0–9]+)/
*该查询只处理 JavaScript 源代码，但也有很多其他编程语言。

因此，我着手为流行的编程语言构建 GitHub 源代码中引用的堆栈溢出问题的目录。

**获取数据**
*第一步*在 Github 源代码中查找引用 StackOverflow 问题或答案的代码行。Felipe Hoffa 好心地提供了一个表格，该表格保存了来自顶级 repos 的顶级语言的内容

结果保存在名为**so _ ref _ top _ repos _ top _ langs**的新表中，该表包含如下行

*步骤 2* 将结果与 StackOverflow 数据连接。查询应该处理从源代码中提取的问题 id 和答案 id

结果包含如下所示的行

像这样的记录大约有 31K 条，下一个问题是如何可视化它们。

**构建网站**
首先，我通过为每种编程语言创建单独的表，将结果数据转移到 SQLite 数据库。然后我构建了[web-site]([http://so citing . biz](http://sociting.biz))，它允许通过在语言之间切换来浏览数据，并跳转到 Github 源代码来检查问题/答案中的信息是如何应用于特定场景的。我还抓住了这个机会，在我的 Macbook Pro 上使用 ASP.NET Core 并实现了网站，而没有使用 Windows。生成的应用程序在后端使用跨平台 ASP MVC Web API，在前端使用 react+redux。源代码在 [Github repo](https://github.com/sAbakumoff/SoCiting2) 中完全可用。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！