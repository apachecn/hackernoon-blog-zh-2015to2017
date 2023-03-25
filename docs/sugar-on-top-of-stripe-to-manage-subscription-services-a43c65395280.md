# Sugar 在 Stripe 之上管理订阅服务

> 原文：<https://medium.com/hackernoon/sugar-on-top-of-stripe-to-manage-subscription-services-a43c65395280>

在过去的几年中，许多在线企业已经采用了基于订阅的商业模式。从音乐会员订阅到每月的刀片盒，订阅经济已经成为一种趋势。但是，找到合适的软件来处理这种商业模式目前是一个痛苦。在本文中，我将解释运行基于在线订阅的 gig 所必需的功能。

![](img/111406a1aeacb972867fd6306139c6bc.png)

ServiceBot and Stripe build the perfect subscription-base business solution

# 条带订阅

如果你必须处理任何类型的在线支付，你需要一个支付网关，而 [Stripe](https://stripe.com) 是最强大的在线支付网关。基本上，它处理在线信用卡、银行账户转账、Apple Pay 等。它集成了几乎所有的电子商务解决方案，如 Magento、Shopify、WooCommerce 等，并使用其强大的 [API](https://stripe.com/docs/api) 。我是你的超级粉丝。

[Stripe](https://hackernoon.com/tagged/stripe) 有一个内置的订阅计费系统，理论上工作如下:

* **资金来源令牌。**对于任何类型的资金，包括信用卡和银行转账， [Stripe 都会创建一个令牌](https://stripe.com/docs/stripe.js#stripe-create-token)。你输入信用卡，Stripe 输出一个令牌 id。
* **给客户附上代币。**一旦获得令牌 id，您[将其附加到客户对象](https://stripe.com/docs/api#create_customer)。客户对象只是使用电子邮件地址创建的。
* **付款计划。**付款计划一目了然。[付款计划](https://stripe.com/docs/api#create_plan)定义价格、计费间隔(每月/每年/每天)、货币和订阅计划的名称。
* **为客户订阅计划。创建订阅就像将客户附加到付款计划一样简单。就是这样！**

# 条纹上的糖

Stripe 确实很好地处理了订阅计费，但是运行基于在线订阅的业务还需要一些额外的功能。让我们浏览一下这些功能:

**客户自助服务门户** —我相信你以前用过高级订阅系统。例如，您的无线提供商的在线帐户。您登录您的帐户，支付您的每月帐单或设置自动付款。您可以通过您的在线帐户查看您的发票，请求取消，并与您的无线公司沟通。如果你的工作也有同样的功能不是很好吗？客户购买您的服务，登录他们的帐户并管理他们的订阅，您管理您提供的服务的生命周期。Stripe 不为您的客户提供自助服务门户。

**网站目录列表** — Stripe 不为您的服务列表提供公共网站。为了有一个前端网站，列出你所有的条纹支付计划，你需要一个第三方电子商务应用程序。然而，大多数第三方应用程序都是为产品销售量身定制，需要定制和额外的插件来准备一个基于订阅的网站。

**高级结账系统** —如果您的 gig 在服务请求期间有问题需要客户回答，该怎么办？您应该能够为您的每项服务创建输入字段或高级请求表单。此外，您不希望您的客户经历一个帐户创建过程。您希望他们购买您的服务，并自动获得一个在线帐户来管理他们购买的订阅。简单易行的结账流程！

# 开源解决方案

理想的解决方案是利用 Stripe 的 API 来构建一个满足上述所有特性的平台。在过去的八个月里，我和我的团队花了[的时间来为基于在线订阅的音乐制作一个](https://servicebot.io/team)[开源](https://hackernoon.com/tagged/open-source)工具(Github 链接:[https://github.com/service-bot/servicebot](https://github.com/service-bot/servicebot))。它叫做[服务机器人](https://servicebot.io)。如果你还没有看过我最初的描述这个故事的博客文章。

ServiceBot 与 Stripe 紧密集成。事实上，为了建立一个新的 ServiceBot 网站，您需要的只是 Stripe API 密钥(它带有一个 Stripe 帐户)。我们没有重新发明轮子，Stripe 负责所有付款。你也不应该重新发明轮子，ServiceBot 会开箱即用地处理你的基于订阅的在线工作。

ServiceBot 是一个开源 Node.js 应用程序。它使用了一些最流行的库，比如 Express、React、Redux 等等。

在有限的时间内，我们提供免费的开放测试版服务机器人实例。我们为托管付费，因此您可以使用 ServiceBot，并向我们反馈如何进一步改进它。你可以通过我们的网站 [https://servicebot.io](https://servicebot.io) 注册一个公测实例。设置您自己的在线订阅服务机器人网站应该不到两分钟！

与我们分享您的 ServiceBot 网站，我们将展示它！

# 然后

在下一篇文章中，我将挑选一个特定的 ServiceBot 特性并详细解释它。