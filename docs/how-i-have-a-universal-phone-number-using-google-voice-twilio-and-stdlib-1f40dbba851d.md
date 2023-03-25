# 如何使用 Google Voice、Twilio 和 stdlib 获得通用电话号码

> 原文：<https://medium.com/hackernoon/how-i-have-a-universal-phone-number-using-google-voice-twilio-and-stdlib-1f40dbba851d>

在过去的一段时间里，我搬到了不同的国家，换了几次手机供应商。然而，如果我以前给过你我的号码，你会注意到它从未改变过。无论我在世界的哪个角落，都可以通过这个号码找到我。

我在日本收到过短信，在埃及的金字塔下接过电话，甚至连数据都没有。

事实证明，如果结合使用正确的服务，实现这一点是非常容易和廉价的。

# 我如何拥有一个通用电话号码

为了做到这一点，我使用了一个谷歌语音账户，并将其发送到多个 T2 号码，这些号码会自动将短信和电话转发给我在世界各地的本地号码。

> 谷歌语音号码-> Twilio 号码->本地号码

自动转发由建立在 [stdlib](http://stdlib.com) (我们在公司也使用[)之上的一个简单的微服务提供支持。](/@ngardideh/shipping-quickly-how-we-test-ideas-using-microservices-and-stdlib-9e8143cee403)

## 谷歌语音号码

这是我传递给朋友的主要电话号码。创建一个[谷歌语音](https://www.google.com/voice)账户并设置它非常容易。

你不能直接从谷歌语音转发到你在日本或加拿大的本地号码的原因是，谷歌不转发美国以外的号码。

从那里——你必须使用谷歌语音的转发系统[连接你的 Twilio 号码(代表你的外国号码)。](https://support.google.com/voice/answer/165221?hl=en)

## Twilio 号码转发器

由于 Twilio 支持许多国际国家和地区，因此这是一项很好的来电转接服务。维护电话号码也很便宜——我通常有一两个号码是为美国以外的新 SIM 卡设置的，如果我在旅行，就可以随时使用。每个号码只需 1 美元一个月。

你必须购买一个美国电话号码(因为谷歌语音只能转发到美国号码)，并将其配置为将来电和短信转发到美国以外的实际号码。

这里有一个例子:

购买号码 415-XXX-XXXX(美国加利福尼亚州号码)，并将其配置为拨打和发送短信 011–81–90–xxxx–xxxx(日本号码)。

配置很简单——你只需要有一个服务来响应 Twilio，告诉它应该分别使用他们的 [call webhook](https://support.twilio.com/hc/en-us/articles/223179908-Setting-up-call-forwarding#devs) 和 [message webhook](https://support.twilio.com/hc/en-us/articles/223134287-Forwarding-SMS-messages-to-another-phone-number#dev) 来转发呼叫或文本。

这就是 stdlib 的用武之地——为我们提供一种简单的方法来响应 webhooks，而不必担心整个系统的维护。

## stdlib Twilio Webhook 电话转发器

Twilio 需要一个响应，告诉它将呼叫或消息转移到另一个号码。使用他们的 [Twiml](https://www.twilio.com/docs/api/twiml) 系统可以非常简单地做到这一点。

好消息是，如果你想这么做——我已经写好了代码并在这里[开源了。你所要做的就是配置服务并把 URL 放入 Twilio 中，然后你就可以开始了！](https://github.com/nemo/twilio-f)

你需要一个语音电话:

> [https://nemo.api.stdlib.com/twilio/forward?forward to = 415 xxxxxxxxx&type = call](https://nemo.api.stdlib.com/twilio/forward?ForwardTo=415xxxxxxx&type=call)

一个用于 SMS:

> [https://nemo.api.stdlib.com/twilio/forward?forward to = 415 xxxxxxxxx&type = SMS](https://nemo.api.stdlib.com/twilio/forward?ForwardTo=415xxxxxxx&type=SMS)

这些 URL 可以作为 Twilio 中的 webhook URLs，您已经为自己创建了一个简单的电话转发程序。你可以在这里阅读更多关于 stdlib 服务[的内容，在这里](http://stdlib.com/services/nemo/twilio)查看源代码[。](https://github.com/nemo/twilio-f)

当然，你必须为你在世界不同地方获得的每一个新号码做这件事。我强烈建议保留相同的 SIM 卡，并在你再次入境时重新激活它们。

感谢你阅读我的一个奇怪的生活窍门。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！