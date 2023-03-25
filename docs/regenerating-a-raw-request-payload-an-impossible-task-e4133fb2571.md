# 重新生成原始请求负载——一项不可能完成的任务？

> 原文：<https://medium.com/hackernoon/regenerating-a-raw-request-payload-an-impossible-task-e4133fb2571>

![](img/ae75ae00a2c8721a544dc3e1cef76ce0.png)

**背景**

我正在创建一个 Facebook Messenger bot，需要验证我的 webhook 收到的原始有效负载的签名。我的 webhook 由[Google Cloud Functions](https://cloud.google.com/functions/)(beta)的 http-trigger 提供支持，它自动提供一个 https 端点来调用这个函数，没有任何麻烦。

然而，我在这个过程中遇到了一些问题，这些问题与平台限制、 [JSON](https://hackernoon.com/tagged/json) 编码和模糊的文档有关。我认为分享我如何一次解决一个问题是很有见地的。

**脸书文件**

> HTTP 请求将包含 X-Hub-Signature 标头，其中包含请求有效负载的 SHA1 签名，使用 app secret 作为密钥，前缀为 sha1=。您的回调端点可以验证这个签名，以验证有效负载的完整性和来源。
> 
> 请注意，计算是在有效负载的转义 unicode 版本上进行的，使用小写十六进制数字。如果你只是计算解码的字节，你会得到一个不同的签名。例如，字符串应该转义为\u00e4\u00f6\u00e5。

来源:[截至 2017 年 5 月 3 日的脸书文件](https://developers.facebook.com/docs/messenger-platform/webhook-reference#security)

**主要问题**

如果我的应用程序可以访问原始有效负载，我就可以对它进行签名并比较签名，它们应该完全匹配。

但是我没有原始有效载荷！我只能访问已经解析过的 JSON 有效载荷。

**为什么？**

**更新:** [Google](https://hackernoon.com/tagged/google) 已通过使原始体可访问来修复该问题。

在 2017 年 5 月 3 日的谷歌云功能(beta)中，它[根据内容类型自动解析请求体](https://cloud.google.com/functions/docs/writing/http)，并将其存储在 [req.body](https://expressjs.com/en/api.html#req) 下。它没有向开发人员提供原始缓冲区，也没有提供任何定制在幕后使用的 [bodyParser](https://www.npmjs.com/package/body-parser) 的方法。

经过进一步研究，我发现这个问题也被其他人重复了。

**一个可能的解决方案**

如果我们可以获取已经解析过的请求体，并反向工作来“重新生成”原始有效负载，会怎么样？然后，我们可以验证原始有效载荷的签名。

**尝试 1**

我获取 req.body 并在其上调用 JSON.stringify 来生成 JSON 字符串。此后，我签署了字符串，并比较了签名。

```
function regenerateRawPayload(req) {
  return JSON.stringify(req.body);
}
```

**尝试 1 的结果**

它对简单的“你好”非常有效，直到我尝试向机器人发送奇怪的字符，如“奥”。

**尝试 2**

我试图产生一个 JSON 有效负载的“转义 unicode 版本”,它满足下面的要求。

> 请注意，计算是在有效载荷的**转义 unicode 版本**上进行的，使用小写十六进制数字…
> 
> 例如，字符串应该转义为\u00e4\u00f6\u00e5。

**尝试 2 的结果**

我发现了 [jsesc 库](https://github.com/mathiasbynens/jsesc)，它为字符串化 JSON 和“在需要的地方”转义字符提供了一个健壮的替代品。在将“hello”和“AUC”传递给机器人并对其进行签名后，重新生成原始有效负载，从而生成正确的签名。

特别是，脸书文件提到使用“小写十六进制数字”。jsesc 库通过提供 lowercaseHex 属性来满足这一需求。

```
function regenerateRawPayload(req) {
  return jsesc(req.body, {
    lowercaseHex: true,
    json: true
  };
}
```

然而，我很快意识到产生“转义 unicode 版本”的要求太模糊了。到底哪些 unicode 字符需要转义？ASCII 字符也是 unicode 字符，它们也需要转义吗？但事实并非如此，因为我在第一次尝试中尝试了一个简单的“hello”而没有转义，并且成功了…他们的意思是 unicode-转义所有非 ASCII 字符吗？

鉴于文件没有解释脸书如何创建有效载荷的转义 unicode 版本，唯一的方法是向机器人发送各种不同的字符，并测试转义它们是否会产生正确的签名。

**尝试 3**

我向机器人发送了一个有趣角色的随机样本。

```
äöå/\/hi 你好こんにちは디제이맥йн£ ÃƒÆ’Ã¢â‚¬Å¡Ãƒâ€šÃ‚Â£ШđђбÉ╡tü³gÃ©Å°áúæø"'‘’“”&amp;`~!@#$%^&*()-_+=\|{[;:?<>]},23:59\z\b\t/‹
```

**尝试 3 的结果**

这是一次令人沮丧的经历。

当我发送整个字符串时，签名不匹配，所以我一次发送子字符串，以缩小违规子字符串的范围。

讽刺的是，罪魁祸首是 ASCII 字符，而不是奇怪的字符。

我发现的第一个违规字符是*一个正斜杠。*

我认为解决方案是 unicode-转义正斜杠。然而，没有成功。

经过多次猜测，我发现脸书在它前面加了一个反斜杠，比如“\/”。根据 JSON 规范，这似乎不是必要的，但也没有错。

在我的 Javascript 代码中，在正斜杠前面添加一个反斜杠有一些问题，因为反斜杠在 JS 中是转义字符。您必须通过使用两个反斜杠来表示一个反斜杠文字来“转义”。此外，我必须对正则表达式使用的正斜杠进行转义。

这是修订后的方法。

```
function regenerateRawPayload(req) {
  return jsesc(req.body, {
    lowercaseHex: true,
    json: true
  }).replace(/\//g, '\\/');
```

我继续测试其他字符，发现了另外三个问题:' @ '、' % '和'【T0]小写十六进制数字。

为“丙”。文件上说所有的十六进制数字都必须是小写的，但这显然违反了这一点。

这是最后的方法。

> **局限性**

那么，从已经解析的 JSON 有效载荷重新生成原始有效载荷仍然是一项不可能的任务吗？

是的。尽管我们成功地创建了一个概念证明，可以在一些样本上工作，但这仍然是徒劳的。只要脸书不告诉我们它是如何产生有效载荷的“unicode 转义版本”的，我们就没有信心完美地重新生成它。我们不想仅仅因为我们的再生不正确就拒绝合法的请求。

```
function regenerateRawPayload(req) {
  return jsesc(req.body, {
    lowercaseHex: true,
    json: true
  }).replace(/\//g, '\\/')
    .replace(/@/g, '\\u0040')
    .replace(/%/g, '\\u0025')
    .replace(/</g, '\\u003C');
}
```

让我们看一个简单的例子，为什么这是一个不可能的任务。

**解决方法**

既然我们无法验证签名，我们还能做什么来确保请求来自脸书呢？

如果我们使用具有足够随机熵的函数名，即创建具有足够随机(但合法)字符的函数名，则包含随机函数名作为资源的完整 https 端点不应该被其他方猜到。也没有理由公开共享端点 url。

```
// Calling JSON.parse and then JSON.stringify
// on both payloads produces the same output
// but the original payloads clearly differ.
var payload1 = '{"foo":"bar"}';
var payload2 = '{"foo": "bar"}';
```

当使用这样的端点时，有理由相信所有请求都仅来自脸书。其实这就是 [Telegram 的 setWebhook](https://core.telegram.org/bots/api#setwebhook) 采取的做法！

希望谷歌能很快在其功能中提供原始有效载荷数据，或者脸书记录下它是如何创建原始有效载荷的。

我希望你能从我的经历中学到一些东西。下次见！

[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。

如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

I hope you learnt something from my experience. Till next time!

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [Hacker Noon](http://bit.ly/Hackernoon) is how hackers start their afternoons. We’re a part of the [@AMI](http://bit.ly/atAMIatAMI) family. We are now [accepting submissions](http://bit.ly/hackernoonsubmission) and happy to [discuss advertising & sponsorship](mailto:partners@amipublications.com) opportunities.
> 
> If you enjoyed this story, we recommend reading our [latest tech stories](http://bit.ly/hackernoonlatestt) and [trending tech stories](https://hackernoon.com/trending). Until next time, don’t take the realities of the world for granted!

![](img/be0ca55ba73a573dce11effb2ee80d56.png)