# 黑客流星 DDP

> 原文：<https://medium.com/hackernoon/hacking-meteor-ddp-9da37790b37b>

![](img/d154301ee752eda16f0cf0fc940f5464.png)

the meteor by ready2freeze

> TD；DR，通过监控**Meteor . connection . _ stream**来跟踪通过您的 Meteor 应用程序的 DPP 连接的所有消息。

在 Meteor 上创建功能测试可能是一件痛苦的事情。它的反应对 UX 来说很好，但当我们谈论功能测试时，事情会变得复杂。

在我一直在做的这个项目中，我们使用[夜巡](http://nightwatchjs.org/)来编写我们的功能测试。这个[工具](https://hackernoon.com/tagged/tool)在 **Selenium** 服务器上运行浏览器测试，并允许我们用 **Nodejs** 编写测试规范。

几周前，我试图弄清楚如何在触发项目网站页面上某个按钮上的 *click* 命令之前等待 Meteor 方法的结果。

等待这个结果并不容易。另外，我们尽量避免使用 Nightwatch 提供的 [*暂停*命令](http://nightwatchjs.org/api#pause)。

当有人推断等待一个计算完成需要多少时间时，会发生很多不好的事情，例如，在提交表单后等待一些 CSS 类被添加到一个 *div* 中。

因为*暂停*不是一个选项，我需要另一种方式来等待这个方法的结果。经过一些调查，我想出了监听 **DDP** 消息的主意。

## 流星的 DDP

> DDP 是 Meteor 的内置发布/订阅和 RPC 协议

互联网上已经有很多文章可以很好地向您介绍 DDP 协议。你也可以在 Meteor 的文档中阅读更多关于 DDP 的内容，链接[这个链接](https://guide.meteor.com/accounts.html#userid-ddp)。

简而言之，DDP 是一个基于 JSON 的协议，它是由 [Meteor](https://hackernoon.com/tagged/meteor) 在 **SockJS** 之上实现的。它用于在客户端和服务器之间创建全双工通信，在此通信中，它可以更改数据并对其更改做出反应。

> SockJS 是一个模拟 WebSockets 的 Javascript 库

当你使用 *Meteor.method，Meteor.call* ， *Meteor.publish* 和 *Meteor.subscribe* 时，Meteor 在底下使用的是 DDP。

**监听 DDP 消息**

通过在 Meteor 全局对象上访问名为 **_stream** 的变量，可以在客户端找到由 Meteor 创建的 DDP 连接:

```
Meteor.connection._stream
```

有了这个对象，我们可以在客户端发送和监听任何我们想要的消息。下面的*要点*展示了一个非常简单的代码示例:

首先，我们保存对原始发送方法的引用。

然后我们用期望的函数覆盖*发送*方法。在这个例子中，它只是在通过最初的 *send* 方法将消息发送到服务器后打印消息。

最后，我们为**消息**事件注册了一个新的监听器，它也将打印从服务器接收到的所有内容。以前注册的所有侦听器都不会受到影响。

如果您有一个 Meteor 应用程序，您可以将这个片段复制并粘贴到您的浏览器控制台上，它将开始跟踪通过 DDP 的所有内容。

您可能注意到的第一件事是乒乓消息。Meteor 在一段时间间隔后继续发送这些消息，以检查连接是否仍然有效。

```
> {"msg":"pong"}
> {"msg":"ping"}
```

**等待方法响应**

你可能会在流中找到我的消息类型。您可以通过浏览您的应用程序并监视控制台中发生的事情来检查这一点。

我使用带有类型**方法**的消息从我的测试中移除对**暂停**命令的依赖。测试现在等待，直到**结果**消息到达，在一些时间间隔内在浏览器控制台中搜索它。

```
Meteor.call('foo');> {"msg":"method", "method":"foo", "params":[], "id":"42"}> {"msg":"result", "id":"42", "result":[{"a":1}, {"b":2}]}
```

**结果**不包括方法名，但是可以在**方法**消息中找到，作为*方法*属性的值。它们通过 **id** 属性链接在一起。

这同样适用于等待*订阅*准备就绪。

```
Meteor.subscribe('bar');> {"msg":"sub", "id":"abc1def3", name:"bar", params:[]}> {msg:"ready", subs:["abc1def3"]}
```

## 结论

除了功能测试之外，你还可以用这个 Meteor Hack 做一些很酷的事情:

*   排除故障
*   查找内存泄漏
*   分析安全问题

## 参考

*   [https://meteorhacks.com/introduction-to-ddp/](https://meteorhacks.com/introduction-to-ddp/)
*   [https://docs.meteor.com/api/connections.html](https://docs.meteor.com/api/connections.html)
*   [https://github.com/sockjs/sockjs-node](https://github.com/sockjs/sockjs-node)
*   [http://docs.seleniumhq.org/](http://docs.seleniumhq.org/)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)