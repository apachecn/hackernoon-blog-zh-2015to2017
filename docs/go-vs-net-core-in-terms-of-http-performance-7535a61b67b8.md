# 就 HTTP 性能而言，Go 与 NET Core

> 原文：<https://medium.com/hackernoon/go-vs-net-core-in-terms-of-http-performance-7535a61b67b8>

![](img/4319b394cf10fc6681b63c0a30e40c57.png)

朋友们好！

最近我听到了很多关于新的。NET 核心及其性能，尤其是在 web 服务器上。

我不想开始比较两种不同的东西，所以我做了相当长时间的耐心等待一个更稳定的版本。

本周一，微软[宣布了。网芯 2.0 版](https://blogs.msdn.microsoft.com/dotnet/2017/08/14/announcing-net-core-2-0/)，所以感觉准备好了！你知道吗？

正如我们已经提到的，我们将在这里比较两个相同的东西，在应用程序，预期响应和运行时间的稳定性方面，所以我们不会试图在游戏中加入更多的东西，如`JSON`或`XML`编码器和解码器，只是一个简单的文本消息。为了实现公平的比较，我们将在双方都使用 [MVC 架构模式](https://en.wikipedia.org/wiki/Model%E2%80%93view%E2%80%93controller)。网芯。

# 先决条件

[Go](https://golang.org/) (或 Golang):是一种[快速发展的](http://www.tiobe.com/tiobe-index/)开源编程语言，旨在构建简单、快速、可靠的软件。

支持 MVC 的 web 框架并不多，但幸运的是 Iris 做了这项工作。

Iris :一个快速、简单、高效的面向 Go 的微 web 框架。它为你的下一个网站、API 或分布式应用程序提供了一个漂亮的表达和易于使用的基础。

[C#](https://en.wikipedia.org/wiki/C_Sharp_(programming_language)) :是一种通用的、面向对象的编程语言。它的开发团队由安德斯·海尔斯伯格领导。

[。NET Core](https://www.microsoft.com/net/) :在任何平台上，用更少的时间开发高性能应用。

从 https://golang.org/dl 下载围棋。网芯来自[https://www.microsoft.com/net/core](https://www.microsoft.com/net/core)。

在你下载并安装了这些之后，你将需要 Go 端的 Iris。安装非常简单，只需打开您的终端并执行:

```
go get -u github.com/kataras/iris/v12
```

# 标杆管理

## 五金器具

*   处理器:英特尔酷睿 i7–4710 HQ CPU @ 2.50 GHz 2.50 GHz
*   内存:8.00 GB

## 软件

*   操作系统:Microsoft Windows[版本 10.0.15063]，电源计划是“高性能”
*   HTTP 基准工具:[https://github.com/codesenberg/bombardier](https://github.com/codesenberg/bombardier)，最新版本 1.1
*   。网芯:[https://www.microsoft.com/net/core](https://www.microsoft.com/net/core)，最新版本 2.0
*   iris:[https://github.com/kataras/iris](https://github.com/kataras/iris)，最新版本 8.3 内置 [go1.8.3](https://golang.org/)

这两个应用程序都将只返回请求路径“api/values/{id}”上的文本“value”。

**。网络核心 MVC**

![](img/358e761a7629c39fbcbbe63692bfd37d.png)

*Logo designed by* [*Pablo Iglesias*](https://github.com/campusMVP/dotnetCoreLogoPack)*.*

使用`dotnet new webapi`创建。那个`webapi`模板将为您生成代码，包括对`GET` 方法请求的`return “value”` 。

*源代码*

*启动。网络核心网络服务器*

```
$ cd netcore-mvc
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-mvc
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press Ctrl+C to shut down.
```

*瞄准并运行 HTTP 基准工具*

```
$ bombardier -c 125 -n 5000000 [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5)
Bombarding [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5) with 5000000 requests using 125 connections
 5000000 / 5000000 [=====================================================] 100.00% 2m3s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec     40226.03    8724.30     161919
  Latency        3.09ms     1.40ms   169.12ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:     8.91MB/s
```

**虹膜 MVC**

![](img/ab69bc774ac7de1935310d5412c56b55.png)

Logo designed by [Santosh Anand](https://github.com/santoshanand).

*源代码*

*启动 Go 网络服务器*

```
$ cd iris-mvc
$ go run main.go
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press CTRL+C to shut down.
```

*瞄准并运行 HTTP 基准工具*

```
$ bombardier -c 125 -n 5000000 [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5)
Bombarding [http://localhost:5000/api/values/5](http://localhost:5000/api/values/5) with 5000000 requests using 125 connections
 5000000 / 5000000 [======================================================] 100.00% 47s
Done!
Statistics        Avg      Stdev        Max
  Reqs/sec    105643.81    7687.79     122564
  Latency        1.18ms   366.55us    22.01ms
  HTTP codes:
    1xx - 0, 2xx - 5000000, 3xx - 0, 4xx - 0, 5xx - 0
    others - 0
  Throughput:    19.65MB/s
```

对于那些通过图像更好理解的人，我也打印了我的屏幕！

点击[这里](https://github.com/kataras/iris/tree/master/_benchmarks/screens)看这些截图。

## 摘要

*   完成`5000000 requests`的时间——越小越好。
*   每秒请求数—越大越好。
*   延迟—越短越好
*   吞吐量—越大越好。
*   内存使用—越小越好。
*   LOC(代码行)—越小越好。

。NET Core MVC 应用程序使用 86 行代码编写，运行了 **2 分 8 秒**，在**平均 3.19 毫秒**延迟和**最大 229.73 毫秒**内每秒处理 **39311.56** 个请求，所有这些的内存使用量大约为 126MB(不含 dotnet 主机)。

Iris MVC 应用程序使用 27 行代码编写，运行了 **47 秒**，在**平均 1.18 毫秒**延迟和**最大 22.01 毫秒**内，每秒处理 **105643.71** 个请求，所有这些的内存使用大约为 12MB。

> *还有另一个带有模板的基准，滚动到底部。*

**2017 年 8 月 20 日更新**

正如乔希·克拉克和斯科特·汉瑟尔曼在本次重新发布会上指出的。NET Core `Startup.cs`文件中带有`services.AddMvc();`的行可以替换为`services.AddMvcCore();`。我按照他们有用的指示重新运行了基准测试。本文现在包含了。NET 核心应用程序，Josh 和 Scott 都提到了这些变化。

It had a small difference but not as huge (8.91MB/s from 8.61MB/s)

对于那些想要与标准`services.AddMvc();` 进行比较的人，您可以通过按下[这里的](https://github.com/kataras/iris/blob/master/_benchmarks/screens/5m_requests_netcore-mvc.png)来查看旧的输出。

# 你能再多呆一会儿吗？

让我们再运行一个基准测试，生成`1000000 requests`，但是这次我们期望`HTML`通过视图引擎由模板生成。

## 。带模板的 NET Core MVC

*启动。网络核心网络服务器*

```
$ cd netcore-mvc-templates
$ dotnet run -c Release
Hosting environment: Production
Content root path: C:\mygopath\src\github.com\kataras\iris\_benchmarks\netcore-mvc-templates
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press Ctrl+C to shut down.
```

*定位并运行 HTTP 基准工具*

```
Bombarding [http://localhost:5000](http://localhost:5000) with 1000000 requests using 125 connections
 1000000 / 1000000 [====================================================] 100.00% 1m20s
Done!
Statistics Avg Stdev Max
 Reqs/sec 11738.60 7741.36 125887
 Latency 10.10ms 22.10ms 1.97s
 HTTP codes:
 1xx — 0, 2xx — 1000000, 3xx — 0, 4xx — 0, 5xx — 0
 others — 0
 Throughput: 89.03MB/s
```

## 带模板的 Iris MVC

*启动 Go 网络服务器*

```
$ cd iris-mvc-templates
$ go run main.go
Now listening on: [http://localhost:5000](http://localhost:5000)
Application started. Press CTRL+C to shut down. 
```

*瞄准并运行 HTTP 基准工具*

```
Bombarding [http://localhost:5000](http://localhost:5000) with 1000000 requests using 125 connections
 1000000 / 1000000 [======================================================] 100.00% 37s
Done!
Statistics Avg Stdev Max
 Reqs/sec 26656.76 1944.73 31188
 Latency 4.69ms 1.20ms 22.52ms
 HTTP codes:
 1xx — 0, 2xx — 1000000, 3xx — 0, 4xx — 0, 5xx — 0
 others — 0
 Throughput: 192.51MB/s
```

**总结**

*   完成`1000000 requests`的时间——越小越好。
*   每秒请求数—越大越好。
*   延迟—越短越好
*   内存使用—越小越好。
*   吞吐量—越大越好。

。NET Core MVC with Templates 应用程序运行了 **1 分 20 秒**，每秒处理 **11738.60** 个请求，平均 **89.03MB/s** ，平均 **10.10ms** 延迟， **1.97s** 最大延迟，所有这些应用程序的内存使用量约为 193MB(不含 dotnet 主机)。

Iris MVC 与模板应用程序运行了 **37 秒**，每秒处理 **26656.76** 个请求，平均 **192.51MB/s** 在 **1.18ms** 延迟内，最大 **22.52ms** 内，所有这些的内存使用大约为 17MB。

# 接下来呢？

从[那里](https://github.com/kataras/iris/tree/master/_benchmarks)下载示例源代码，在你的机器上运行相同的基准测试，然后回到这里与我们分享你的结果！

对于想加其他 go 或者 c#的。net 核心 web 框架列表，请推送一个 PR 到[这个库](https://github.com/kataras/iris)里面的`_benchmarks`文件夹。

我需要亲自感谢[开发到](https://dev.to/kataras/go-vsnet-core-in-terms-of-http-performance)团队在他们的 twitter 账户上分享我的文章。

**感谢**所有 100%绿色反馈，玩得开心！

## 更新:2017 年 8 月 21 日星期一

很多人找到我，说他们想看一篇基于。网芯的下级**红隼**这次。

因此，我按照下面的链接了解了 Kestrel 和 Iris 之间的性能差异，它还包含一个会话存储管理基准！

[](https://hackernoon.com/iris-go-vs-net-core-kestrel-in-terms-of-http-performance-806195dc93d5) [## Iris Go 与 NET Core Kestrel 在 HTTP 性能方面的比较

### Iris Golang 和 Kestrel 之间的公平基准。NET Core (C#)。

hackernoon.com](https://hackernoon.com/iris-go-vs-net-core-kestrel-in-terms-of-http-performance-806195dc93d5) 

我喜欢不止一次点击按钮时的视觉效果，你呢？很简单:只要点击拍手按钮。如果感觉强烈，再点一下**(或者直接按住** **)。**

![](img/e7bd15bcb40212893d2e7daf3f20813b.png)