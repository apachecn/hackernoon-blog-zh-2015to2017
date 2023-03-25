# 救火基地:通过托管+功能集成实现动态路由

> 原文：<https://medium.com/hackernoon/firebase-to-the-rescue-dynamic-routing-via-hosting-functions-integration-aef888ddf311>

![](img/5e235ae868d5536c2014ab7a1ead59a1.png)

这篇文章有点像回忆录，我在其中分享了让动态路由与 Firebase 一起工作的经验。要了解这是怎么回事，你应该熟悉他们的[主机](https://firebase.google.com/docs/hosting/)和[功能](https://firebase.google.com/docs/functions/)产品。我已经将所有重要的技术内容浓缩到了迷你教程中。如果你愿意阅读整篇文章，可以跳过这一部分。

# TL；迷你博士教程

Firebase 托管服务来自您的自定义域的静态文件。Firebase 函数是通过难看的`cloudfunctions.net`URL 调用它们来执行的。截至谷歌 IO 2017，他们现在合作得很好。在您的`firebase.json`的`hosting`配置中，您可以`rewrite`自定义域上的任何特定路由来调用一个`function`而不是服务一个静态页面。查看文件。

为了模拟像`example.com/posts/123`这样的动态路线，我做了以下工作:创建一个名为`[post](https://gist.github.com/pejalo/1715c896658d660e0ded0d49d9910896)`的函数。设置`firebase.json`让所有对`/posts/**`的请求调用`post`函数。该函数获取数据并返回预呈现的 HTML，其中包含所请求帖子的所有适当的社交和 SEO 元标签，以使爬虫感到满意并支持[展开](/slack-developer-blog/everything-you-ever-wanted-to-know-about-unfurling-but-were-afraid-to-ask-or-how-to-make-your-e64b4bb9254)。

该页面还包括一个 [JavaScript](https://hackernoon.com/tagged/javascript) `window.location`重定向片段，因此一个真实的用户被重定向到`example.com/?post=123`。这个静态索引页面读取查询参数，显示包含文章内容的正确屏幕，并利用 HTML5 的`replaceState()`将`example.com/posts/123`放回浏览器的地址栏。面向所有人的无服务器动态路由！

# ❤燃烧基地

Firebase 单枪匹马地重新点燃了我与 web 开发的关系。我现在只需要担心前端，而不是为每个项目构建前端和后端。尽管 Firebase 的产品范围很广，但它仍然缺乏许多受欢迎的[功能](https://medium.freecodecamp.com/firebase-the-great-the-meh-and-the-ugly-a07252fbcf15)(如[搜索](/google-cloud/firebase-search-a-bigger-boat-c85695546d02)，复杂数据库[查询](https://stackoverflow.com/questions/26700924/query-based-on-multiple-where-clauses-in-firebase)，洞察[使用](https://startupsventurecapital.com/firebase-costs-increased-by-7-000-81dc0a27271d)等)。但是我在这一点上太投入了，以至于我宁愿跳过几个圈来让 Firebase 工作，而不是回去自己建立一个基础后端，就好像我们仍然生活在前[解析](http://parseplatform.org/)时代一样。(没错，我们开发者就是懒。)

几周前，我在做一个产品搜索克隆，它有一个非常简单的需求:动态路由。像`example.com/posts/123`一样，每篇文章都需要自己的网址在网络上分享。这些链接必须返回预先渲染的社会和搜索引擎优化元标签，以支持[展开](/slack-developer-blog/everything-you-ever-wanted-to-know-about-unfurling-but-were-afraid-to-ask-or-how-to-make-your-e64b4bb9254)。

到目前为止，我在 Firebase 上愉快地托管了所有东西，作为一个臭名昭著的*单页应用*，从客户端直接连接到数据库。但是有了这个新的预渲染需求，Firebase 还不支持，看起来好像我必须将我们的域指向一个定制的 VPS 并自己托管站点。

所以我花了一个周末的时间来学习关于 [Dokku](http://dokku.viewdocs.io/dokku/) (顺便说一下，这是一个很好的选择)，让我们的网站启动并运行，做一些负载测试，观察网站崩溃，搜索互联网试图找出原因，说服自己这个应用程序有内存泄漏，然后意识到我只需要一台内存更大的机器，bla bla bla 一直希望我可以回到构建功能上来，停止处理这些开发运营的废话。

几天后，Firebase Slack 团队中的某个人发布了一个 [Google IO](https://events.google.com/io/) 直播。我调进来只是为了好玩，就在那里，我需要的确切功能被宣布:[主机+功能！](https://firebase.google.com/docs/hosting/functions)有了这种新的集成，开发人员可以在向客户端发送 HTTP 响应之前，在自定义域后面运行一些后端代码。几个小时后，我就有了生产中的动态路由，并可以将 Dokku 放回货架上。

# 托管+功能

Firebase 主机现在有了一个新的配置选项。在`firebase.json`中，你可以指定所有的[托管行为](https://firebase.google.com/docs/hosting/url-redirects-rewrites)，比如缓存、重写、重定向等等，你可以`rewrite`任何指向函数而不是静态文件的路径。这里有一个例子:

```
// firebase.json{
  "hosting": {
    "public": "public",
    "rewrites": [
      { "source": "/posts/**", "function": "post" }
    ]
  }
}
```

有了这个配置，任何对`example.com/posts/postidorhashorwhatever`的请求都将忽略你可能已经托管在那里的静态文件，而是调用你的`post`函数。使用 Express [Request](https://expressjs.com/en/api.html#req) 和 [Response](https://expressjs.com/en/api.html#res) 对象调用该函数，通过这些对象可以发送任意 HTTP 响应。

这让我们可以在返回客户端之前执行任何我们想要的逻辑！(只要它运行在 Firebase 的云功能节点环境中……)很好，您可能会想:我们可以返回任何内容，但是当网站被构建为静态页面时，我们如何返回显示适当帖子内容的整个网站页面呢？

事实是，我不知道。也许有一天，Webpack 大师们会创建超级花哨的插件，将动态路由分成具有自己的部署配置的功能。谁知道他们下一步会想出什么。与此同时，我有一个不太复杂的解决方案，可以达到同样的效果。

# 重新寄送

因为我们的函数可以返回任何东西，所以让它返回带有我们需要的任何特定于内容的社交和 SEO 元标签的有效 HTML 非常容易。这是我们页面中唯一真正需要预渲染以支持展开的部分。如果我们能让它返回一个 HTML 字符串，我们当然可以在其中包含一段 JavaScript 来帮助异步显示页面的其余部分。

下面是我的`post`函数的简化版本:

[Click here for the full version of this file](https://gist.github.com/pejalo/1715c896658d660e0ded0d49d9910896)

可以看到，要发送的 HTML 响应包括`'<script>window.location="[https://example.com/?post=](https://example.com/?post=)' + post.id + '";</script>'`。一个只寻找你的元标签的爬虫会很高兴收到它们，不会费心去执行这个脚本。但是在浏览器中加载这个页面的真实用户会立即被重定向到`example.com/?post=123`。

现在`example.com/?post=123`上到底发生了什么已经超出了本文的范围，因为它完全取决于您正在使用的前端框架。您只想显示文章内容，而不需要将浏览器重定向回`/posts/123`。(我的网站是用 [Vue.js](https://vuejs.org/) 构建的，所以在我的索引页面的`beforeMount()`功能中我勾选了`this.$route.query.post`。如果存在，我会在索引页面中显示请求的文章，而不触及路由。)需要知道的重要一点是，你可以[操纵](https://developer.mozilla.org/en-US/docs/Web/API/History_API)浏览器地址栏中显示的内容，使其看起来更漂亮。把难看的`?post=123`换成`/posts/123`，可以叫`window.history.replaceState({}, ‘Title’, ‘/posts/’ + postId);`。

瞧啊。除非用户强迫性地注意他们的地址栏，否则他们所知道的就是他们点击了一个链接，并最终出现在我们网站的正确页面上。

老实说，作为一个设计狂，这种双重重定向确实让我有点恼火。它不是闪闪发光的干净。但是这种情况只会发生在那些从其他来源通过像`example.com/posts/123`这样的独立链接导航到我们网站的用户身上。从推荐网站跳到我们网站的不和谐体验掩盖了后台发生的快速重定向。

你知道什么比小小的重定向更肮脏吗？启动一个新的虚拟机，安装 Ubuntu，添加你的 SSH 密钥，创建一个新的 sudo 用户，`sudo apt-get update`等等，你明白了…

Firebase FTW。

坚持到最后学到了什么？留点爱💚

*Peter LoBue 是一名自由职业的 UX 设计师、网页开发人员和 iOS 开发人员，居住在费城和旧金山之间。伸手可得:* [*@pejalo*](https://twitter.com/pejalo)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)