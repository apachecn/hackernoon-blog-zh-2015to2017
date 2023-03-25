# Cleartrip.com 的安全漏洞

> 原文：<https://medium.com/hackernoon/security-vulnerabilities-in-cleartrip-com-37b6ae6b5d1>

## 包括整个卡细节泄露的问题

![](img/950ce411d9a9581f64d6cc5fc4d8bb10.png)

Cleartrip 是一家印度在线旅游预订公司，为印度和其他一些中东国家的用户提供机票、火车和酒店预订等服务。

我们在 Cleartrip 的公共内部 API 中发现了多个漏洞，包括-

1.  用户全部信用卡/借记卡信息泄露，包括 CVV 和卡到期日期。
2.  用户的行程和个人信息数据，包括姓名、电话号码、酒店电子邮件、航班和 Cleartrip 本地预订。
3.  失败或正在进行的交易的用户个人信息泄露。
4.  能够大量取消通过 Cleartrip 预订的所有航班。

这些漏洞已被修复。我们从 2017 年 2 月 28 日开始向 Cleartrip 告知这些问题以及我们负责任的披露时间表。Cleartrip 在修复问题的整个过程中反应迅速，我们帮助验证修复，并报告应用修复时出现的其他副作用。我们收到通知，Cleartrip 已经修复了我们在 2017 年 5 月 3 日报告的所有漏洞。这是我们最初联系他们的推文，因为我们找不到任何与安全相关的电子邮件。

一个值得注意的问题是 HTTP 头泄露了一个 URL，该 URL 包含一个连续的旅程 id，而端点本身没有受到保护。卡细节泄漏发生在一个 API 端点上，该端点应该以隐藏的形式预填充不同支付网关的卡信息。以下数据已被编辑为用户数据隐私。稍后，我们会在自己的博客上发布这些问题的详细技术细节。

> 向 API 端点*'*[*【https://www.cleartrip.com/payment/XXXXXX/redirect】*](https://www.cleartrip.com/payment/citruspay/redirect)*'发出一个 HTTP POST 请求，其中包含一个包含 merchantTransactionId 的数据，在本例中该数据只是一个整数，可以在一个循环中运行，该请求将在 HTML 表单响应中显示完整的卡详细信息。*

> *<input type = " hidden " name = " VPC _ secure hash " value = " 3a 189 D2 Abe 416355 e 604 c 371005445 AE 6 af 93 af 138 a 52 e 5 FCA 37 b 9 ff 185 ed 45 "/><input type = " hidden " name = " VPC _ secure hashtype " value = " sha 256 "/><input type = " hidden " name = " VPC _。 ><input type = " hidden " name = " VPC _****CardExp*******value = " 2110 "****/><input type = " hidden " name = " VPC _****CardSecurityCode****

****我们想再次提醒用户，假设像 PCI-DSS 这样的遵从性会使您免受攻击是不明智的。遵从的唯一目的是检查复选框并继续前进。大多数时候，合规性流程缺乏必要的时间和技能来发现可能被老练的黑客利用的漏洞。谈论“安全远离 OWASP 十大威胁”也是如此。除了 OWASP 十大安全漏洞之外，还有很多安全漏洞。****

****在 Fallible，我们的重点领域是自动检测 API 中的一般安全漏洞。我们认为，像 HTTP APIs 这样的安全漏洞需要一种技术解决方案，每个运行 web 服务的人都可以使用，而不是像 bug bounty 这样的基于流程的解决方案，这种解决方案只会使那些积极运行 bug bounty 并利用全球购买力差异等待有人报告问题的公司受益。在制造“智能 WAFs”方面已经取得了进展，但大多数尝试都没有成功。在过去的几个月里，我们花了一些时间思考这个问题，并测试了各种替代策略。****

****[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)********[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)********[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)****

> ****[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[正在接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。****
> 
> ****如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！****

****![](img/be0ca55ba73a573dce11effb2ee80d56.png)****