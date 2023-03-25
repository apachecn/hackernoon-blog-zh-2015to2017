# 使用 Kafka 和 NodeJS、SocketIO 和 D3js 构建实时地图。

> 原文：<https://medium.com/hackernoon/using-kafka-with-nodejs-socketio-and-d3js-to-build-a-real-time-map-b6d3c3eae381>

创始人/数据科学家 Isaac Godfried

大家好，欢迎回到我们的系列。几周前，我们发表了一篇关于我们如何开发卡夫卡“主干”以获取 USGS 流量信息的报道。本周，我们将继续展示如何将 Kafka producer 与 NodeJS 和 SocketIO 结合使用，以向您的客户端推送实时更新。在我们的具体情况下，由于 USGS API，我们的更新不会接近实时；尽管如此，我们还是决定以实时“就绪”的方式实现它，以防 USGS 加速他们的 API(不太可能)，或者可能将其移植到其他应用程序，因为这只是一个很好的体验。

首先，我们用 Express 创建了一个简单的 NodeJS 应用程序。我们创建了一个 index.js，其中包含我们的核心代码和一个 index.html 文件。为了使用 Kafka 和 Node，你需要安装一个使用 NPM 的 Kafka JavaScript 库。我们建议您使用 kafka-node，因为它似乎对我们来说相当不错。为此，您可以使用命令:`npm install kafka-node` 。警告不要简单地做`npm install node`，因为这将安装一个非常旧的 Kafka 包，它已经 3 年多没有更新了，当它试图连接到你的主题时会抛出错误。

一旦我们建立了库，我们就创建了一个非常简单的函数来发送 SocketIO 消息。然后，我们用 kafka 节点库初始化了一个 Kafka 消费者，并编写了一个 consumer.on(message)函数来调用 SocketIO 函数。我们还添加了一些简单的代码来保存消息，以实现持久性。如果这看起来令人困惑，不要担心下面的完整代码，你会看到它是多么简单。

Code is self explanatory. Basically on a message from Kafka we call SocketIO which emits the message to the client. I also included some stuff just to save the message. In reality you would probably be saving the message to Redis, SQL or something else but this is just my impromptu archive method.

我们的下一步是在 web 浏览器中将消息发送给客户端。幸运的是，使用 SocketIO 这是相当简单的。

现在，我们希望使用来自美国地质调查局的 JSON 消息来更新我们的地图。为此，我们决定使用非常流行的数据可视化库 D3js。基本上在我们的 D3js 地图上有河流的圆圈，根据河流的流量是高还是低显示不同的颜色(你可以看到 http://rivermaps.herokuapp.com 的[作为例子)。我们希望从 SocketIO 接收的消息中获取信息，并实时更新我们的地图。为此，我们创建了一个在 socket 事件上调用的更新函数。更新事件将通过基于对应于 JSON 消息的纬度/经度选择相关的圆圈来改变圆圈(即河流)的颜色。如果这听起来令人困惑，那么不要担心，因为像上次一样，代码实际上要简单得多。](http://rivermaps.herokuapp.com)

从各方面考虑都很简单！因此，总的来说，要创建这样一个程序，你需要一个 Kafka Producer(使用最适合你的语言)，一个在 NodeJS 中调用 SocketIO 的 Kafka consumer，以及一个 SocketIO 在收到消息时调用的图形更新方法。

一些最后的想法:

似乎包含 SocketIO 几乎是多余的。我想知道你是否能用 kafka-node 发送信息？

尽管这样做是可行的，但是为了让用户从中受益(在我们的例子中)，他们必须在页面上停留一个多小时而不刷新。看到每秒钟都有实际实时数据更新的情况会有趣得多。如果美国地质勘探局能让他们的 API 运行得更快就好了！

将实时 ML 和流处理的例子与 Flink 结合起来也是很好的。谁知道将来会不会有一篇关于实时 Twitter 情绪分析和地理地图的文章呢？

不管怎样，今天就到这里吧！希望这对那些来这里寻找关于如何建立和运行自己的实时系统的信息的人有所帮助。