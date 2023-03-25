# 宣布 ERC-20 运输机——现在任何人都可以发送 ERC-20 代币而无需持有 ETH

> 原文：<https://medium.com/hackernoon/announcing-the-erc-20-transporter-now-anyone-can-send-erc-20-tokens-without-holding-eth-1c1aae361e89>

![](img/4ab994bb67d02dc7ccaa1128858ab909.png)

- Gaze does not claim to have any rights to this image -

**BokkyPooBah 的令牌传送服务智能合约**

虽然凝视硬币网络还没有准备好黄金时间，但其首席技术官 Bok Khoo“BokkyPooBah”已经开发了一种 ERC-20 隐形传态服务，使用户能够在不持有 ETH 的情况下发送 ERC-20 代币。POC 旨在为没有加密货币的虚拟现实用户解决一个问题，它可以用于其他基于令牌的应用程序，参与者以前必须持有 ETH 才能使用它们

# 什么是令牌传送服务智能合同？

在一个将虚拟世界与以太坊区块链整合的项目中，该系统测量虚拟世界中的“凝视”，并使用 GazeCoin token(GZE)来促进内容制作者、广告商和消费者之间的小额支付，几年前我试图在比特币网络上实现这一点，但事实证明太难了。

我从以太坊的经验中知道，创建加密安全的可交易令牌的最简单的方法是在以太坊网络上，在那里可以快速、廉价地部署 [ERC-20](https://github.com/ethereum/EIPs/blob/master/EIPS/eip-20-token-standard.md) (以太坊令牌标准)合约。但这并不是让在虚拟现实环境中使用代币成为美好体验所需要的全部。

漫游在 GazeCoin 虚拟世界的用户可以赚取和消费 GZE。问题是，全球不到 1%的人口是加密货币的用户，因此很大一部分 VR 用户不会持有它们。

因此，BokkyPooBah 的令牌传送服务智能合同(BTTS)是一个允许非加密用户使用加密令牌的解决方案的概念验证。详情可以在 [(BTTS) GitHub 仓库](https://github.com/bokkypoobah/BokkyPooBahsTokenTeleportationServiceSmartContract)找到。

GazeCoin GZE token 合同将采用 BTTS 技术建造，因此这是其实际应用的最佳范例。

用户创建一个帐户，然后备份 12 或 24 个单词的助记符种子短语。助记种子短语将允许在他们的钱包内创建一个或多个以太坊地址。然后，用户可以在这些以太坊地址中持有 GZE 令牌。

GZE 令牌合同将建立智能合同功能，允许用户的地址签署指令，允许另一个以太坊地址代表用户转移 GZE 令牌。这一可选功能建立在 ERC20 令牌合约的基础上，因此正常的以太坊 ERC20 令牌传输将是用户的退路。

用户的钱包将制作指令，并使用私钥对指令进行加密签名。该指令然后被发送到 BTTS 服务提供商，然后他们代表用户执行令牌传输。

用户用 BTTS 服务提供商收到的 GZE 令牌支付交易费用。这是为了补偿 BTTS 服务提供商为执行用户的 GZE 加密令牌传输而支付的 ETH 交易费。BTTS 技术如何工作的技术细节在[如何工作](https://github.com/bokkypoobah/BokkyPooBahsTokenTeleportationServiceSmartContract#how-it-works)中进行了总结。

如果用户选择，他们仍然可以转移 GZE 令牌，同时直接支付 ETH 交易费。BTTS 只是一项可选的附加技术，用于简化 GZE 令牌传输过程。BTTS 是第一个让用户轻松加入 GazeCoin 平台的解决方案，我相信其他开发者也会发现它对自己的应用程序有用。

# 构建分散的沉浸式经济

随着我们开始将 GazeCoin 推向一个去中心化的沉浸式经济，粉丝和观众从一个去中心化的世界转移到另一个世界，我们的交通系统允许他们从一个世界到另一个世界交换代币，并购买数字商品，BTTS 协议的重要性变得更加重要。

不要忘记查看 GitHub 中的[源代码和文档，或者如果你有任何问题要问我，请跳到](https://github.com/bokkypoobah/BokkyPooBahsTokenTeleportationServiceSmartContract)[电报](https://t.me/GazeCoinPublic)。

**关于作者**

Bok Khoo，“BokkyPooBah”是一位经验丰富的以太坊开发者和凝视币 CTO。博克制造了密码衍生品。2016 年 11 月，市场分散了无信任以太/令牌交换合约市场，熟悉了以太坊区块链上与智能合约交互的一些问题，并开始寻找解决方案。