# 我们从 Python 转向 Go 的 5 个原因

> 原文：<https://medium.com/hackernoon/5-reasons-why-we-switched-from-python-to-go-4414d5f42690>

![](img/fad7768bc4149c00189d5ce6f35e91cb.png)

Gophers from golang.org

> Python 太牛了！尤其是具有异步功能的 Python 3。但是真正的 Go 是不给任何在企业世界生存的机会…

如果你明白了这句话的意思，那么你可能已经尝试过或者正在寻找 Go 编程语言。我认为这是最简单的编程语言，适合任何类型的应用程序开发。是啊！你没看错，对我来说，围棋甚至比 JavaScript 更容易学。我认为这是 Go 语言在几年内变得如此流行的主要原因。

# 那么在使用 Go 之前我们有什么呢？

【TreeScale.com 主要是仪表盘和基于 API 的网络应用，还有一些分析工具。这是我们的技术堆栈

*   仪表盘**前端**的 React.js
*   Django Python for Dashboard **后端+认证服务**
*   **API 服务**的 Node.js
*   用于**数据库**的 PostgreSQL 和用于**日志**的 Cassandra
*   *用 Rust 语言自定义编写的容器注册表*

如您所见，整个基础设施完全基于微服务，大部分逻辑完全分离，即使采用不同的技术。

这就是我们开始考虑将 **API 服务和后端**合并到一个项目中的主要原因，因为它们有很多相似之处，但代码库在多种编程语言和技术上是重复的。但事实证明，我们需要做很多工作来定制 Django 的 API 服务，尤其是定制 JSON 响应。

在用 Django 构建 API 服务 1 个月后，我觉得 Django 很棒，直到你构建一些非常普通的东西，但是当你想要更多的性能和一些定制模块时，事情变得越来越复杂。

# 为什么要去？

正如我之前所说的，Go 语言是我见过和用过的最简单的语言。但这只是对我的第一个吸引。

以下是我们选择使用 Python Django 的 5 个主要原因

**#1 编译成单二进制**

Golang 是作为一种编译语言构建的，谷歌开发人员在这方面做得很好。使用*静态链接*它实际上根据操作系统类型和架构将所有依赖库和模块组合成一个单一的二进制文件。这意味着如果你正在用 Linux X86 CPU 在你的笔记本电脑上编译你的后端应用程序，你只需要把编译好的二进制文件上传到服务器上，它就可以工作了，不需要在那里安装任何依赖项！

**#2 静态型系统**

类型系统对于大规模应用程序非常重要。Python 是一种伟大而有趣的语言，但有时你会遇到不寻常的异常，因为你试图将变量作为整数使用，但结果却是字符串。

```
# Django will crash process because of this
def some_view(request):
    user_id = request.POST.get('id', 0)
    # If this post request has "id" parameter then
    # user_id would be a string, 
    # but you really thinking it is integer
    User.objects.get(id=user_id)
```

Go 将在编译时让您知道这个问题是一个编译器错误。这就是你为这种愚蠢的问题赢得时间的地方。

**#3 性能！！**

这可能令人惊讶，但在大多数应用情况下，Go 比 Python 快(2 和 3)。这里是[基准测试游戏](https://benchmarksgame.alioth.debian.org/u64q/compare.php?lang=go&lang2=python3)的结果，这是不公平的，这取决于应用类型和用例。

对于我们的例子，Go 表现得更好，因为他的并发模型和 CPU 可伸缩性。每当我们需要处理一些内部请求时，我们都使用单独的 Goroutine 来完成，这比 Python 线程节省了 10 倍的资源。因此，由于内置的语言功能，我们节省了大量资源(内存、CPU)。

**# 4 Go 不需要 Web 框架**

这就是编程语言最牛逼的地方。Go 语言创建者和社区已经内置了如此多的由语言核心支持的工具，以至于在大多数情况下你真的不需要任何第三方库。例如，它内置了语言，你可以构建非常复杂的 API 服务，甚至不用考虑在 Github 上找库！

当然，有很多为 Go 构建的库和框架，以及用 Go 制作 web 应用程序，但我会推荐不用任何第三方库来构建您的 web 应用程序或 API 服务，因为在大多数情况下，它们并不会让您的生活比使用原生包更轻松。

**#5 出色的 IDE 支持和调试**

当您尝试切换编程语言时，IDE 支持是最重要的事情之一。舒适的 IDE 平均可以节省 80%的编码时间。我为 JetBrains IDEA 找到了 [Go 插件，它也支持(Webstorm、PHPStorm 等……)。该插件提供了项目开发所需的一切，借助 JetBrains 理念的力量，您可以真正促进您的开发。](https://github.com/go-lang-plugin-org/go-lang-idea-plugin)

根据我们的代码库统计，在重写所有项目后，我们得到的代码比之前少了 64%。

> 你真的不需要调试不存在的代码。代码少，麻烦少！

# 结论

Go 给了我们巨大的灵活性，所有用例都使用单一语言，而且对所有用例来说，它都运行得非常非常好。我们的后端和 API 服务性能提高了大约 30%。现在，我们可以实时处理日志记录，将其传输到数据库，并使用 Websocket 从单个或多个服务进行流式传输！这是 Go 语言特性的惊人成果。

***别忘了“推荐”这篇帖子，分享你对此的想法。***

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)