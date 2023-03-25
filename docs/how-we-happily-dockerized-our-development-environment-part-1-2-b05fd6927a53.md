# 我们如何愉快地将我们的开发环境分类(第 1/2 部分)

> 原文：<https://medium.com/hackernoon/how-we-happily-dockerized-our-development-environment-part-1-2-b05fd6927a53>

在这篇文章中，我将展示一些我们在[在第 2 部分](https://medium.com/u/4fb3c47e7f1c#.235fodt6h)中，我们将解释如何让这种配置发挥作用，这样你就再也不用等待 *npm 安装/gem 安装*了。

## 为什么我应该对我的工作流程进行任何更改？

通过正确设置 [docker](https://hackernoon.com/tagged/docker) 配置，我们现在了解以下特性:

*   **一键安装**工作就绪环境。从零开始。没错。

![](img/635e1cc7f2c14a8345c64d806fa44877.png)

*   **没有特定版本/依赖关系的本地安装**。作为一名自由职业者，你可能会参与几个项目，每个项目都使用不同版本的 node/mongo/elastic-search…等等。这根本不应该是一个问题。
*   **如果在本地有效，则在产品**中有效。CI 和生产服务器应该与我的开发环境完全一样。
*   本项目不需要基础设施方面的知识。前端开发人员、设计人员、集成商可以对基础设施有所了解。我们希望一个集成商能够在我们的项目上工作，而不必知道如何安装 mongodb 服务器。嗯，他/她根本不应该关心是否有数据库。

除了这些特性之外，我们还想要现在已经成为任何现代编码配置标准的特性:

*   **持续测试**:更改代码应该触发一系列测试，这对开发人员来说是直接可见的
*   **热重装:**你的代码的变化会很快出现在你的浏览器上，而不需要手动重启
*   **持续集成**被插入，每次提交都会在 CI-server 上触发一个完整的测试套件。

没错。所以我们将基本上摆脱

*   欢迎来到[。我们将改进我们的配置，以显著加快这一步。](https://medium.com/u/4fb3c47e7f1c#.235fodt6h)

    编码快乐！

    前往第 2/2 部分，了解如何将您的工作流程应用于类固醇！

    延伸阅读:[使用 typescript 构建 node-express api 的文档化开发环境](/@aherve/edge-node-express-api-using-docker-typescript-codeship-mongoose-37397cd07955#.gnqj3w7av)

    [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

    > [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
    > 
    > 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

    [![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)