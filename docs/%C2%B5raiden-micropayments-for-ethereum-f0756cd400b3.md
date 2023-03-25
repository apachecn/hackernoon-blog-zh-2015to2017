# 雷电:以太坊的小额支付

> 原文：<https://medium.com/hackernoon/%C2%B5raiden-micropayments-for-ethereum-f0756cd400b3>

![](img/bb4131ce3e95a0d49298c91e14373ea3.png)

**TL；DR:虽然雷电网络还没有准备好迎接黄金时代，但它的小姐妹** [**雷电**](http://micro.raiden.network) **是一种现成的微支付解决方案，可以帮助解决当今许多用例的可扩展性和延迟挑战。**

# 雷电是什么？

雷电网络旨在为以太坊提供可扩展的、低成本的、即时的令牌传输。虽然还没有完成，但 Raiden 已经在一个更受限制的环境中提供了这些特性。

在与 Dapp 开发者的交谈中，我们注意到他们中的许多人只是想将雷电网络作为一个健壮的多对一支付渠道系统；一个服务提供商向许多经常性客户提供服务。这种系统不需要成熟的网络，而是已经从直接支付渠道中受益匪浅。针对这些，我们设计了雷电。

**介绍 Raiden** (Micro Raiden):一个健壮的框架，用于在商业应用中实现即时和免费的按使用付费支付通道技术。

[Raiden](http://micro.raiden.network) 是一个开源、易用、现成的系统，用于实现基于 ERC20 的小额支付。它使用单向支付渠道，允许双方之间频繁、快速和免费的支付。这使得提供商可以很容易地对他们的服务按使用付费。鼓励经常性客户支付甚至小额支付的应用程序可以从 Raiden 设置中受益匪浅。例子包括新闻文章的付费墙、按请求付费的 API、存储、带宽和计算的微支付、直播小费等。

Raiden 还将用于 Raiden 网络，以支付辅助助手的服务费用，如路径查找或信道监控。

# 民众

我们制作了一个雷电的互动现场演示，你可以在 Kovan Testnet 上用雷电演示令牌购买幸运饼干。你也可以看看我们的付费墙演示，它展示了一个基于雷电的网站的用户体验的基本例子。

我们的第二个演示展示了如何使用几行简单的 Python 代码将 Raiden micropayments 用于每次使用的 API 支付。

# 项目状态

雷电目前正在接受外部安全审计，很快就可以在以太坊主网上使用。我们的 GitHub 上有 testnet 版本。如果你已经迫不及待地想尝试并实现你自己的基于微支付的应用程序，请查看 GitHub 上的[源代码](https://github.com/raiden-network/microraiden)、[文档](https://github.com/raiden-network/microraiden/blob/master/README.md)和[示例](https://github.com/raiden-network/microraiden/blob/master/microraiden/microraiden/examples)或访问网站了解更多信息。