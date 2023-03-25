# 带有谷歌云功能的私人 NPM 包

> 原文：<https://medium.com/hackernoon/private-npm-packages-with-google-cloud-functions-4cdfb13c37cc>

访问 https://www.mikenikles.com 的[](https://www.mikenikles.com)****获取我的最新博文。****

***TL；dr:现在正式支持*[](https://cloud.google.com/functions/docs/writing/dependencies#using_private_modules)**🎉！****

***作为 AWS 向 GCP 迁移的一部分，我们希望利用谷歌云功能([https://cloud.google.com/functions](https://cloud.google.com/functions/))来帮助我们以更易于管理的方式扩展我们的基础设施。***

> ***云函数用 JavaScript 编写，在标准的 [Node.js](https://nodejs.org/) 运行时环境中执行。***

***多棒啊。—所以我们认为。我们没有迁移我们的 AWS 整体，而是决定提取孤立的部分，并将它们作为独立的服务部署，一次一个部分。我们现有的应用程序是用 Node.js 编写的，迁移到 GCF (Google Cloud Functions)听起来很简单。如果不是我们的私人 NPM 套餐…***

***为了清楚起见，如果你只依赖公共的 npm 包，GCF 总是工作得很好。***

# ***私人 NPM 套餐——问题***

***为了让我们的团队共享公共代码，我们使用[私有 npm 包](https://docs.npmjs.com/private-modules/intro)。GCF 不喜欢这样……有一个关于解决方法的 StackOverflow 讨论。如果您的代码具有私有 npm 包的顶级依赖项，它会工作得很好。如果像在我们的例子中，你有短暂的依赖，也是私有的 npm 包，你要么在你的脚本上发挥真正的创造力，要么寻找替代方案。在我们的案例中，我们部署了 Google App Engine，这是一个临时的解决方案，因为它会很快变得非常昂贵…***

# ***私人 NPM 套餐——解决方案***

***昨天，我偶然在 GCF 文档上找到了一个新的章节“**使用私有模块**”。等等什么？怎么了？***

> ***为了使用[私有 npm 模块](https://docs.npmjs.com/private-modules/intro)，您必须在位于函数目录的`.npmrc`文件中提供 npm 注册表的凭证(auth token)。您可以简单地复制使用`npm login`命令登录 npm 时在您的主目录中创建的`.npmrc`文件。***

*****这是整个章节，它是一个游戏改变者！*****

***如果这让你决定迁移到 GCF 更简单，请在评论中告诉我。有问题吗？尽管问吧，我很乐意尽我所能帮忙。***