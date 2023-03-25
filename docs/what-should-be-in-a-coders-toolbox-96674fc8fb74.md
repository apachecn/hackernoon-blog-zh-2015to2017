# 程序员的工具箱里应该有什么？

> 原文：<https://medium.com/hackernoon/what-should-be-in-a-coders-toolbox-96674fc8fb74>

作为开发人员，我们经常遇到许多不同的技术，对我们来说都是新的和陌生的，有人告诉我们这是下一件大事，我们必须学习它。我已经听了这些人很多年了，我已经找到了一些模式，可以适当地抽象出你的工具箱中应该有哪些类型的技术。所以这里有…

![](img/2bd00cc019138556a9053fa878f01778.png)

I mean more like software but you get the metaphor, ha!

首先，我相信全栈开发理念。我真诚地相信任何开发人员都应该能够完成任何任务。这并不是说没有专家或全职特定技术开发人员的空间，但我相信最好的开发人员是那些与其他技术混合并融合的人，他们将模式带回他们的“家乡”语言，保持他们的学习技能敏锐，并保持他们的思想开放。也就是说，我相信开发人员应该对所有的事情都有所了解，以下是我在职业生涯中所涉及的一些基础。那么，对于每个优秀的开发人员来说，最小的工具箱里应该有什么呢？

# 网络框架

这可能是 [Ruby on Rails](http://rubyonrails.org/) 、 [Node.js](https://nodejs.org/en/) 、 [PHP](http://php.net/manual/en/intro-whatis.php) 、 [Phoenix](http://www.phoenixframework.org/) 、 [Perfect](http://perfect.org/) 等等，这并不重要。这里的要点是 web 框架应该做什么——它响应给定的 HTTP 请求，从数据库中创建、读取、更新和删除(CRUD)数据，并提供合适的响应。它还可以启动后台任务，或者将数据添加到队列/流中，以便以后处理。

# 任务执行者/调度者

如上所述，任务运行器适用于需要在未来某个时间点运行的任务，或者按照计划运行的任务。这可能是 Cron、 [sidekiq](http://sidekiq.org/) 、 [Verk](https://github.com/edgurgel/verk) ，甚至是 windows 任务调度器。这里的想法是了解一些任务需要相对于时间本身或相对于请求发生，但是可以在以后的时间执行。例如，处理文件上传；回答是“我们收到了你的文件，谢谢！”但是后台任务可能是文件的实际处理，并且可能在处理完成时发送电子邮件。

# 队列软件

这可能是 [RabbitMQ](https://www.rabbitmq.com/) 或[亚马逊 SQS](https://aws.amazon.com/sqs/) 或 [Azure 队列存储/消息总线](https://docs.microsoft.com/en-us/azure/service-bus-messaging/service-bus-azure-and-service-bus-queues-compared-contrasted)。这里的想法是，后端的一些软件被命名为“生产者”，它们将数据放在队列中供“消费者”使用。这允许您根据服务器上的负载启动或停止更多的消费者。

# 流软件

与排队类似，进入队列的项目被消费者移走，流媒体软件让大量数据像河流一样流过，许多消费者可以对他们看到的经过做出反应。这可能是[卡夫卡](https://hortonworks.com/apache/kafka/)、[亚马逊 Kinesis](https://aws.amazon.com/kinesis/streams/) 等。队列和流之间的区别也很重要，但是现在不是讨论这个的时候。

# 前端框架

这可能是 [EmberJS](https://www.emberjs.com/) ， [Angular](https://angularjs.org/) ， [React+Redux](https://github.com/reactjs/react-redux) ， [Vue.js](https://vuejs.org/) ，甚至是 [jQuery](https://jquery.com/) ！了解前端框架的想法是学习一些有趣的课程，如浏览器怪癖、语言的转换/编译、web 调试/检查、响应设计、数据的解序列化和 UI/自动化测试。

# 移动应用框架

虽然不是必要的，但我认为至少学习一个移动平台是有用的，比如 iOS、Android 或 Windows 10 手机。还可能包括[科尔多瓦](https://cordova.apache.org/)、 [React Native](https://facebook.github.io/react-native/) ，甚至 [Unity](https://unity3d.com/) 。移动编程教授了很多关于技术限制下的编程，以及权衡许多用户体验的权衡，如小屏幕尺寸、电池寿命和隐私。IDE 支持也有助于营造一个愉快的工作环境。

# 一种脚本语言

有些时候，您需要一些快速和肮脏的东西来编写一些脚本。这可能是 AppleScript，Bash，Powershell，Python 或者 Ruby。只是一些自动化任务的一次性或运行时不时与 Cron 或类似。每个优秀的开发人员都应该知道何时何地写一些快速而简洁的东西，并且知道如何去做是非常好的。

# 关系数据库

这可能是我的 MySQL、T2 的 PostgreSQL、T4 的 MS SQL Server 或类似的东西。这里的想法是学习关系数据库是如何工作的，以及记录是如何存储和搜索的。如果您了解了存储过程相对于代码过程的好处，以及在存储和检索时可以进行什么样的优化，那将会很有帮助。

# 非关系数据库

这些工具越来越多，每一个都针对特定的任务进行了优化，比如针对搜索的 [ElasticSearch](https://www.elastic.co/) 或者针对基于时间的数据的 [Druid](http://druid.io/) 。这也可能包括更多的通用 NoSQL 数据库，如 [MongoDB](https://www.mongodb.com/) 或 [DynamoDB](https://aws.amazon.com/dynamodb/) 。您应该试着理解 NoSQL 数据库和 SQL 数据库之间的区别(非关系数据库和关系数据库)

那是你的工具箱！有了这些，你或许可以构建任何你想要的东西，或者为你贡献几乎任何软件业务。当然，你不必在一周内学会所有的知识，这需要几年的时间。你也不需要对所有这些都充满热情，你可以挑选——这是你的职业。可能还有一些其他值得一提的技术，我已经忘记了，所以温和平静地向我提起它们吧！祝你好运:)

*这是我的* [*初级开发者日记*](https://www.samjarman.co.nz/diaries) *博客系列的第 12 篇帖子。我每周都在写更多的东西，你可以在我的网站* [*上注册听更多，看以前的帖子。*](https://www.samjarman.co.nz/diaries/)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)