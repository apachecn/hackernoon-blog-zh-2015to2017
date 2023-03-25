# GitHub 页面和 Javascript 的免费托管无服务器功能

> 原文：<https://medium.com/hackernoon/freely-hosted-serverless-functionality-with-github-pages-and-javascript-9152167c1d26>

原谅我，亲爱的读者，如果所有这些都是显而易见和众所周知的。我没有看到书面的。如果这仅仅是我的无知，请开导我。

作为一些快速开发自由软件和原型的人，我经常寻找一种轻量级的方式来提供功能。

每年这变得越来越容易，越来越便宜，轻量级的定义也越来越短暂。

在 2016 年生产这种原型的一个好方法是使用 GitHub 提供的名为“GitHub Pages”的功能，结合 JavaScript 来生产无服务器、零成本、零部署成本的网页。GitHub Pages 是一种有效的网络托管服务，GitHub 免费提供非常有限的服务器端计算。

曾几何时，使用 JavaScript 会严重限制你以这种方式提供的应用程序的种类，但是正如 Cannon.js 和 Three.js [展示的](http://schteppe.github.io/cannon.js/examples/threejs_cloth.html)，你现在可以只使用他们自己无处不在的商品浏览器给用户提供复杂的功能。

在过去的几年里，我以这种方式制作了许多应用程序:

*   一个双摆[展示了](https://pubinv.github.io/chaos-pendulum/)混沌，
*   用于设计电磁线圈的[图形工具](http://pubinv.github.io/CoilChoice/)，
*   向高管介绍敏捷软件的[游戏](http://pubinv.github.io/agile-game-for-execs/AgileSim.html)，以及
*   一个[未发布的工具](https://pubinv.github.io/gluss/playground.html)用于编程一个全新的机器人。

这种方法已经成为我的编程风格。

从某种意义上说，尝试这种风格最简单的方法就是在 GitHub(或任何其他使用相同风格的回购)上派生出我的一个回购，然后去掉你不想要的功能(包括 JavaScript)。我所有的代码都是在 GPL 下发布的。

当然，这种复制-粘贴-附加工作的风格很可能会给你留下一个混乱的代码库，除非你对你的删减很挑剔。

这里有一个基本的“操作方法”,从一个更空泛的点开始:

*   创建一个 GitHub 帐户，如果你没有的话(他们是免费的)，
*   创建新的回购协议，
*   在 repo 的“设置”中使用 GitHub 提供的内置功能来自动创建“GitHub 页面”。您也可以手动完成这项工作，但是这样做有一些缺陷，所以第一次使用自动工具。
*   选择一个适合你的视觉主题。
*   通过访问 GitHub 提供给你的 URL 来测试你是否有一个实时的、可普遍访问的公共网站。它很可能是这样的形式:"[https://YOURORG.github.io/YOURREPO](https://pubinv.github.io/gluss/playground.html)"。
*   通过删除 GitHub 自动页面提供给您的一些默认文本，并将您的本地更改推送到 GitHub repo，测试您是否可以控制和编辑公共页面。
*   把 GitHub 提供的 HTML 中你不想要的部分删掉。
*   继续将某种复杂的渲染打包复制到 repo 中，并从 html 文件中引用它。(更好的方法是使用可用的 CDN，这样您就不必为原型实验复制包。)我过去用过的包有:Bootstrap.js，JQuery.js，JQueryUI.js，Three.js，P2.js，Cannon.js 你最后也可能会用 d3，这是一个既多又少的渲染系统。

此时，您应该有一个(潜在的)响应性 web 应用程序，它完全免费，可以普遍访问，甚至可以渲染用物理引擎计算的照片级真实感场景，或者只有一些用 JavaScript 计算的漂亮按钮和文本。巨大的价值和美丽可以被创造出来。

在某些时候，您可能需要为用户提供与持久数据相关的功能:也就是说，特定于用户 A 且不同于用户 B 的“会话”概念开始变得重要起来。当那一天最终到来时，你也许能够使用浏览器存储来有效地实现它。

最终，您可能会被迫拥有一个真正的数据库来维护持久的身份和其他功能，但不要这样做，直到明确需要它来改善用户体验。让用户拖着你又踢又叫。在此之前，您不必花费任何资金，也不必承担部署和配置服务器的开销和复杂性。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！