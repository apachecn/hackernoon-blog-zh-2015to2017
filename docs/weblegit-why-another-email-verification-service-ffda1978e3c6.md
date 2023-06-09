# Weblegit —为什么是另一种电子邮件验证服务？

> 原文：<https://medium.com/hackernoon/weblegit-why-another-email-verification-service-ffda1978e3c6>

我一直渴望把它写下来。最近我向我的一个朋友讲述了来自 https://weblegit.com[的电子邮件](https://weblegit.com)[验证](https://hackernoon.com/tagged/validation)服务。他的第一个问题是——这有什么特别的？他列出了他过去使用过的几个电子邮件验证服务，以及还有很多其他的服务。这让我笑了，因为我期待这些问题。当有人问你已经预料到的问题时，这有助于你确认你的想法。

在回答他原来的问题之前，我问了他两个问题:

1.  您使用过或见过批量或实时电子邮件验证服务吗？
2.  你见过用户注册、联系我们或订阅我们页面的电子邮件验证吗？

他的回答也正是我所经历的。**大多数电子邮件验证服务用于在发送新闻信之前批量处理电子邮件**。其次,**没有任何网站使用电子邮件验证来注册、联系我们或订阅页面。**

![](img/e78c8e28ce5dea8cefda864eeb146d39.png)

# 最具讽刺意味的是，我们检查的所有电子邮件验证服务都没有将电子邮件验证用于他们自己的注册和联系我们页面。

有些服务确实提供了实时 API，但是作为一名工程师，我知道任何代码集成都有隐藏的和持续的成本。API 密钥绝对不能存储在客户端，即使在服务器端，它们也需要一些敏感的处理。不管怎样，API 集成涉及到维护。

接下来我问我的朋友他有没有个人网站，他确实有一个。然而，由于他无法访问他的代码，我向他展示了如何在我的机器上用一个简单的静态 HTML 站点集成 Weblegit 电子邮件验证和他们的站点。他被这样一个事实震惊了，即使是静态站点也能做到这一点。鉴于**集成只需要一行代码**，他对它的安全性更加好奇。我确实向他透露了所有的细节，甚至告诉他如何才能黑掉这项服务。但是，因为我已经知道了这一点，所以我实施了一个非常好的安全措施来防范它。考虑此类解决方案时，保护平台和应用程序的经验非常有用。

Ease of integration with [Weblegit](https://weblegit.com) Email Validation service

我仍然记得当他看到整合在行动时的表情，那确实是值得的。

现在是关于准确性的更难的部分。他对结果的准确性提出了一些疑问。好事情是，我们一直在努力改进我们的算法和代码，以获得更好的准确性和性能。尽管咒语是尽早接触你的客户，我们决定花时间(4 个月)直到我们有一个好的产品展示。我的 validator 朋友先从一些简单的开始，然后转向更难的，看到与并行窗口中的其他电子邮件提供商相比更好的结果，真的很惊讶。

快结束时，我问了他一个最重要的问题——怎样才能让他成为顾客？

![](img/b93c8acb7e1f4d57304cc55eb3a08cb5.png)

How much are you willing to pay for the service ?

我不知道他花了多少心思，他最终给出了我心中价格的三分之一。这基本上意味着 Weblegit 将提供电子邮件验证的一半的价格相比，任何其他服务提供商，甚至更容易集成和隐私，安全功能。

我只用了不到半秒钟的时间就实时改变了价格，以反映我朋友的需求。我听说过寻找你的第一个客户有多难的故事，尤其是如果你没有好的销售技巧。我不得不说，我们的产品功能对我们跨越这一障碍非常方便。

![](img/95a0eee2b17c90d0464fae37640cdda9.png)

做工程师有其自身的缺点。编写代码和逻辑比记录事情要容易得多，尤其是当这不是设计或代码的时候。这一切都发生在几周前，我答应我的朋友要写下这个故事。他一直在焦急地等待它与他的其他朋友分享。今天，我终于跨越了那个障碍。

如果你的产品和其他产品一样，就很难区分。让我们保持动力的是，它不仅仅是另一个电子邮件验证服务。我们的目标是使电子邮件验证足够简单和负担得起，以便它可以被添加到所有的注册，联系我们和订阅页面。

![](img/5b66a3ef02fb501afc8c67163071dec9.png)