# 你好码头工人

> 原文：<https://medium.com/hackernoon/hello-docker-56e0bc8deede>

Docker 是时下相当流行的一种新时尚。我之前对此了解不多，但在我实习的时候，我参加了来自 [Dieter Reuter](https://twitter.com/Quintus23M) (码头船长)和[Niclas Mietz](https://twitter.com/solidnerd)bee 42 solutions gmbh 的研讨会。

和他们一起参加为期两天的研讨会非常有趣，我也了解了很多。那么让我们从 Docker 开始吧。

在开始使用它之前，您应该在您的机器上安装 Docker。从这里得到帮助[如何安装 Docker](http://bfy.tw/h1i) 。

好了，现在你的机器有 Docker，所以现在你可能在想 Docker 到底是什么？

Docker 是一个用于构建、发布和运行应用程序的开放平台。使用 Docker，您可以像管理应用程序一样管理基础设施。这很容易，简单，也相当强大。

众所周知，我们总是从 Hello-world
`docker container run hello-world`
开始输出，你可以看到我们正在做很多事情(Docker 正在做的事情)。但在此之前，我想说明一些关于 Docker 引擎。

# 码头引擎

![](img/56cd28876c0a79a140d9495cc22744cc.png)

Docker 就像你的汽车一样，它有一个管理一切的引擎。它还包含一个名为 Docker Daemon 的服务器(`dockerd`命令),为客户端管理一切(Docker CLI)。在这两者之间，API 处理从客户机到服务器的请求。服务器通过管理映像、容器、网络和卷来相应地响应客户端。

*   **Docker 守护进程:**Docker 守护进程监听 API 请求并管理 Docker 对象(映像、容器、网络和卷)。一个守护进程也可以与其他守护进程通信来管理 Docker 服务(swarm 模式)。
*   **Docker 客户端**:Docker 客户端是很多 Docker 用户与 Docker 交互的方式。当你使用像`docker run`这样的命令时，客户端将这些命令发送给`dockerd`，T3 执行这些命令。`docker`命令使用 Docker API。Docker 客户端可以与多个守护进程通信。
*   **Docker 注册表**:Docker 注册表*存储 Docker 图像。Docker Hub 和 Docker Cloud 是任何人都可以使用的公共注册表，Docker 默认配置为在 Docker Hub 上查找图像。当您使用`docker pull`或`docker run`命令时，所需的图像从您配置的注册表中提取。当您使用`docker push`命令时，您的映像会被推送到您配置的注册表中。您可以通过获取新版本的映像并重新部署容器来升级应用程序。*
*   **图像**:一个*图像*是一个 Docker 容器的快照。通常，一个图像是基于另一个图像的*和一些额外的定制。例如，您可以构建一个基于`ubuntu`映像的映像，但是安装 Nginx web 服务器和您的应用程序，以及运行您的应用程序所需的配置细节。与其他虚拟化技术相比，映像重量轻、体积小、速度快。*
*   **容器**:容器是一个图像的可运行实例。您可以使用 CLI 创建、运行、停止、移动或删除容器。您可以将容器连接到一个或多个网络，为其附加存储，甚至基于其当前状态创建新的映像。默认情况下，容器与其他容器及其主机相对隔离。您可以控制容器的网络、存储或其他底层子系统与其他容器或主机的隔离程度。容器由其映像以及创建或运行时提供给它的任何配置选项来定义。当容器停止时，没有存储在持久存储中的对其状态的任何更改都会消失。
*   **服务**:它允许你跨多个 Docker 守护进程扩展容器，这些守护进程作为一个*集群*与多个*管理器*和*工作器*一起工作。群的每个成员都是一个 Docker 守护进程，这些守护进程都使用 Docker API 进行通信。服务允许您定义所需的状态，例如在任何给定时间必须可用的服务副本的数量。默认情况下，该服务在所有工作节点之间进行负载平衡。对于消费者来说，Docker 服务看起来是一个单独的应用程序。

在一些理论之后，现在让我们看看输出，

> *码头工人您好！*

1.  docker 客户端连接到 Docker 守护程序。
2.  Docker 守护进程从 Docker Hub 中提取“hello-world”映像。
3.  Docker 守护进程从该映像创建了一个新的容器，它运行产生您当前正在阅读的输出的
    可执行文件。
4.  Docker 守护进程将输出流式传输到 Docker 客户机，客户机将输出发送到您的终端。

就是这样，非常简单和容易。享受它的轻松。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)