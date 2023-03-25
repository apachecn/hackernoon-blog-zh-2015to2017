# 正在为运行测试。Travis CI 上的 NET 和 Node.js

> 原文：<https://medium.com/hackernoon/running-tests-for-net-and-node-js-on-travis-ci-8704ea3a5512>

# 介绍

我和一个同事一起开始制作第一部分。像许多项目一样，这由一个做一些事情的 web api 和一个前端组成。

为了使事情变得简单，我们决定从为我们的 api 服务的同一个服务器上为我们的前端服务，并将它们保存在同一个 git 存储库中。我们选择在后端使用 ASP.NET 核心，在前端使用[反应](https://hackernoon.com/tagged/react)。

我们设置了项目并配置了 Travis CI 来运行我们的测试并发布到 Heroku。

最初我们只有。网络测试和特拉维斯运行他们没有问题。但是后来我们为前端引入了 Jest 测试。Travis CI 默认在上安装了 [Node.js](https://hackernoon.com/tagged/node-js) 。NET 环境所以事情应该进展顺利。

但是事情并不顺利。显然 Node.js 的默认版本是 4，我们的项目是用 Node.js 8 编写的。

是时候想办法解决这个问题了。

# 解决办法

我用一个. NET 核心测试项目和一个 npm 项目创建了一个 [repo](https://github.com/bartw/dotnetcore_node_travis) ，两个项目都有一个简单的测试。

[](https://github.com/bartw/dotnetcore_node_travis) [## bartw/dotnetcore_node_travis

### dotnetcore _ node _ travis——在一个 Travis 构建中同时运行 dotnet 和 node 的特定版本的测试

github.com](https://github.com/bartw/dotnetcore_node_travis) 

在 Travis CI 上，节点版本管理器( [nvm](https://github.com/creationix/nvm) )可用。在我的. travis.yml 文件中，我使用 nvm 安装 Node.js 8 并使用这个版本运行我们的测试。

现在双方。NET 测试和 Node.js 测试一起运行。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！