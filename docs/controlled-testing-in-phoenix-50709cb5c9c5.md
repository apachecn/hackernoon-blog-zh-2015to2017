# 在 Phoenix 测试中避免第三方 API 调用

> 原文：<https://medium.com/hackernoon/controlled-testing-in-phoenix-50709cb5c9c5>

最近在做一个 [Phoenix](https://hackernoon.com/tagged/phoenix) 项目时，我发现我想在运行我的测试时避免调用第三方 API。使用 [Twilio](https://www.twilio.com/) ，通过 [ExTwilio](https://github.com/danielberkompas/ex_twilio) 库，我给用户发送短信。发送每条短信都要花钱，我的测试是[生成](https://hackernoon.com/tagged/generating)随机的电话号码和信息，所以你可以明白我为什么需要这么做。我用这个[方法](https://hackernoon.com/tagged/method)进行第三方 API 调用:

为了避免库调用，我在[插件中做了一些挖掘。Conn](https://hexdocs.pm/plug/Plug.Conn.html) 文档并看到当运行测试时，Conn 的适配器字段是‘Plug’。“适配器.测试.连接”。知道了这一点，我认为模式匹配是在测试期间避免 API 调用的简单方法，我只需将 conn 传递给我的方法，然后像这样编辑它:

如果运行测试，将调用第一个[实现](https://hackernoon.com/tagged/implementation)，因为它将首先匹配，否则将调用真正的实现。有了它，我可以很容易地避免我的 API 调用，这对我来说并不坏，因为它只发生在我代码中的一个地方。

然而，我在函数中传递了一个参数，而没有真正在函数内部做任何事情，所以如果我不得不在多个地方这样做，它可能会变得很脏。此外，它依赖于 conn，所以如果我必须在 web 请求未被处理时调用此方法，就会出现问题。为了解决这个问题，还有另一种方法，它使用项目的配置设置，并在测试期间调用一个假模块。

比方说，我正在运行相同的短信发送代码，除了它是在后台完成的，我们没有连接，我拉一个电话号码列表，每个小时给每个人发一条短信。让我们像这样定义一个模块 Twilio.ex 来处理 API 调用:

同时，我会定义一个假的模块来模拟测试用例中使用的真实模块，FakeTwilio.ex:

现在，通过在我们的 config.exs 和 test.exs 文件中设置定义，我可以很容易地根据我是否正在运行测试来使用正确的模块:

这将设置它，以便通过“Application.get_env”调用检索正确的模块，只需将模块包装在一个帮助器函数中，该函数将获得正确的配置设置，如下所示:

这里，私有的 text_module 方法将在测试期间自动加载假模块，否则加载真模块。

这两种方法都允许我在运行测试时避免调用第三方 API，这里有一个使用这两种设置的示例项目:[https://github.com/chiragtoor/test_controlled_apis](https://github.com/chiragtoor/test_controlled_apis)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)