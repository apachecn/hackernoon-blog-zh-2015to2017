# 微服务系列:带 stdlib 的 Twilio

> 原文：<https://medium.com/hackernoon/microservice-series-twilio-with-stdlib-8c8c98db6f65>

![](img/d93eb52ae7f5999665fbb0ff591d27ef.png)

Using Twilio and stdlib to create a scalable SMS service.

一项让用 Twilio 发送消息变得更容易的服务。

这篇文章是我将在接下来发布的一系列开源项目的一部分，正如之前在这里描述的。

# 传递消息

之前，我们已经开发了一个产品， [Suto](https://asksuto.com) ，它通过短信提供产品推荐。我们必须通过短信和彩信向用户发送大量信息。幸运的是，有几家像 [Twilio](https://twilio.com) 和 [Plivo](https://www.plivo.com/) 这样的大公司正在解决通过多个网络传递信息的宏观问题。

然而，在处理这些 API 以及为通过 SMS 或 MMS 与您的企业进行通信的用户提供良好的体验方面，存在相当多的问题。

这些问题包括以错误的顺序或格式传递消息，以及根本没有传递消息。作为一家试图创造人们喜爱的产品的小型创业公司，我们必须处理所有这些问题。

我们在 [stdlib](https://stdlib.com) 上创建了一个简单的服务来解决这些问题。

# Twilio 消息微服务

今天，我们开源了我们构建的一小部分来处理其中的一些问题。特别是消息订单交付、截断和多媒体消息传递。

这是一个使用 Twilio 帮助传递消息的微服务，名为 [twilio-f](https://github.com/nemo/twilio-f) ，使用 [stdlib](https://stdlib.com/@nemo/lib/twilio/) 构建。该服务在[这里](https://stdlib.com/@nemo/lib/twilio/)可用，代码可以在[这里](https://github.com/nemo/twilio-f)找到。

您可以使用它的***sendMessage****功能来发送带有多媒体附件的长篇描述性消息，它会为您处理所有工作。*

*它将处理**多个媒体 URL**，自动**分割长消息**以避免截断，并且**使用超时控制发送顺序**。*

*你可以在这里阅读如何开始:[https://github.com/nemo/twilio-f#sending-messages](https://github.com/nemo/twilio-f#sending-messages)*

*下一次，当你在制作聊天机器人或向你的客户发送短信/彩信时，现在有一个超级简单且经过实战检验的功能可以完成这项工作！*

*如果你想继续关注开源微服务的发布，请关注我的帖子。*

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*