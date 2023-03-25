# 修正了 uBlock Origin 阻塞谷歌第三方认证弹出窗口的问题

> 原文：<https://medium.com/hackernoon/fixed-a-problem-where-ublock-origin-was-blocking-googles-third-party-authentication-pop-up-window-62f3d1c171ec>

你必须注册，并使用谷歌账户登录到[canary . pro](https://granary.pro)。

问题是，当你点击“免费注册谷歌”按钮时，请求访问你的谷歌账户的弹出窗口被 uBlock Origin 阻止了。

在粮仓. pro 中，我们在首页源码中使用了以下声明来使用 GA，这部分被 uBlock Origin 屏蔽了。

由于 google-analytics.js 只是使用 GA 清楚地阅读源代码所需的声明的包装，

> (function(i，s，o，g，r，a，m){ I[' googlenalyticsobject ']= r；i[r]=i[r]||function(){(i[r]。q=i[r]。q||[])。push(arguments)}，i[r]。l = 1 * new Date()；a=s.createElement(o)，m = s . getelementsbytagname(o)[0]；a . async = 1；a . src = g；m.parentNode.insertBefore(a，m)})(窗口，文档，'脚本'，'【https://www.google-analytics.com/analytics.js'，' ga ')；
> 
> ga('创建'，' UA-91234420–1 '，'自动')；

如果直接在 homepage source 中声明上述声明，将会修复 Google 第三方认证弹窗被 uBlock Origin 屏蔽的问题。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！