# 部署 PHP 应用程序的 5 种方法

> 原文：<https://medium.com/hackernoon/5-ways-to-deploy-php-applications-81272ea11cab>

![](img/977fb12dfc96465d784e67118435b5f1.png)

*免责声明:* [*哥们。Works*](https://buddy.works/) *是一款帮助开发者自动化测试和部署他们应用的工具。在这篇文章中，我们想要比较 PHP 项目最常见的交付工作流——并试图找出哪一个是最好的。*

# PHP 交付概述

每个应用程序都各不相同，就像交付过程一样。PHP 交付最常见的步骤通常是这样的:

1.  测试应用程序
2.  下载依赖项([编写器](https://hackernoon.com/tagged/composer))
3.  编译资产(Gulp/[web pack](https://buddy.works/blog/webpack-vs-gulp)/[Grunt](https://buddy.works/blog/introducing-grunt)
4.  部署到服务器

当然，这里可以采取更多的行动。例如，您可能希望通过 SSH 迁移数据库或重启服务器——所有这一切都取决于项目规范和所使用的硬件。

# 两件重要的事情

在我们进一步讨论之前，每当使用“部署”这个词时，都应该强调两件事:

**一、一切保持版本控制！**

应用程序源代码、配置文件、文档、数据库迁移脚本—您应该在 repo 中妥善保存所有这些内容。当然，每条规则都有例外(尤其是在 IT 方面)，这就引出了第二条规则:

**二。永远不要在版本控制中保留依赖项和编译的应用程序！**

我们在这里解释了不应该这么做的原因。让我们来看看最让开发人员害怕的原因(除了被他们的家人当成一个 24/7 的 IT 服务人员之外)。

# 如果您想了解更多，请查看我们的指南:
[**好友指南——部署 PHP 应用程序的 5 种方法**](https://buddy.works/guides/5-ways-to-deploy-php-applications)

![](img/237fecb458d889482966108f09dae68f.png)