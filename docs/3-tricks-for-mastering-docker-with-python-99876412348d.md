# 用 Python 掌握 Docker 的 3 个技巧

> 原文：<https://medium.com/hackernoon/3-tricks-for-mastering-docker-with-python-99876412348d>

这个故事的灵感来自于我用 100% Python 编写 Docker 式容器守护进程的徒劳尝试。

# 技巧 Python 的 Docker

`pip install docker`

使用 Docker CLI 很好，但是使用 Python 的 API 要好得多。您可以列出容器，查看属性，并使用其他 3 个技巧，利用它们的强大功能。

# 技巧 Python 的名称空间

`pip install nsenter`

感谢 Zalando 的人们，这是 C API 进入内核名称空间的包装器。有了 docker，Docker 运行容器就有了自己的名称空间。这是保护它不受主机上其他容器影响的原因，使它保持隔离。[更多细节请点击](https://tech.zalando.com/posts/entering-kernel-namespaces-with-python.html)查看他们的博客文章。

使用 nsenter 很简单，假设您使用`docker run -d nginx`启动了一个新的 nginx 容器，并获得了一个容器 ID 277906 BC 266 c，您可以使用这个命令获得正在运行的进程 ID。

使用技巧 1 中的例子来获取进程 ID。使用该进程 ID 跳转到命名空间并运行命令，查看文件系统或者只是进行一般性破坏。

*为什么是*？嗯，您经常会发现自己在一个 bash 会话中跳转到一个正在运行的容器来调试代码中的一些 bug(我？绝不！)，嗯这叫 Bash 是有原因的，既然可以用[声波螺丝刀](https://en.wikipedia.org/wiki/Sonic_screwdriver)为什么还要用锤子呢？

# 绝招三:`cgroups`针对 Python

`pip install cgroups`

控制组是 Docker 和其他工具确保特定进程不会失控并耗尽主机上所有资源的方式。你可以使用`cgroups`包既创建、列出又操纵主机上现有的 cgroups。例如，默认情况下，容器上没有设置 CPU 限制，所以您可以使用 Python 在主机上动态地转移资源。这个例子启动了一个新的进程并将它移动到一个 cgroup 中，你可以对 Docker 已经启动的现有进程做类似的事情。

现在就这些了，如果你想要一些使用这些包的可靠例子，请查看我在 GitHub 上的 Docker 守护进程实现[https://github.com/tonybaloney/mocker](https://github.com/tonybaloney/mocker)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！