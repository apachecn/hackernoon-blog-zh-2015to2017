# 2017 年围棋编程要看的 5 件事

> 原文：<https://medium.com/hackernoon/5-things-to-watch-in-go-programming-in-2017-39cd7a7e58e3>

# 今年像动态插件、无服务器 Go、HTTP/2 Push 这样的创新对你的开发意味着什么？

Go 1.8 将于下个月发布，预计将有几个新功能，包括:

*   [HTTP/2 推送](https://beta.golang.org/doc/go1.8#h2push)
*   [HTTP 服务器正常关闭](https://beta.golang.org/doc/go1.8#http_shutdown)
*   [外挂](https://beta.golang.org/doc/go1.8#plugin)
*   [默认 GOPATH](https://beta.golang.org/doc/go1.8#gopath)

这些新特性中哪一个会产生最大的影响可能取决于你和你的开发团队如何使用 Go。自从 Go 1.0 在 2012 年发布以来，它对简单性、并发性和内置支持的强调一直保持着它的[受欢迎程度](https://github.com/golang/go/wiki/GoUsers)指向右上方，所以对于“*Go 有什么用途？*“不断相乘。

在这里，我将对即将发布的版本和最近引起我注意的 Go 世界中的一些事情提供一些想法。这不是一个详尽的列表，所以[让我知道](https://twitter.com/SuConant)你认为 2017 年 Go 中还有哪些重要的东西。

# Go 的超级可部署性+插件=容器，有人吗？

计划下个月发布的 1.8 版本有几个人想知道动态插件的增加会如何影响容器之类的东西，动态插件是用来加载共享库的代码，而代码在编译时并不是程序的一部分。动态插件应该使得在容器中使用高并发微服务变得更加简单。您将能够轻松地将插件作为外部进程加载，并拥有容器中微服务的所有附加好处:保护您的主进程不会崩溃，并且不会在您的内存空间中留下任何东西。插件的动态支持对于在 Go 中使用容器来说应该是一个福音。

*参加一些高手直播围棋培训，报名* [***超越基础***](https://www.safaribooksonline.com/live-training/courses/go-beyond-the-basics/0636920065357/) *。*

# 跨平台支持将继续吸引开发者

在 Go 开源的 7 年里，它已经在全球范围内被采用。为 Jupyter 维护围棋内核的数据科学家和工程师 Daniel Whitenack 告诉我，他最近在西伯利亚(是的，西伯利亚！是的，数据科学和 Go——稍后会详细介绍。。。)和“惊讶地发现那里的 Go 社区是如此的活跃和活跃。”人们将继续在他们的项目中采用 Go 的另一个重要原因是交叉编译，正如几位 Go 专家解释的那样，[在 1.5 版本中变得更加容易。使用 Python 等其他语言的开发人员应该会发现，在目标平台上没有虚拟机的情况下，能够为多个操作系统构建一个捆绑的、随时可以部署的应用程序，这是在 Go 中工作的一个关键优势。](/@rakyll/go-1-5-cross-compilation-488092ba44ec#.7s7sxmc4h)

将这种跨平台支持与 1.8 版本中预计的编译时间 15%的速度提升结合起来，你就会明白为什么 Go 是初创公司最喜欢的语言。

*对围棋基础感兴趣？查看*[***Go Fundamentals 学习路径***](http://shop.oreilly.com/category/learning-path/go-fundamentals.do) *以获得 O'Reilly 专家的指导，帮助您入门。*

# 一个围棋解说员正在工作中；再见读取-评估-打印-循环

一些非常聪明的人正在开发一个围棋解释器，我肯定会关注这个。正如你们中的许多人所熟知的，有几种读取-求值-打印-循环(REPL)解决方案可以评估表达式并确保您的代码按预期工作，但这些方法通常意味着容忍不方便的警告，或者通过几个方法找到适合您用例的方法。一个强大的，一致的翻译将是伟大的，一旦我听到更多，我会让你知道。

在你的开发中处理 Go 复杂性？观看奥莱利的 [***中级围棋***](http://shop.oreilly.com/product/0636920047513.do) *视频训练。*

# 移动无服务器—那会是什么样子？

是的，现在有很多关于无服务器架构的宣传，也就是所谓的功能即服务(FaaS)。但有时无风不起浪，所以无服务器领域会发生什么呢？今年我们能看到一个支持 Go 的无服务器服务吗？

[AWS](https://hackernoon.com/tagged/aws) Lambda 是最知名的无服务器提供商，但谷歌最近也推出了[谷歌云功能](https://cloud.google.com/functions/docs/)。这两种 FaaS 解决方案都允许您在不管理服务器的情况下运行代码；您的代码存储在为您管理的服务器集群上，仅在触发事件调用它时运行。AWS Lambda 目前支持 JavaScript、Python 和 Java，另外您还可以启动 Go、Ruby 和 bash 进程。Google Cloud Functions 只支持 JavaScript，但是 Java 和 Python 很快也会得到支持。许多物联网设备已经利用了无服务器方法，随着 Go 越来越多地被初创公司采用，无服务器似乎是一个可能的增长点，所以我正在观察这些无服务器解决方案中支持 Go 的发展。

已经有[几个支持 Go 的框架正在为 AWS Lambdas 开发](https://github.com/SerifAndSemaphore/go-serverless-list):

*   [λ Gordon](https://github.com/jorgebastida/gordon) —使用 CloudFormation 创建、连接和部署 AWS Lambdas
*   [Apex](https://github.com/apex/apex) —构建、部署和管理 AWS Lambda 功能
*   [Sparta](http://gosparta.io/)—AWS Lambda 微服务的 Go 框架

还有一个支持 Go 的 AWS Lambda 替代方案:

*   [Iron.io](https://www.iron.io/) :建立在 Docker 和 Go 之上；语言不可知；支持 [Golang](https://hackernoon.com/tagged/golang) ，Python，Ruby，PHP，和。网

*关于无服务器架构的更多信息，请观看 Mike Roberts 在旧金山 O'Reilly 软件架构大会上的主题演讲:* [***无服务器介绍***](https://www.safaribooksonline.com/library/view/oreilly-software-architecture/9781491976142/video288473.html?utm_source=oreilly&utm_medium=newsite&utm_campaign=5-things-to-watch-in-go-programming-body-text-cta) *。*

# 去找数据——不，真的！

我在这篇文章的开头暗示了这一点:也许令人惊讶的是，很多人正在使用 Go 进行数据科学和机器学习。关于这是否是一个很好的选择还有一些争论，但是根据 2016 年 12 月 Gopher Academy[的年度来临帖子，你会注意到 30 个帖子中至少有 4 个是关于 ML 或某种分布式数据处理的，这正在发生。](https://blog.gopheracademy.com/series/advent-2016/)

我之前关于 Go 易于部署的观点可能是数据科学家使用 Go 的一个关键原因:他们可以更容易地在可读和可生产的应用程序中向他人展示他们的数据模型。再加上围棋的广泛应用(正如我前面提到的，它的流行是向右上方的！)，并且您让数据人员创建“与其他人一起工作和娱乐”的应用程序数据科学家在 Go 中构建的任何应用程序都将与公司的其他部分使用相同的语言，或者至少非常适合现代架构。

*关于 Go 数据科学的更多信息，Daniel Whitenack 写了一篇精彩的概述，解释了它是如何被使用的:* [***数据科学地鼠***](https://www.oreilly.com/ideas/data-science-gophers) *。*

*最初发表于*[*www.oreilly.com。*](https://www.oreilly.com/ideas/5-things-to-watch-in-go-programming?utm_source=external&utm_medium=medium&utm_campaign=5-things-to-watch-in-go-programming-in-2017)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！