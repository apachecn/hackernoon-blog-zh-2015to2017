# github 页面和服务人员上的 Jekyll。渐进式网络应用程序和博客的离线模式

> 原文：<https://medium.com/hackernoon/jekyll-on-github-pages-and-service-workers-progressive-web-apps-and-offline-mode-for-your-blog-b1cf9e87a6d1>

我上周参加了 2016 年 chrome dev 峰会，谷歌一直在非常努力地推动进步的网络应用。它们基本上是一种新的方式，通过使网站更加可靠、快速和吸引人，向网络用户提供令人惊奇的体验；通过添加[离线体验](https://developers.google.com/web/fundamentals/instant-and-offline/offline-cookbook/)，定期后台同步和推送通知。

其中一个关键的方面是**服务人员**。服务工作者是一个独立于页面运行的脚本，它可以拦截网络请求，写入缓存，它不能操纵 DOM，但可以使用 post 消息来获取和发送数据，并且它们大量使用承诺。很多东西都超级酷，你绝对应该去看看，但我不会去太多，因为已经有比我能制作的更好的内容了。

![](img/cc5e46fed5367b15889309acb6d59b50.png)

# 入门指南

在峰会期间，我把我的一个网站变成了一个进步的网络应用，[https://techqueria.org/](https://techqueria.org/)。(在 safari 或 iOs 中不起作用)。在你的网络选项卡上看看它换页的速度，以及即使你打开了飞行模式它是如何工作的。

服务人员有一些重要的必备条件:

1.  你必须用 HTTPS。
2.  该文件必须位于应用程序的根目录下。

幸运的是，使用 github 页面很容易完成这两件事！

**HTTPS**

如果您没有使用自定义域，您的页面已经使用 HTTPS 服务。如果你想使用自定义域，试试 [Cloudflare](https://www.cloudflare.com/) ，他们提供免费 https 附带的 DNS 服务。您将您的 NS 指向 Cloudflare，它们将自动加载您当前的 DNS 设置。要激活简单 SSL，您只需切换选项[https://www.cloudflare.com/ssl/](https://www.cloudflare.com/ssl/)

如果你使用的不是 github 页面，而是数字海洋之类的东西，那么使用 [LetsEncrypt](https://letsencrypt.org/) ，他们提供免费的 SSL 证书。

**SW.js**

我在创建应用程序时遇到的最大问题是我没有找到文件的位置。服务工作者将有一个范围，它不能是在其位置之上的目录，它必须在根目录中。我花了一段时间来调试，直到 Mozilla 的一些友好的人发现了我的错误。您可以简单地将 sw 文件放在 jekyll 项目的根目录下，访问者就可以使用它了。
这个错误很难发现，因为我们看到服务工作者是活跃的，但没有请求被拦截，离线模式不工作。

你可以看看 github 的所有代码，你基本上可以复制我的 sw.js，只需替换你的文件的路径，它应该可以工作。

[](http://github.com/techqueria/site) [## 技术查询/网站

### 技术社区中拉丁人的网站

github.com](http://github.com/techqueria/site) 

我们还包含了一个 manifest.json，因此在 Android 中，它将向用户显示一个在主页中显示的选项，这增加了一个漂亮的图标和类似应用程序的体验。

除此之外，我遵循的步骤与任何教程中的步骤都是一样的。
如果你想了解更多信息，我推荐以下几款:

*   [现场编写一个渐进式网络应用](https://www.youtube.com/watch?v=X8EQSy-ajo4):在这个简短的视频中，你可以看到苏尔马和保罗在休息时间演示如何向网站添加一名服务人员。
*   您的第一个渐进式 web 应用程序:是一个使用示例应用程序来介绍概念的介绍。
*   [离线 we 应用课程](https://www.udacity.com/course/offline-web-applications--ud899):是一个 Udacity 类，它更深入地探讨了离线 web 应用的可能性，比如在 IndexedDB 中缓存用户数据。
*   [未来的渐进式 web 应用](https://www.youtube.com/watch?v=J2dOTKBoTL4&t=600s) : Jake(服务工作者的发起人之一)分享了 Web 应用模型的未来，包括 Foreign Fetch、Streams 等等。