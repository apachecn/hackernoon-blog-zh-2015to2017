# 码头区的拉勒维尔

> 原文：<https://medium.com/hackernoon/laravel-in-docker-ceed4465352>

![](img/cc6dd25a732e93cfe9324c53cfba38a9.png)

通过这篇文章，您将学习如何准备一个 [Docker](https://hackernoon.com/tagged/docker) 的 [Laravel](https://hackernoon.com/tagged/laravel) 应用程序的图像，将它推送到注册中心，并在连续交付中使用 Docker。

# 介绍

## 拉勒韦尔

Laravel 用于构建您想要的任何类型的 web 应用程序。这个框架出奇的快，用户友好，并且有很好的文档记录。你可以从官方介绍中了解到，

Laravel 是一个 web 应用程序框架，具有丰富、优雅的语法。我们相信，发展必须是一种愉快的、创造性的体验，才能真正令人满意。Laravel 试图通过简化大多数 web 项目中使用的常见任务(如身份验证、路由、会话和缓存)来减轻开发的痛苦。

## 码头工人

另一方面，Docker 是一种虚拟化方法，当与其他开发人员合作开发代码时，它消除了“在我的机器上工作”的问题。由于 Docker 的虚拟化架构方法略有不同，它还:

*   比常规虚拟机速度更快，消耗的资源更少
*   更易于设置和修改
*   易于重用:您可以选择一个现有的 Docker 映像并安装任何缺失的库和包(类似于类继承)
*   可共享的:你可以把图片推送到 Docker 注册中心，然后把它们放到其他机器上，就像你从 Git 上推代码一样

# 归档 Laravel 应用程序

在本指南中，我们将使用一个用 Laravel 框架编写的简单计算器。确保从我们的 GitHub 配置文件中派生出[github.com/buddy-works/laravel-first-steps](https://github.com/buddy-works/laravel-first-steps)，并使用`php artisan serve`运行它。

> 请注意，我们使用“php 工匠服务”仅用于测试目的。对于生产，您应该使用 Apache 或 Nginx 的映像。

计算器应该在 Docker 容器中启动。我们需要的是一个 Docker 文件，其中包含将运行 Docker 容器的 Docker 映像的描述。觉得很复杂？不要担心，我们将带您了解整个过程，并详细描述每件事情。

该指南的完整版本可在我们的网站上免费获得: [**Laravel in Docker。**](https://buddy.works/guides/laravel-in-docker?utm_source=medium&utm_medium=post&utm_campaign=laravel-in-docker&utm_content=link)

![](img/237fecb458d889482966108f09dae68f.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)