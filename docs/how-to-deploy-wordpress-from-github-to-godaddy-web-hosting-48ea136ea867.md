# 如何将 WordPress 从 GitHub 部署到 GoDaddy 虚拟主机

> 原文：<https://medium.com/hackernoon/how-to-deploy-wordpress-from-github-to-godaddy-web-hosting-48ea136ea867>

![](img/586182f598ade95df279c8897c8b821a.png)

在这篇文章中，我们将向你展示如何以最简单的方式将 [WordPress](https://hackernoon.com/tagged/wordpress) 自动部署到由 [GoDaddy](https://hackernoon.com/tagged/godaddy) 管理的服务器上。

# 部署的痛苦

如今更新网站是一个挑战:在上传到服务器之前，你可能需要用 Gulp 或 Grunt 运行一些任务，用 Webpack 捆绑资产，更新 S3 上的媒体，等等。哦，不要忘记在部署后刷新缓存！

你可以用像 Jenkins 这样的 DevOps 工具做上面所有的事情，但是对于一个普通的开发人员来说，花几个小时在配置上似乎是多余的。然而，没有多少开发人员知道，你可以用一个叫做 Buddy 的简单工具将所有这些东西简化成一个*单个*推送至你的 Git 库。

![](img/f55d13d1e725f6c2eb1a6f0b22449252.png)

# 简化的自动化

Buddy 可让您一键回购、点击或按时间间隔自动部署您的网站。你可以在任何地方部署:FTP、SFTP、DigitalOcean、AWS、Google 等等。在本例中，我们将使用 SFTP，这是一种安全类型的协议，我们总是建议在常规 FTP 上使用。

简而言之，Buddy 使用交付管道来部署您的网站。配置速度非常快，感觉就像用砖块盖房子一样:您需要做的只是选择一个动作，配置访问细节，并将其排列成行。

# 步骤 1:配置管道

1.[使用您的 GitHub 帐户注册好友](https://buddy.works/sign-up),并使用您的网站资源选择存储库:

![](img/61b89954ea6a35770e9738269557ac1b.png)

2.创建一条**新管道**，在 push 时将触发模式切换到**，选择管道将被分配到的**分支**。例如，如果您将管道分配给主分支，它将在每次推送到主分支时自动执行。**

![](img/26c01ad3fbcecd35ada2acb52d237b11.png)

3.现在是配置交付的时候了。选择 **SFTP** 操作，并输入 GoDaddy 实例的详细信息(主机名、用户名和密码)

例如，您还可以选择 Buddy 将部署文件的远程路径

```
~/html/wp-content/themes/my-theme
```

> 你可以在你的 [GoDaddy 账户](https://gateway.godaddy.com/)中找到凭证:点击托管 WordPress 服务器的设置按钮，切换到 SSH & SFTP 标签。

![](img/21f4ad89aee8abda64be102553883b2f.png)

4.最后一部分是在部署后刷新缓存。选择 **SSH** 操作，并填写您在步骤 3 中使用的相同连接凭证。在命令字段中，输入:

```
wp godaddy cache flush
```

如果愿意，您可以添加更多命令，每个命令占一行:

```
wp godaddy cache flush
wp theme update --all
wp core update
```

![](img/78f3a085beb260229dce606239587331.png)

5.一切就绪后，单击 **Add new pipeline** 保存更改。

# 步骤 2:运行管道

你需要做的就是推一推指定的分支，看 Buddy 自动更新你的网站:

# 第三步:拓展渠道

上面的管道可以通过额外的操作轻松扩展。您可以在部署前添加跑步/咕噜，更新您的 S3 桶中的资产，向您的 Slack 频道发送通知，等等。

![](img/f12b209cc4464ef20f6e566e4ad93b86.png)

您还可以设置监控功能，检查您网站的停机时间，并让您的手机知道:

![](img/7232ebd5bcc31faab269d03c13641034.png)

突然间，DevOps 的事看起来没那么糟了，是吗？:)

# 立即自动化！

如果你想了解更多关于处理 WordPress 部署的信息，你可以查看我们的[指南部分](https://buddy.works/guides)，或者通过网站上的实时聊天。这项服务有一个 14 天的试用期，其中一个项目总是免费的，这使得它非常适合更新你的副业项目。

感谢阅读！