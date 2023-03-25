# 交付与增长:选择什么以及何时选择

> 原文：<https://medium.com/hackernoon/delivery-vs-growth-which-to-choose-and-when-8296f7ddf0c1>

## 如何在速度和个人成长之间取得平衡

![](img/535908f282a7ac2583f2b042f1eb2f36.png)

在过去的两年里，我一直在新加坡的一家初创公司担任全栈开发人员，参与不同的项目，为我的移动应用构建不同的模块。

当构建最小可行产品(MVP)来测试需求时，我们采用敏捷方法，参与诸如 [Scrum](https://www.scrum.org/resources/what-is-scrum) 和[看板](https://leankit.com/learn/kanban/what-is-kanban/)的实践，因为项目需求总是在变化，总是有新的特性要开发，新的项目要承担。

正如敏捷的名字所暗示的，上市速度无疑是一个竞争优势，尤其是在初创企业中，我相信这是因为当资源紧张和有限时，一切都必须快速进行。

然而，作为开发人员，这是一个永无止境的学习过程，因为技术变化很快，跟上最新的工具、语言和框架很重要。

> 这可能是危险的；这是通往停滞的道路。知道“小丑”并与这三个和弦一起生活会很舒服。这就好比你匆匆浏览 stackoverflow.com 的[帖子，然后复制/粘贴，只是为了完成手头的任务。或者在不了解其工作原理的情况下使用“xyz”插件来解决问题— **摘录自**](http://stackoverflow.com/) [**开发人员如何以及为什么必须不断发展**](https://ontraport.com/blog/engineering/developers-must-always-evolve)

因此，当速度至关重要时，如何在编写好代码和速度之间取得平衡呢？面对更快交付产品的压力，作为一名开发人员，您如何找到时间来学习或成长？

以下是我从自己的经历中学到的一些经验，告诉我如何在个人学习/成长和快速交付的压力之间取得平衡:

> TL；DR，交付快不代表停滞不前，也不代表===蹩脚代码。

# 1." IDWID "

“当它完成的时候就完成了”就像遥远星系中某个 scrum 大师引用的那样…

当我刚开始开发时，我常常给自己很短的时间来编写特性，但我意识到这不仅导致了代码中的错误，因为它没有经过正确的测试，而且也是一个考虑不周的最终产品。

开发者要学会反推。当一个紧张的截止日期到来时，它会引起恐惧和分心，这会抑制创造力/下一层次的思考。给自己多点时间。为了给批判性思考的时间、提问的时间、产生想法的时间、计划的时间、实验和成长的时间让路，牺牲速度是可以的。

# 2.减少、再利用和回收

政府是这么说的……还有你一直敬仰的开发商。构建您自己的模板或组件，以便在不同的项目中重用。这大大加快了速度。通过很好地构建一个组件并重用它，没有必要为了达到速度而牺牲代码质量！

> 我们不是在设计页面，我们是在设计组件系统。— [斯蒂芬·海伊](http://bradfrost.com/blog/mobile/bdconf-stephen-hay-presents-responsive-design-workflow/)

**原子设计**的概念是将整个界面分解成基本的构建模块，并从那里开始工作。您可以将您的项目分成不同的组件并重用它们，这也可以产生更清晰和更一致的代码。顾名思义，原子设计的要旨是一切物质都是由原子构成的。这些原子单位可以结合形成分子，最终形成更复杂的有机体，导致我们宇宙中的所有物质。

我过去花了很多时间验证不同的想法，对于每个想法，我会花 2-3 天从头开始创建一个简单的登录页面。所以有一天，我决定为自己(【https://github.com/stacygohyunsi/landing-starter】)建立一个登陆页面启动模板，这样我就可以一次又一次地重复使用它。同样，您可以构建自己的迷你组件，并在不同的项目中重用它们。

> 在这里阅读更多:[为什么要用原子设计](http://www.creativebloq.com/web-design/10-reasons-you-should-be-using-atomic-design-61620771)？

# 3.好奇心拯救猫

做一个狂热的探险家。涉足新技术，尝试新的框架和工具。不要过分依赖一个框架/技术。花一些时间学习新的框架，它可以在以后为你节省大量的时间。

就在几年前，Angular 1 还是“热门”框架——它似乎主导了所有与 JavaScript 相关的框架讨论，也是许多开发人员想写的话题。当 Angular 2 问世时，它似乎与熟悉的 Angular 1 和一种新的架构完全不同。这是我开始更加关注 ReactJS 并投入相当多的时间学习 ReactJS + Redux 的时候，这与 Angular 1 相比是一个相当大的思维转变。现在，它已经成为我的首选框架，即使谷歌不打算在未来继续支持 Angular 1，我仍然能够继续开发项目，而不会延迟速度。

现在，在你开始对大肆宣传驱动的开发进行批评并对这篇文章不满之前，让我添加一个免责声明，我建议只在附带项目中使用新技术/框架。一旦你充分利用了这项技术，并且确定了它的利弊，那么你就可以考虑向你的团队推荐它了。

如果你是一个 Javascript 爱好者(就像我一样)，下面是我从个人项目中探索的一些 JS 框架，它们在我需要快速构建和交付产品的绝望时刻帮助了我:

[**next js**](https://github.com/zeit/next.js)

*   一个易于使用的样板模板，可以节省大量设置 React 应用程序的时间。它带有自动传输和捆绑(与 webpack 和 babel)，热代码重载，简单的页面路由和文件服务。

[GatsbyJS](https://github.com/gatsbyjs/gatsby)

*   Gatsbyjs 是一个 React.js 静态站点生成器。它允许实时重载，对于快速开发网站/博客特别有用。

[](https://github.com/hakimel/reveal.js)

*   **非常适合创建幻灯片、目录、培训演示或在短时间内介绍新产品的方法(包装有现成的动画！)**

> **在这里阅读更多:[炒作驱动发展](https://blog.daftcode.pl/hype-driven-development-3469fc2e9b22)**

**我喜欢讨论想法，所以如果你在这些方面有任何想法/意见，我很乐意听听。**

**最后，如果你想知道我接下来要做什么，请继续关注@[**https://github.com/stacygohyunsi**](https://github.com/stacygohyunsi)！**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**