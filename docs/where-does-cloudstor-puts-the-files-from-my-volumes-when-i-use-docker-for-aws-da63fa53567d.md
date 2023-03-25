# 当我使用 Docker for AWS 时，Cloudstor 将我的卷中的文件放在哪里？

> 原文：<https://medium.com/hackernoon/where-does-cloudstor-puts-the-files-from-my-volumes-when-i-use-docker-for-aws-da63fa53567d>

![](img/99237c6d7bddc13da1823317af3fea00.png)

嘿，关于 Docker 的另一个快速提示，特别是与`cloudstor`一起提供的用于 AWS 产品(edge edition)的 [Docker。这是一个卷插件，它使容器能够附加保存到](https://docs.docker.com/docker-for-aws/) [AWS EFS](https://aws.amazon.com/efs/) 中的卷，允许人们从共享存储和持久存储中受益(想想由 AWS 支持并由 Docker 包装在一个漂亮接口上的网络文件系统)。

在这里，我会告诉你如何可视化插件如何把东西放入 EFS。

**TL；dr** : **插件在一个容器中运行，容器中有一个挂载点，可以挂载到 EFS，为每个命名的卷创建一个目录(在机器上传播)。**

*注意。:我不是来自 Docker，也没有关于那里的事情如何运作的所有信息。其中一些东西是专有的，并没有太多的记录，但我们仍然可以有一个关于事情如何工作的想法。万一我做错了，请随时纠正我。*😁

首先，如果你已经设置过一次`docker-for-aws`(使用他们的标准 [CloudFormation](https://aws.amazon.com/cloudformation) 模板)，你可能会注意到你并没有真正登录到机器，而是登录到一个运行在主机上的 SSH 容器。这是一组保持运行的容器中的一个，用于与 AWS 进行交互，并使整个事情具有内聚性。

list of docker-for-aws containers running in the manager

不过，有一个容器不见了:T2 号。这是因为像`cloudstor:aws`(上面提到的卷插件)这样的插件运行在容器内部(参见[https://docs.docker.com/engine/extend/plugin_api](https://docs.docker.com/engine/extend/plugin_api/#what-plugins-are))，并对针对给定主机中的 docker 守护进程发出的命令做出反应。所以，现在的问题是:那个集装箱在哪里？我怎么能看到它？

要做到这一点，我们必须从 SSH 容器的“牢笼”中走出来。我们可以这样做，猜猜看，用一个容器:

using `nsenter1` to get into all the PID1 namespaces

上面的命令允许我们拥有一个被授予了所有权限的容器，共享主机的 PID 名称空间(这样我们就可以访问主机的进程)，然后使用映像`justincormack/nsenter1`，这实际上为我们提供了一种进入主机的 PID 1 的挂载名称空间的方法(这样我们就可以访问主机的所有文件)。

(参见[n 输入 1.c](https://github.com/justincormack/nsenter1/blob/01ad645aacbf23d8967f671d8ec6af3046ae5e6f/nsenter1.c) 了解`nsenter1`的功能)

一旦你在那里(在主机文件的整个视图中)，我们就可以访问`docker-runc`(见 [runc.io](https://runc.io/) )，它本质上是 docker 用来创建和运行 linux 容器的最底层的引擎。只是执行`docker-runc list`:

execution of `docker-runc list` shows all the runc linux containers running

这些包目录给了我们一个地方，在那里我们可以找到`runc`用来创建容器的`config.json`文件。每当我们想要将`exec`放入其中一个容器时，我们可以使用`docker-runc exec -t <cid> /my/binary`。有意思但还是没有。

retrieving the configuration of the container running the plugin

我们对 docker volume 插件运行的容器感兴趣，所以我们必须首先获得容器的`id`。为此，发出`docker plugin ls --notrunc`，这将在`ID`部分为我们提供完整的`id`集装箱。现在我们可以使用 ID 来访问目录，在那里我们可以找到运行插件的容器的配置。

配置文件(` config.json `)描述了运行 linux 容器所需的所有最基本的属性集(你可以在 https://github.com/opencontainers/runc 的[查看)。这很有趣，但并没有描述太多关于插件，因为这是太低的水平。](https://github.com/opencontainers/runc)

使用插件 API ( `docker plugin inspect <pluginID>`)我们可以找到一些有用的东西:有一些东西被挂载到`/mnt`，因为，[https://github.com/moby/moby/pull/26398](https://github.com/moby/moby/pull/26398)声明“卷插件挂载并希望它传播到主机名称空间，需要挂载在`/mnt`内。”，那么，我们来看看那个`/mnt`下面是什么:

inspection of the plugin configuration

`exec` into the plugin container and viewing the container filesystem

原来如此！我们创建的命名卷位于运行在我们主机上的插件容器中的`/mnt`目录下。因为他们都在 EFS 上安装了这个`/mnt`，所以我们在所有这些机器上共享存储。非常整洁的🕺🏻

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)