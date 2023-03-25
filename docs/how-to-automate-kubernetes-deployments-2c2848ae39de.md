# 如何自动化 Kubernetes 部署

> 原文：<https://medium.com/hackernoon/how-to-automate-kubernetes-deployments-2c2848ae39de>

![](img/797edd62e5c2fa19c6031211980b8466.png)

Kubernetes 让您在一个节点集群上管理容器化的应用程序。如果您需要在不消耗更多资源的情况下动态部署和扩展您的应用程序，这就是您的解决方案。

Buddy 让你[自动化](https://hackernoon.com/tagged/automate)整个交付过程，一个 git 推送到你的回购。这些是好处:

*   您不必每次都手动输入命令
*   万一出现错误，你会得到准确的反馈。yml 文件
*   您不需要 DevOps 专家来更新您的应用程序
*   在部署之前，您可以通过构建和测试来扩展您的交付管道

这篇文章的主要目的是告诉你如何通过一系列简单的步骤做到这一点。

# 与巴迪一起建立一个项目

1.  [**用 GitHub/Bitbucket 或 email 注册好友**](https://buddy.works/sign-up)
2.  创建一个新项目，选择您的 Git 提供者，然后选择您的 WP 项目:

# 配置管道#1:应用 K8s 部署

第一个管道会自动将配置更改应用到您的 K8s 卷和推送到分支的 WordPress 实例:

1.  点击**添加新管道**并将触发模式设置为“每次推送时”
2.  选择将触发执行的分支
3.  点击**添加新管道**

# 喜欢你读的吗？[点击此处查看完整指南](https://buddy.works/guides/how-automate-kubernetes-delivery)！

![](img/237fecb458d889482966108f09dae68f.png)