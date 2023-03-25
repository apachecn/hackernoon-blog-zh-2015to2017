# 缩小 Docker 图像尺寸的技巧

> 原文：<https://medium.com/hackernoon/tips-to-reduce-docker-image-sizes-876095da3b34>

Docker 图像可以轻松达到 2–3GB。这里有一些小贴士可以帮助你缩小它们的尺寸。

# 技巧 1——使用较小的基本图像

```
FROM ubuntu
```

一开始会将您设置为 128MB。考虑使用较小的基础图像。对于您在`Dockerfile`中添加的每一个`apt-get install`或`yum install`行，您将按照库的大小增加图像的大小。意识到你可能不需要你正在安装的那些库。

考虑使用一个 [alpine](https://hub.docker.com/_/alpine/) base image(大小只有 5MB)。最有可能的是，您正在使用的编程语言有 alpine 标签。比如 Python 有`2.7-alpine` (~50MB)和`3.5-alpine` (~65MB)。

# 技巧 2——不要安装像 vim/curl 这样的调试工具

我注意到许多开发人员在他们的`Dockerfile`中安装了`vim`和`curl`，这样他们可以更容易地调试他们的应用程序。除非您的应用程序依赖它，否则不要安装这些依赖项。这样做违背了使用小型基础映像的目的。

但是我如何调试呢？

一种技术是拥有一个开发文档和一个生产文档。在开发过程中，准备好您需要的所有工具，然后在部署到生产环境时，删除开发工具。

# 提示#3 —最小化图层

`Dockerfile`的每一行都是构建过程中的一个步骤；占据尺寸的层。合并您的 RUN 语句以减小图像大小。代替

```
FROM debian
RUN apt-get install -y <packageA>
RUN apt-get install -y <packageB>
```

做

```
FROM debian
RUN apt-get install -y <packageA> <packageB>
```

这种方法的一个缺点是，每次添加新库时，您都必须重新构建整个映像。如果你不知道，Docker 不会重建它已经建立的层，它会逐行缓存`Dockerfile`尝试改变你已经建立的`Dockerfile`的一个字符，然后重建。您会注意到，该行上面的每一步都被认为是已经构建好的，但是您更改的行(以及后面的每一行)都将被重新构建。

我推荐的一个策略是，在开发和测试依赖项时，分离出`RUN`命令。一旦准备好部署到生产环境，就将`RUN`语句组合成一行。

# 技巧 4 使用`— no-install-recommends on apt-get install`

将 `— no-install-recommends`添加到`apt-get install -y`中可以避免安装技术上不依赖但建议与软件包一起安装的软件包，从而有助于显著减小大小。

`apk add`命令应增加`--no-cache`。

# 技巧 5 将 RM-RF/**var**/lib/apt/lists*/*添加到与 apt-get 安装*相同的层

在`apt-get -y install`后添加`rm -rf /**var**/lib/apt/lists/*` 来清理安装包。

对于`yum`，增加`yum clean all`

同样，如果你安装`wget`或`curl`是为了下载一些包，记得把它们都放在一个`RUN`语句中。然后在 run 语句的末尾，一旦你不再需要它们就选择`apt-get remove curl`或`wget`。这个建议适用于任何你只是暂时需要的包。

# 技巧 6 使用 FromLatest.io

[FromLatest](https://www.fromlatest.io/#/) 将 Lint 您的 docker 文件，并检查您可以执行的更多步骤来减小您的图像大小。

还有许多其他的提示。请随意分享我可能错过的。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！