# 如果你是 Rails 开发人员，并且你的客户要求使用 WordPress，请阅读这篇文章

> 原文：<https://medium.com/hackernoon/read-this-if-youre-a-rails-developer-and-your-client-asks-for-wordpress-4511c38c4896>

![](img/cf0672ee7aaa7081927f632fb324dd24.png)

假设您已经为一个客户端构建了一个 Rails 应用程序。在推出他们的 MVP 几个月后，你的客户想要加强他们的营销，他们要求你在他们的网站上添加一个博客。“如果是 WordPress 就好了”，他们告诉你。"哦，我们的搜索引擎优化顾问告诉我们，它应该生活在我们的主要领域，而不是一个子域."

一想到 [WordPress](https://hackernoon.com/tagged/wordpress) 你就不寒而栗，但是你开始悄悄地研究你的选择。

将 WordPress 集成到你的 [Rails](https://hackernoon.com/tagged/rails) 应用程序中是不可能的，但是如果你单独运行 WordPress(或者类似的博客解决方案，比如 Ghost ),它将需要托管并且必须存在于一个单独的子域中，这将会让你热衷于 SEO 的客户大失所望。

您探索了设置反向代理的解决方法，但是看起来非常不愉快。即使经历了所有这些麻烦，你仍然需要弄清楚如何定制模板，以使设计符合你漂亮的 Rails 网站——你想知道它们是否可以共享相同的布局和 CSS…

决心找到可以集成到你的 Rails 应用中的东西，你在 Github 中搜寻开源博客引擎。你从一个有希望的仓库跳到下一个。你找到的第一个项目看起来不错但是三年没更新了。另一种方法需要向已经负担过重的数据库中添加大量臃肿的数据库表。你的希望开始消退…

如果这种情况对你来说很熟悉，你并不孤单。

我们遇到过无数次这样的问题，客户想要在我们已经创建的项目中添加一个博客。

我们想要一个博客系统:

*   支持图片上传、标签等
*   拥有所见即所得编辑器
*   使用现有的设计和布局
*   清晰可读的代码
*   不超过一天的工作来设置

我们最近遇到了 [ButterCMS](https://buttercms.com/) ，一个基于 API 的 [Rails 博客引擎](https://buttercms.com/rails-blog-engine/)和带有 [gem](https://github.com/buttercms/buttercms-rails) 的 [Rails CMS](https://buttercms.com/rails-cms/) 。Butter 只花了几个小时就设置好了，并为我们的客户提供了友好的用户界面。因为这是一项托管服务，所以不需要维护，产品会不断改进，这是我们的客户所喜欢的。

下面是我制作的一个一分钟的截屏，演示 Butter 如何轻松地集成到现有的 Rails 应用程序中:

Build a Rails blog with ButterCMS

# 包裹

下次你的客户要求写博客时，使用像 ButterCMS 这样的 Rails 博客引擎来完全避免 Wordpress。通过[登录 Github](https://buttercms.com/github/oauth) 免费试用 ButterCMS。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)