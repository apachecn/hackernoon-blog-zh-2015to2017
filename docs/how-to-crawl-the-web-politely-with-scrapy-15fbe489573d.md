# 如何用 Scrapy 有礼貌地抓取网页

> 原文：<https://medium.com/hackernoon/how-to-crawl-the-web-politely-with-scrapy-15fbe489573d>

网络爬行的第一条规则是你不能损害网站。网络爬行的第二条规则是你做**而不是**损害网站。我们是网络数据民主化的支持者，但不是以网站所有者为代价。

在本帖中，我们为想要礼貌周到的网络爬虫的 [Scrapy](https://scrapy.org/) 用户(Scrapy 是一个 100%开源的网络爬虫框架)分享一些小技巧。

不管你叫它们蜘蛛、爬虫还是机器人，让我们一起创造一个 Baymaxs、WALL-Es 和 R2-D2s 的世界，而不是一个 HAL 9000s、T-1000 和威震天的世界末日荒原。

![](img/6514b6bcae7d1b39f7bdaf55c00435cc.png)

Embrace the lovable bots

# 是什么让爬虫变得有礼貌？

> 一个有礼貌的爬虫尊重机器人。txt
> 一个有礼貌的爬虫从不降低网站的性能
> 一个有礼貌的爬虫用联系信息识别它的创建者
> 一个有礼貌的爬虫不是系统管理员的眼中钉

# robots.txt

始终确保您的爬虫遵循网站的 robots.txt 文件中定义的规则。这个文件通常可以在一个网站(www.example.com/robots.txt)的根目录下找到，它描述了根据[机器人排除标准](https://support.google.com/webmasters/answer/6062608?hl=en)爬虫应该或不应该爬行的内容。一些网站甚至使用爬虫的用户代理为不同的网络爬虫指定单独的规则:

```
User-agent: Some_Annoying_Bot
Disallow: /User-Agent: *
Disallow: /*.json
Disallow: /api
Disallow: /post
Disallow: /submit
Allow: /
```

# 爬行延迟

拥有一个有礼貌的爬虫的关键任务是确保你的爬虫不会太猛烈地攻击一个网站。遵循 robots.txt Crawl-Delay 指令，遵守爬网程序在请求之间应该等待的延迟。

当一个网站的请求数量超过 web 服务器的处理能力时，它可能会变得没有响应。不要成为让网站管理员头疼的人。

# 用户代理

然而，如果你忽略了上面的基本规则(或者你的爬虫已经获得了侵略性的感知)，需要有一种方法让网站所有者联系你。为此，您可以在请求的用户代理标题中包含您的公司名称和电子邮件地址或网站。比如谷歌的爬虫用户代理就是“Googlebot”。

# 如何礼貌地使用 Scrapy

Scrapy 有点像擎天柱:友好、快速，无论如何都能完成任务。然而，就像擎天柱和他的汽车人同伴一样，Scrapy 偶尔也需要被控制。因此，这是确保 Scrapy 尽可能礼貌的本质。

[![](img/44571c27d509bfba2875f37072f09688.png)](https://scrapy.org/)

# Robots.txt

默认情况下，使用 Scrapy 1.1+创建的爬虫已经尊重 robots.txt。如果您的爬网程序是使用以前版本的 Scrapy 生成的，您可以通过将它添加到项目的设置中来启用此功能。

```
ROBOTSTXT_OBEY = True
```

然后，每当您的爬虫程序试图从不允许的 URL 下载页面时，您将会看到这样的消息:

```
2016-08-19 16:12:56 [scrapy] DEBUG: Forbidden by robots.txt: <GET http://website.com/login>
```

# 识别您的爬虫

如果系统管理员对您的爬虫有任何问题，为他们提供一种容易联系您的方式是很重要的。如果你不这样做，他们将不得不挖掘他们的日志，寻找违规的 IP。

善待你生活中友好的系统管理员，并通过 Scrapy USER_AGENT 设置识别你的爬虫。分享您的爬虫名称、公司名称和联系电子邮件:

```
USER_AGENT = 'MyCompany-MyCrawler (bot@mycompany.com)'
```

# 引入延迟

刺痒蜘蛛速度惊人。它们可以处理许多并发请求，并充分利用您的带宽和计算能力。然而，权力越大，责任越大。

为了避免过于频繁地访问 web 服务器，您需要在您的项目中(或者在您的蜘蛛中)使用 [DOWNLOAD_DELAY](http://doc.scrapy.org/en/latest/topics/settings.html?highlight=download_delay#download-delay) 设置。Scrapy 将在对同一域的连续请求之间引入一个范围从 0.5 * DOWNLOAD_DELAY 到 1.5 * DOWNLOAD_DELAY 秒的随机延迟。如果你想坚持你定义的确切的下载延迟，你必须禁用[随机化下载延迟](http://doc.scrapy.org/en/latest/topics/settings.html?highlight=download_delay#randomize-download-delay)。

默认情况下，DOWNLOAD_DELAY 设置为 0。要在爬网程序的请求之间引入 5 秒钟的延迟，请将其添加到您的设置中，py:

```
DOWNLOAD_DELAY = 5.0
```

如果您有一个爬行多个站点的多蜘蛛项目，您可以使用 download_delay(是的，是小写)蜘蛛属性为每个蜘蛛定义不同的延迟:

```
class MySpider(scrapy.Spider):
    name = 'myspider'
    download_delay = 5.0
    ...
```

# 每个域的并发请求

另一个让你的蜘蛛更有礼貌的设置是每个域并发请求的数量。默认情况下，Scrapy 将同时向任何给定的域发送最多 8 个请求，但是您可以通过更新[CONCURRENT _ REQUESTS _ PER _ DOMAIN](http://doc.scrapy.org/en/latest/topics/settings.html#concurrent-requests-per-domain)设置来更改该值。

注意， [CONCURRENT_REQUESTS](http://doc.scrapy.org/en/latest/topics/settings.html?highlight=download_delay#concurrent-requests) 设置定义了 Scrapy 的下载器将为所有蜘蛛执行的最大并发请求量。当你同时抓取多个域时，调整这个设置更多的是关于你自己的服务器性能/带宽，而不是你的目标。

# 自动油门拯救世界

网站可以处理的请求数量差异很大。为你正在浏览的每个网站手动调整这个就像看着油漆变干一样有趣。为了拯救你的理智，Scrapy 提供了一个名为 [AutoThrottle](http://doc.scrapy.org/en/latest/topics/autothrottle.html) 的扩展。

自动油门根据当前 web 服务器负载自动调整请求之间的延迟。它首先计算一个请求的等待时间。然后，它将调整同一域的请求之间的延迟，使得不超过[个 AUTOTHROTTLE _ TARGET _ CONCURRENCY](http://doc.scrapy.org/en/latest/topics/autothrottle.html#std:setting-AUTOTHROTTLE_TARGET_CONCURRENCY)个请求同时处于活动状态。它还确保请求在给定的时间跨度内均匀分布。

要启用自动油门，只需将其包含在项目的设置中。

```
AUTOTHROTTLE_ENABLED = True
```

[Scrapy Cloud](https://scrapinghub.com/scrapy-cloud/) 用户不必担心启用它，因为它已经默认启用。

有一个[大范围的设置](http://doc.scrapy.org/en/latest/topics/autothrottle.html#settings)来帮助你调整油门机制，所以玩得开心点！

# 使用 HTTP 缓存进行开发

开发网络爬虫是一个迭代的过程。然而，运行爬虫来检查它是否工作意味着每次测试都要多次访问服务器。为了帮助你避免这种不礼貌的活动，Scrapy 提供了一个名为 [HttpCacheMiddleware](http://doc.scrapy.org/en/latest/topics/downloader-middleware.html#module-scrapy.downloadermiddlewares.httpcache) 的内置中间件。您可以通过将它包含在项目的设置中来启用它，py:

```
HTTPCACHE_ENABLED = True
```

一旦启用，它将缓存蜘蛛发出的每个请求以及相关的响应。所以下一次你运行你的蜘蛛时，它不会向服务器请求已经完成的请求。这是一个双赢的局面:你的测试会运行得更快，网站也会节省资源。

# 不要抓取，使用 API

许多网站提供 HTTP APIs，以便第三方可以使用他们的数据，而不必抓取他们的网页。在构建 web scraper 之前，检查目标网站是否已经提供了您可以使用的 HTTP API。如果有，就用 API。同样，这是一个双赢的局面:你避免了钻研页面的 HTML，你的爬虫变得更加健壮，因为它不需要依赖于网站的布局。

# Zyte 滥用报告表

嘿，使用我们 [Scrapy Cloud](https://www.zyte.com/scrapy-cloud/) 平台的人们！我们相信你会负责任地爬行，但为了支持网站管理员，我们提供了一个[滥用报告表](https://www.zyte.com/contact-us/)，他们可以报告在我们平台上运行的爬虫的任何不当行为。我们会友好地传递消息，以便您可以修改您的抓取，避免破坏一个系统管理员的一天。如果你的爬虫正在变成天网和践踏人类法律的，我们保留停止它们爬行活动的权利，从而避免机器人的末日。

# 包裹

让我们都尽自己的一份力量来保持系统管理员、网站所有者和开发者之间的和平，确保我们的网络爬行项目尽可能地无创。请记住，我们需要团结起来，以推迟我们的机器人霸主的崛起，所以让我们保持我们的爬虫，蜘蛛和机器人礼貌。

![](img/e5ed7edd63b1fed535c7cff0ce5519fd.png)

所有网站所有者，帮助爬虫，确保你的网站有 HTTP API。

[刺痒云永远免费](https://www.zyte.com/scrapy-cloud/)是刺痒果冻的花生酱。希望你学到了一些既能加快抓取速度又能防止滥用投诉的技巧。

![](img/7c7fe2e670081a58430f9315be2e77ed.png)

这篇帖子是 Zyte(前身为 Scrapinghub)的开发者巴尔迪尔·斯图姆([@ stumjr](https://twitter.com/stummjr))写的。

请用心“推荐”，让别人更好地了解如何礼貌地使用 Scrapy。

[**了解更多关于网络抓取和网络数据能为你做什么**](https://www.zyte.com/data-extraction/) **。**

最初发表在 Zyte 博客上。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)