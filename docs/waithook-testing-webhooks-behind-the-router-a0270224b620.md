# Waithook —测试路由器后面的 WebHooks

> 原文：<https://medium.com/hackernoon/waithook-testing-webhooks-behind-the-router-a0270224b620>

Waithook.com[是为了在开发和测试环境中轻松接收 HTTP 通知而构建的。它使用 **WebSockets** 来传递消息，因此它可以在防火墙、网络路由和代理之后工作。](http://waithook.com)

测试来自其他服务的 HTTP 通知很难，通常我会设置一些从我的计算机到远程服务器的 SSH 隧道，或者使用一些公共服务来完成。并且每次在 CI 服务器上运行测试时都很难进行这样的设置。所以我想到了服务的概念:客户端将使用 [WebSocket](https://hackernoon.com/tagged/websocket) 协议进行连接，我的服务将接收 HTTP 请求并发送给所有订户。可以把它看作 HTTP 回调的 Pub/Sub。当不止一个开发人员同时使用它或者您的测试并行运行时，它会工作得很好，因为所有的订阅者都会收到所有的消息。

我发现它对以下方面很有用:

*   测试与支付系统的集成
*   测试 [GitHub](https://hackernoon.com/tagged/github) /GitLab webhooks
*   测试传入电子邮件处理
*   测试 slack 机器人
*   测试 facebook webhooks

# 如何使用它

您需要选择您的唯一前缀，将发送服务中的通知 URL 更改为`http://waithook.com/$your_unique_path`(将向您发送 HTTP 通知的服务，如支付系统、收到的电子邮件等)。

它还可以向其他 URL 发送 http 请求，例如您的临时服务器。为此，只需在端点 URL 中添加`?forward_url=http://example.com`。示例:

```
http://waithook.com/foobar?forward_url=http://foobar.com/
```

然后你应该打开 WebSocket 连接到`waithook.com/foobar`，具体可以看在线演示或者使用 ruby 库 [waithook-ruby](https://github.com/Paxa/waithook-ruby) :

```
gem install waithook
waithook waithook.com/foobar --forward http://localhost:3000/notify
```

现在你可以试着发送请求给[http://waithook.com/foobar](http://waithook.com/foobar)，它会发送同样的请求给`http://localhost:3000/notify`耶！

Waithook 是免费的并且[开源](https://github.com/Paxa/waithook)。我把它做得很有趣，并在工作中用来测试支付系统的 webhook 处理。在我的测试中，每当我期望 HTTP 回调到达我的系统时，我就打开新的连接，并等待直到我得到正确的消息，这样我就可以测试完整的流。

我很高兴听到你的任何反馈！编码快乐！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！