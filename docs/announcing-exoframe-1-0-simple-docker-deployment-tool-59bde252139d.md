# 宣布推出 Exoframe 1.0 —简单的 Docker 部署工具

> 原文：<https://medium.com/hackernoon/announcing-exoframe-1-0-simple-docker-deployment-tool-59bde252139d>

Exoframe 从一个目标开始——提供一个简单的自托管解决方案，允许使用 Docker 进行单命令部署。
大约 6 个月前 Exoframe 发布测试版后，我就一直在开发它。在那段时间里，Exoframe 一直用于我的个人项目，以及我们在[我们的研究小组](https://twitter.com/DiceResearch)开发的许多项目。

今天，我很兴奋地宣布 exo frame 1.0 版本的发布，这是该项目的重要一步。Exoframe 现在被认为是“生产就绪”。

# 1.0 中的新功能

随着时间的推移，Exoframe 获得了大量的特性，可以帮助您轻松地在服务器上管理和部署项目。
以下是 Exoframe 1.0 提供的开箱即用功能列表:

*   单一命令项目部署
*   基于 SSH 密钥的认证
*   滚动更新
*   部署令牌(例如，从配置项部署)
*   通过 letsencrypt *实现自动 HTTPS 设置
*   自动 gzip 压缩*
*   简单访问部署日志
*   docker-撰写支持
*   多个部署端点和多用户支持
*   客户端、服务器和 Traefik 的简单更新过程
*   可选的自动子域分配(即每个部署都有自己的子域)

*由 [Traefik](https://traefik.io/) 提供的功能

# 下一步是什么？

这次发布只是一个开始。我们计划在接下来的几个月里增加一些令人兴奋和令人敬畏的功能。我将在这里重点介绍其中的两个:

1.  我们计划增加 Docker Swarm 支持，允许你在多台服务器上部署和扩展你的项目。
2.  我们还将增加对部署方案的支持。想象一下在一个命令中部署大型复杂的东西(例如 Apache Spark stack)——这是我们的目标。

当然，如果你有任何建议，我很乐意倾听！

# 项目链接

你可以在 GitHub 上获得 [Exoframe，设置只需要几分钟。
你可以在 YouTube 上观看简短的](https://github.com/exoframejs/exoframe)[视频演示](https://www.youtube.com/watch?v=AEwLt5hmKYo)，它将在大约 8 分钟内带你完成设置和使用。
其余有用的链接和文档可以在 [GitHub 资源库](https://github.com/exoframejs/exoframe/tree/master/docs)中找到。

一如既往——感谢任何[反馈](https://twitter.com/yamalight)！