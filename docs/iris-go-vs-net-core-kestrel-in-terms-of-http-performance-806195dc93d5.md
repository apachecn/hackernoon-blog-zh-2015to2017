# Iris Go 与 NET Core Kestrel 在 HTTP 性能方面的比较

> 原文：<https://medium.com/hackernoon/iris-go-vs-net-core-kestrel-in-terms-of-http-performance-806195dc93d5>

Iris Golang 和 T2 红隼之间的公平基准。网芯 (C#)。

![](img/3507b7eee97b2cdc0e15d5130624dc0e.png)

你好！

本文是“就 HTTP 性能而言 Go vs .Net Core”系列的第二篇，如果您还没有阅读第一篇文章的话，您*应该*真的[阅读第一篇文章](/@kataras/go-vs-net-core-in-terms-of-http-performance-7535a61b67b8)。

> 为自己准备一杯咖啡或茶，无论你最喜欢什么！

## 关于反馈

在这一部分，我会试着给你一个关于[前一篇文章](/@kataras/go-vs-net-core-in-terms-of-http-performance-7535a61b67b8)的一部分(不好的方面)的大致想法，然而你可能根本不在乎或者你没有心情，因此你**可能** *想要* ***跳过*** 那个和**直接进入**基准**部分。**

在我们的[上一篇文章](/@kataras/go-vs-net-core-in-terms-of-http-performance-7535a61b67b8)中，我们比较了 [Iris web 框架](https://github.com/kataras/iris)和 [MVC](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller) 特性的性能。NET Core 通过测试一个简单的应用程序(以避免类似“您可以使用更快的 json 编码器”之类的争论)。

这篇文章引起了相当惊人的反响。大多数的评论都很好，很有礼貌，不管你同意还是不同意。有些人的评论带有负面的味道，但这是意料之中的，也是受欢迎的。

我总是努力做到负责任，所以我会回答你的大部分问题，这些问题都与文章避免重复有关。如果你在你的评论下面没有看到我的回复，那么搜索一下相关的问题，我相信你会找到我的答案的。

一些。NET 开发者通过 twitter 联系我，告诉我这不是真的。NET Core 因为我用的是 MVC 而不是 Kestrel，是不是意味着 C#开发者要避免。NET Core 的 MVC 特性？我不明白。从基准测试标题和[代码本身](https://github.com/kataras/iris/tree/master/_benchmarks)可以清楚地看出，我们正在对 Iris 和的 MVC 版本进行比较。NET Core，Iris 也有一个“更低的级别”,但是我们没有使用它。

Note: the IrisMVC is a foreign twitter account, it is not Iris web framework’s account.

我知道我的文章会对。网络世界。我最近收到了很多粗鲁的评论，有趣的是，发布最令人难以接受的评论的人都在为大公司工作，我的意思是，他们在某个年龄可以理解人类的接触，但实际上他们从来没有学到人类沟通的基本知识，礼貌。

我真的不觉得这是对的，我的意思是发表他们(已经公开的)评论是正确的决定，但无论如何，我们都应该尽快谴责他们。

He insists without even reading the article…

That guy really thinks that I got paid from someone? Sadly I have just 0.67$ at my personal bank account!

Scott’s reply to that guy. Beside the fact that every Scott’s talk is beyond the best It’s a great person with ethos as well.

另一方面，我真的没有任何问题，**仇恨言论**过去和将来都不会影响我，因为我体内没有那个东西——设计:p**T3。我不认为这些是人身攻击，也不害怕我，但这个**并不意味着我应该容忍它们，心照不宣**。**

例如[那个家伙](https://twitter.com/abatishchev) **声称**他在微软工作，但我不相信受过“高等教育”的人能写出这样的东西:

Even if the code ran in production mode he doesn’t care about that, he just want to comment something. One more guy who didn’t actually read the article neither the code inside it.

![](img/1ec7186f3bde76eadcf23c2159dbc2fa.png)![](img/de565c395689b531f7cc3fa842a2923d.png)![](img/d0878a3b63f34b9a191297a5874cc371.png)

He’s clearly promoting hate without reason, just to be said.

**不，他们没有就此止步**，你会惊讶于这个评论所宣扬的民主意识！

That guy for example, reported dev.to for sharing my article. Bad dev.to authors, don’t share my articles again, he doesn’t allow these things! “after investigating” oh boy…

B 除了遗漏的论点和诽谤性的评论，第一个人 [Ali Malekpour](https://twitter.com/AliMalekpour) 很好地发表了他的论点，所以我认真地考虑了一下，他实际上**给了我这个想法:**为这两个世界的“低级”web 端编写基准，去。我们到了:)

# 标杆管理

这次我们将比较“低级”的速度。NET Core 的服务器实现名为 **Kestrel** 和**Iris**’“低级”处理程序，我们将测试两个简单的应用程序，第一个**和第二个**几乎与我们之前的应用程序相同，但使用处理程序编写，第二个**和第三个**测试将包含一个单一的路由，该路由基于一个键(字符串)设置和获取一个**会话**值(字符串)并将该会话值返回给客户端。

## 五金器具

*   处理器:英特尔酷睿 i7–4710 HQ CPU @ 2.50 GHz 2.50 GHz
*   内存:8.00 GB

## 软件

*   操作系统:Microsoft Windows[版本 10.0.15063]，电源计划是“高性能”
*   HTTP 基准工具:[https://github.com/codesenberg/bombardier](https://github.com/codesenberg/bombardier)，最新版本 1.1
*   。网芯:[https://www.microsoft.com/net/core](https://www.microsoft.com/net/core)，最新版本 2.0
*   iris:[https://github.com/kataras/iris](https://github.com/kataras/iris)，最新版本 8.3 内置 [go1.8.3](https://golang.org/)

## 首次申请

Spawn `1000000 requests`与`125` 不同的“线程”，以一个动态注册的路由路径为目标，用一个简单的`“value”`文本响应。

**。净芯(红隼)**

*源代码*

*启动。NET Core Kestrel web 服务器*

```
$ cd netcore
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press Ctrl+C to shut down.
```

*瞄准并运行 HTTP 基准工具*

```
$ bombardier -c 125 -n 1000000 [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5)
Bombarding [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5) with 1000000 requests using 125 connections
 1000000 / 1000000 [======================================================================================] 100.00% 10s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     97884.57    8699.94     110509
  Latency        1.28ms   682.63us    61.04ms
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    17.73MB/s
```

**虹膜**

*源代码*

*启动 Iris Go 网络服务器*

```
$ cd iris
$ go run main.go
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press CTRL+C to shut down.
```

*瞄准并运行 HTTP 基准工具*

```
$ bombardier -c 125 -n 1000000 [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5)
Bombarding [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5) with 1000000 requests using 125 connections
 1000000 / 1000000 [=======================================================] 100.00% 8s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec    117917.79    4437.04     125614
  Latency        1.06ms   278.12us    19.03ms
  HTTP codes:
    1xx - 0, 2xx - 1000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    21.93MB/s
```

## 摘要

*   完成`1000000 requests`的时间——越短越好。
*   每秒请求数—越大越好。
*   延迟—越短越好
*   吞吐量—越大越好。
*   LOC(代码行)—越小越好。

。使用 **63 代码行**编写的 NET Core (Kestrel)应用程序运行了 **10 秒**，每秒处理 **97884.57** 个请求，平均 **17.73MB/s** ，平均 **1.28ms** 延迟， **61.04ms** 最大。

使用 **14 代码行**编写的 Iris 应用程序运行了 **8 秒**，每秒处理 **117917.79** 个请求，平均 **21.93MB/s** ，平均 **1.06ms** 延迟， **19.03ms** 最大延迟。

## 第二次申请(会话)

用针对静态请求路径的`125 different “threads”`生成`5000000 requests`，基于名称`“key”` ，字符串值`”value"`设置并获取会话，并将会话值写入(或返回，由您选择)响应流。

**。具有会话的网络核心(Kestrel)**

*源代码*

*启动。NET Core Kestrel web 服务器*

```
$ cd netcore-sessions
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-sessions
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press Ctrl+C to shut down.
```

*瞄准并运行 HTTP 基准工具*

```
$ bombardier -c 125 -n 5000000 [http://localhost:5000/setget](http://localhost:5000/setget)
Bombarding [http://localhost:5000/setget](http://localhost:5000/setget) with 5000000 requests using 125 connections
 5000000 / 5000000 [====================================================================================] 100.00% 2m40s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     31844.77   13856.19     253746
  Latency        4.02ms    15.57ms      0.96s
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    14.51MB/s
```

**Iris 与会话**

*源代码*

*启动 Iris Go 网络服务器*

```
$ cd iris-sessions
$ go run main.go
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press CTRL+C to shut down.
```

*瞄准并运行 HTTP 基准工具*

```
$ bombardier -c 125 -n 5000000 [http://localhost:5000/setget](http://localhost:5000/setget)
Bombarding [http://localhost:5000/setget](http://localhost:5000/setget) with 5000000 requests using 125 connections
 5000000 / 5000000 [====================================================================================] 100.00% 1m15s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     66749.70   32110.67     110445
  Latency        1.88ms     9.13ms      1.94s
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    20.65MB/s
```

## 摘要

*   完成`5000000 requests`的时间到了——越小越好。
*   每秒请求数—越大越好。
*   延迟—越短越好
*   吞吐量—越大越好。

。带有会话应用的 NET Core 运行了 **2 分 40 秒**，每秒处理 **31844.77** 个请求，其中 **14.51MB/s** ，平均延迟 **4.02ms** ，最大延迟 **0.96s** 。

Iris with Sessions 应用程序运行了 **1 分 15 秒**，每秒处理 **66749.70** 个请求，平均 **20.65MB/s** ，平均**1.88 毫秒**延迟，最大**1.94 秒**。

# 收场白

我们真的不得不承认 Kestrel 相对于它的 MVC 端运行得相当快，**祝贺** [**开源项目的贡献者**](https://github.com/dotnet/corefx) **和** [**微软**](https://www.microsoft.com/) 启动并支持它！

像上一篇文章一样，这篇文章[有自己的截图](https://github.com/kataras/iris/tree/master/_benchmarks/screens)取自我的电脑，你在这里看到的相同代码片段也存在于 gists 和[github 库](https://github.com/kataras/iris/tree/master/_benchmarks)中，点击链接即可导航。

我希望看到更多的测试人员，所以下载源代码并从你的机器上运行基准，**与我们其他人分享结果。**

如果你想和我讨论某个话题，我随时欢迎新的机会和新的朋友，在我的推特上你可以找到我所有的联系方式！

[](https://twitter.com/MakisMaropoulos) [## Gera simos Maropoulos(@ MakisMaropoulos)|推特

### Gerasimos Maropoulos 的最新推文(@MakisMaropoulos)。那个叫#golang 的家伙。自学 10 多年的开发人员…

twitter.com](https://twitter.com/MakisMaropoulos) 

**感谢**你给我个人时间来阅读这篇文章，像你对上一篇文章所做的那样，把它传播给外界。

我会在我的下一篇文章中看到你…一如既往，玩得开心！

我喜欢当**我不止一次点击**按钮时的视觉效果，你呢？很简单:只要点击拍手按钮。如果感觉强烈，再点一下**(或者直接按住** **)。**

![](img/e7bd15bcb40212893d2e7daf3f20813b.png)