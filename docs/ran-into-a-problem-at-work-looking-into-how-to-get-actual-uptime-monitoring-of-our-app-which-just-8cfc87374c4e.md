# 使用 NightmareJs 监控正常运行时间

> 原文：<https://medium.com/hackernoon/ran-into-a-problem-at-work-looking-into-how-to-get-actual-uptime-monitoring-of-our-app-which-just-8cfc87374c4e>

在工作中遇到了一个问题，如何获得我们的应用程序的实际运行时间监控，仅仅 ping URL 是不够的。当然，这表明我们已经“上线”了，但是如果用户无法登录呢？当用户可以登录，但没有从我们的后端服务获得有用的数据时，会发生什么？这就是挑战。

当然，我们可以找一个 SaaS 来做这件事，或者依靠另一个团队来做这件事，但是这有什么意思呢？因此，在研究使用开源工具时，我们发现 [watchmen](https://github.com/iloire/watchmen) 是可扩展的，可以在任何云提供商上运行，并且有一个插件生态系统。

因为我们需要某种浏览器[自动化](https://hackernoon.com/tagged/automation)，用一点工具，我们能够找到一个小的、易于使用的库，几乎任何人，甚至开发者都可以编写(加上一个记录点击并转换为 [javascript](https://hackernoon.com/tagged/jasvascript) 的浏览器扩展)，如果我们需要更深的点击来验证我们的网站正常运行时间，这降低了障碍。噩梦(不介意名字)完全符合我们的需求。

我和我的一个队友将这一切合二为一，我们意识到了帮助其他团队的潜力，不仅是在 Comcast 内部，而是在世界上，他们也可能会使用这一点，所以我们决定开源我们的代码。

这是一个守望者插件，它使用噩梦浏览器自动化库，让你进行综合监控，以验证你的网站或应用程序符合你的标准。

我可以更深入地了解所有基础设施的无聊细节，以及它们部署的原因和方式，但我将把这些留到另一个故事中。

[](https://github.com/Comcast/watchmen-ping-nightmare) [## 康卡斯特/守望者-平-噩梦

### 这是一个为守望者设计的插件，使用梦魇和电子浏览器来监控网站

github.com](https://github.com/Comcast/watchmen-ping-nightmare) 

您可以从 npmjs 下载这个包，网址是:

[](https://www.npmjs.com/package/watchmen-ping-nightmare) [## 守望者-平-噩梦

### 守夜人夜魔平集成

www.npmjs.com](https://www.npmjs.com/package/watchmen-ping-nightmare) 

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！