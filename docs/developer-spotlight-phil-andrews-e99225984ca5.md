# 开发者聚焦:菲尔·安德鲁斯

> 原文：<https://medium.com/hackernoon/developer-spotlight-phil-andrews-e99225984ca5>

![](img/415c2aba42df79d571c86ba90c81a1c3.png)

它是一只鸟；是一架飞机！不，这只是开发人员聚焦系列的最新部分，开发人员是我们故事的主角。我们采访了 T2 的菲尔·安德鲁斯，他是一名网络应用开发者和 JavaScript 传播者。在[推特](https://twitter.com/phl_andrews)、 [Medium](/@PhilipAndrews) 上关注他，并享受问答环节

**你开发软件有多久了？相对而言，我是游戏新手，不到三年前才开始写软件。我需要为公司定制一些软件。问题是，我不知道我到底想要什么，但我知道让别人来建造它会有多贵，而且每次需要改变时，我都不得不收回成本。所以我决定自己学着做。**

我从 [Swift](https://cosmicjs.com/knowledge-base/ios-cms) 开始，然后是 [Python](https://cosmicjs.com/knowledge-base/python-cms) ，然后是 [JavaScript](https://cosmicjs.com/knowledge-base/javascript-cms) 。从那以后就没回来过。事实上，为了在自由职业和个人项目之间分配时间，我将在这个月底关闭我之前的公司。

**您首选的开发堆栈是什么？如果有意义，我不会离开苹果的生态系统。工具(Xcode)，语言(Swift)，它们太棒了，让人爱不释手。尤其是当它们配对在一起的时候。令人印象深刻的是苹果公司在他们的 IDE 背后所投入的力量。**

但是在现实世界中[节点](https://cosmicjs.com/knowledge-base/nodejs-cms) / [表达](https://cosmicjs.com/knowledge-base/emberjs-cms) / [反应](https://cosmicjs.com/knowledge-base/react-cms)都是我的日常工具。JavaScript，在开发人员想去的任何地方编写。这真的应该是它的口号。对于我所有的后端需求，我使用 AWS。他们处理我所有的数据库、服务器和存储组件。我的 IDE 是 Atom。再说一次，如果它对每种语言都一样，那就没有什么比 Xcode 更好的了。

过去有哪些项目是你最引以为豪的，为什么？
我做的第二件事对我的技能来说太复杂了。一款多人 iOS 琐事应用。它配有配对、全球排行榜、复杂的游戏逻辑(更像国际象棋，而不像琐事破解)，以及从支付到地理定位再到推送通知的几乎整套移动插件。我已经不知所措了。这很棒，因为它迫使我在短时间内学习了大量的知识。app，登陆页，预览视频，我都写了。那件事完成后，我如释重负。

**多谈一点你在你的世界和垂直领域构建应用的流程。**
如果我想使用我所有的 JavaScript 工具箱来创建一个漂亮的博客，我应该在哪里存储我的内容呢？我将需要经常检索和更新它的大部分。此外，它还需要在仪表板中内置富文本编辑和保存功能。

答案是用最少的代码行以简单灵活的方式检索预先格式化的内容。

![](img/cc82e4bafa1ac83879ae8f548d0c5c41.png)

这是一个返回一堆对象(我的所有博客文章)或一个对象(一篇博客文章)的单一端点。它在[宇宙](https://cosmicjs.com/)中的每个对象页面的顶部。向它发送 get 请求将返回一个 JSON 响应对象，其中包含所有相关的内容和元数据。对我来说，post 主体内容是以 HTML 格式提交的，所以它可以直接注入到页面中。

页面顶部的链接是很棒的设计。将它复制并粘贴到任何浏览器中，您就可以立即看到 JSON 对象。它使得测试和调试非常快速。

下面是两个输入框，还有一个功能齐全的编辑器。它在所见即所得和直接 HTML 格式之间切换。这允许您轻松地添加自定义的类或标签。

![](img/1c635a5ed6ec6ce13f8355018e0e3b76.png)

最重要的是[宇宙](https://cosmicjs.com/)是平台不可知的。我可以写一篇文章，然后从任何地方访问它。一个获取请求，一个发布。或者，一个获取请求，我的所有帖子。我不需要在我的项目中加入任何东西。没有插件，没有下载，只是一个简单有效的内容交付系统。

几个小时后，我偶然发现了宇宙。但是当我摆弄了几分钟后，我就停止了寻找。对于博客或作品集的内容管理，我想不出更好的系统了。我已经在考虑如何将它整合到客户端项目中，因为该界面对开发人员和非开发人员都很友好。

**您对目前正在使用的哪些技术感到兴奋，或者想了解更多？**
我已经完全投入到 React 的前端工作中。我正在做的下一个大项目是一个双平台 React 原生应用程序，所以我会更深入地涉足这些领域。对我来说，真正有趣的东西是在人工智能的世界里。自动驾驶汽车让我着迷。不必开车去某地的想法将对社会产生深远的影响。我完全支持“如果可以自动化，就应该自动化”。

我现在从事的个人项目非常依赖机器学习。我融入得越多，就越意识到我们离我们都熟悉的虚构的人工智能有多远，比如钢铁侠中的贾维斯。主要问题在处理能力上。我相信，在我们开发出一种新的 CPU 之前，我们不会获得一流的人工智能体验。但是我跑题了。我们会到达那里的。即使只是我们现在拥有的一小部分人工智能也是非常有用的。

**宇宙 JS 开发者聚焦系列**

[开发者聚焦:Jason Price](https://cosmicjs.com/blog/developer-spotlight-jason-price)
[开发者聚焦:Abe Hendricks](https://cosmicjs.com/blog/developer-spotlight-abe-hendricks)
[开发者聚焦:Brian Mullis](https://cosmicjs.com/blog/developer-spotlight-brian-mullis)
[开发者聚焦:Micah Walter](https://cosmicjs.com/blog/developer-spotlight-micah-walter)
[开发者聚焦:Joe Tuson](https://cosmicjs.com/blog/developer-spotlight-joe-tuson)
[开发者聚焦:编码道场](https://cosmicjs.com/blog/developer-spotlight-coding-dojo)
[开发者聚焦:简单媒体](https://cosmicjs.com/blog/developer-spotlight-simple-media)
[开发者聚焦:Jon Kalfayan](https://cosmicjs.com/blog/developer-spotlight-jon-kalfayan)

[Cosmic JS](https://cosmicjs.com/) 是一个 API 首创的基于云的内容管理平台，可以轻松管理应用和内容。如果你有关于[宇宙 JS API](https://cosmicjs.com/) 的问题，请在 [Twitter](https://twitter.com/cosmic_js) 联系我们，或者加入 [Slack](https://cosmicjs.com/community) 的社区。