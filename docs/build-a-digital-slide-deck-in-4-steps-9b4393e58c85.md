# 用 4 个步骤构建数字幻灯片

> 原文：<https://medium.com/hackernoon/build-a-digital-slide-deck-in-4-steps-9b4393e58c85>

![](img/bd07d05ae6543ea7654ef57cf6f6e1de.png)

任何公司或组织的表示层对于成功都至关重要。幕后的工作通常不会以执行的方式报告，让知识专家看起来缺乏光彩，让销售团队看起来毫无准备。克服这一障碍的一个现成资源是格式化一个数字幻灯片，以便向客户演示，无论您是与他们在房间里，还是在介绍性会议后提交一个链接。

在这篇博客中，我将向你展示如何利用现有的代码库例子来构建你自己的数字幻灯片演示文稿 [。](https://cosmicjs.com/apps/landing-page) Slide Deck 构建在 [Impress.js](https://github.com/impress/impress.js) 演示框架之上，运行在 light Node.js 服务器上。安装后，您可以在您的 Cosmic JS bucket dashboard 中配置所有布局、内容和动画。

![](img/0b278e1731cfc628c8760e62a756d4e4.png)

对于这个示例应用程序，我将使用 [Cosmic JS](https://cosmicjs.com/) 。Cosmic JS 是一个 [API 优先的 CMS](https://cosmicjs.com/) ，它使得管理和构建网站和应用程序更加快速和直观。通过将内容从代码中分离出来，Cosmic JS 增强了开发人员的灵活性，同时确保内容编辑人员能够以最适合他们的方式规划和部署内容。我们将使用 Cosmic JS 来安装我们的示例应用程序，部署和更新来自基于云的内容管理平台的内容。

![](img/14cd1274d4a82188772517fbd07e042d.png)

如果你还没有，那就从[报名](https://cosmicjs.com/signup)参加[宇宙 JS](https://cosmicjs.com/) 开始吧。下面提供了有用的资源来简化您的开发操作。

> [幻灯片应用页面](https://cosmicjs.com/apps/slide-deck)
> 
> [幻灯片应用演示](https://cosmicjs.com/apps/slide-deck/demo)
> 
> [GitHub 上的幻灯片应用代码库](https://github.com/cosmicjs/cosmicapp-slide-deck)

# 1.创建新的存储桶

![](img/7fb7a8b52745543204b8e29c421e0a67.png)

您的 bucket 的名称是您正在构建的网站、项目、客户端或 web 应用程序的名称。我将我的命名为“数字幻灯片”，以保持示例博客的简洁。

# 2.安装幻灯片应用程序

![](img/f4f97daf80e8bcce1eb1b77b1e62f660.png)

一旦你注册并命名了你的桶，你将被提示从头开始或者“查看一些应用”。对于这个博客，我简单地点击了右键“查看一些应用程序”，这样我就可以开始安装 [Cosmic JS 数字幻灯片](https://cosmicjs.com/apps/slide-deck)了。

# 应用程序安装选项

![](img/7967a79f0fbd6e22b625be0e4e76ec53.png)

[Cosmic JS](https://cosmicjs.com/) 让你能够在编程语言之间进行过滤，比如 [Node.js](https://cosmicjs.com/apps) 、 [PHP](https://cosmicjs.com/apps) 、 [React](https://cosmicjs.com/apps) 、 [AngularJS](https://cosmicjs.com/apps) 等等。

![](img/39b0b14eab3df38bdea58289e2a03382.png)

只需点击[幻灯片组](https://cosmicjs.com/apps/slide-deck)图标下方的“安装”即可开始，或者访问[宇宙 JS 应用页面](https://cosmicjs.com/apps)。

![](img/cf3f8381821abd52fd98ab573429f9b9.png)

# 3.部署到 Web

![](img/d34f79135fb870de255d70a296cf5925.png)

我点击了“部署到 Web”。然后，我可以在部署 web 应用程序时编辑对象。您将收到一封电子邮件，确认您的 web 应用程序的部署。如果您在部署过程中遇到任何问题，您可能会被转到 [Cosmic JS 故障排除页面](https://cosmicjs.com/troubleshooting)。

# 确认部署位置和分支

![](img/382ac9fb98c99008e23b729ecbbb535f.png)

部署分支机构确认模式

![](img/70f6d2a853c28cb59375f9a0941eed75.png)

我从一个回购中提取，我的分支被澄清，我有一个与我在步骤 1 中创建的 slug / bucket 名称相匹配的部署位置。

# 展开，展开

![](img/842d02171e83f92e6a28959582e798ff.png)

一个小小的保证，让你很快就能在网上活得好好的。:)

# 4.编辑全局对象

![](img/3d029b7dc1a5c81ecf5fd2ab65de0f14.png)

幻灯片组中设置的对象被列为步骤，并分别授予编辑每个幻灯片动作的副本、动画和内容的权限。编辑是在宇宙 JS 仪表盘中实现的梦想。要了解更多关于如何在考虑编辑内容的情况下构建 [Cosmic JS](https://cosmicjs.com/) 的信息，请阅读[在考虑内容编辑器的情况下构建](https://cosmicjs.com/blog/building-with-the-content-editor-in-mind)。

# 应用部署确认

![](img/68b4e292a7da3e20aaa5c36ee6d40424.png)

就像注册、创建新的存储桶、安装 web 应用程序、编辑对象和部署一样简单！我收到了访问我的 web 应用程序的确认电子邮件，还看到了我的桶升级选项，如自定义域、一键式 SSL、webhooks 和本地化。

[Cosmic JS](https://cosmicjs.com/) 是一个 API 首创的基于云的内容管理平台，可以轻松管理应用和内容。如果你对 Cosmic JS API 有任何疑问，请通过 [Twitter](https://twitter.com/cosmic_js) 或 [Slack](https://cosmicjs.com/community) 联系创始人。

> 卡森·吉本斯是 [Cosmic JS](https://cosmicjs.com) 的联合创始人& CMO，这是一个 API 第一的基于云的[内容管理平台](https://cosmicjs.com)，它将内容与代码分离，允许开发人员用他们想要的任何编程语言构建流畅的应用程序和网站。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)