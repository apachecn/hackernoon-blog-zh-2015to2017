# 使用多阶段构建和 Zeit pkg 将 nodejs Docker 图像大小减少 50%。

> 原文：<https://medium.com/hackernoon/reducing-nodejs-docker-images-size-by-50-using-multi-sage-builds-and-zeit-pkg-360ab8b6c6d2>

*更新:你可能想试试我为这篇博文写的互动教程:*[http://training.play-with-docker.com/node-zeit-pkg/](http://training.play-with-docker.com/node-zeit-pkg/)

一周前 [Zeit](https://hackernoon.com/tagged/zeit) 宣布了一个名为 pkg([https://github.com/zeit/pkg)](https://github.com/zeit/pkg)的新项目，该项目允许将 [nodejs](https://hackernoon.com/tagged/nodejs) 应用程序及其所有依赖项(甚至是节点运行时)捆绑在一个动态链接的二进制文件中，该文件可以很容易地发布到不同的环境中。这非常有用，因为它只处理一个二进制文件，而不是处理不同的依赖项和节点运行时，从而使应用程序的打包、部署和运行变得非常简单。

如果你目前在你的项目中使用 nodejs + docker，很可能你依赖于任何不同的官方 nodejs([https://hub.docker.com/_/node/](https://hub.docker.com/_/node/))图像。正如您可能已经知道的，这些图像通常不是轻量级的，因为它们需要`node`运行时，并且还包括其他工具，如`npm`，这导致从`slim`替代品开始的 200 MB 以上的图像。

与此同时，docker 宣布了一项名为多阶段构建的新功能(https://blog . Docker . com/2017/04/Docker con-2017-day-1-highlights/)，该功能有助于构建通常具有不同`build`和`prod`上下文的 Docker 映像。

> 目前，多阶段构建功能仅在最新的 Docker 17.05-rc 中可用。在尝试下面的例子之前，确保有一个正确的 Docker 版本，否则你会得到一些错误。

现在，我将向您展示如何结合这两种特性来构建和发布极小且经过优化的 nodejs docker 映像。

首先，我们将创建一个小型 nodejs 项目，让我们从一个简单的 **index.js** 和 **package.json** 开始

index.js:

```
console.log('Hello, pkg!');
```

package.json:

```
{
    "name": "pkg-sample",
    "bin": "index.js"
}
```

我们现在将创建使用 pkg 和多阶段构建的 docker 文件:

Dockerfile:

```
FROM node:boronRUN npm install -g pkg pkg-fetchENV NODE node6
ENV PLATFORM linux
ENV ARCH x64RUN /usr/local/bin/pkg-fetch ${NODE} ${PLATFORM} ${ARCH}COPY . /appWORKDIR /appRUN /usr/local/bin/pkg --targets ${NODE}-${PLATFORM}-${ARCH} index.jsFROM debian:jessie-slimCOPY --from=0  /app/index /CMD ["/index"]
```

正如你在 docker 文件中看到的，在这个特殊的例子中，我们的目标是一个**节点 6、linux 和 x86_64** 运行时。根据您需要的运行时，您可以组合几个选项，例如，如果您愿意，您可以只使用标准的 alpine 平台而不是 linux。有关更多详细信息，请参考 pkg 文档。

现在，您可以像以前一样使用`docker build -t myapp .`构建您的应用程序映像。一旦你的映像建立起来，你会注意到它的大小只有几兆，你可以像其他 docker 容器一样用`docker run myapp`运行它

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！