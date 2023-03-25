# 追踪链条:寻找以太三角洲黑客的新发现

> 原文：<https://medium.com/hackernoon/following-the-chain-a-new-revelation-in-the-search-for-the-etherdelta-hacker-a00b2a5f4e50>

![](img/bda701d4c082c11567ccdbfe80710dd4.png)

Photo by [William Bout](https://unsplash.com/@williambout)

我曾经报道过 EtherDelta 黑客分两次偷走了价值数十万美元的 ETH:

1.  [https://medium . com/@ deck tonic/how-one-hacker 窃取了价值数千美元的加密货币-注入经典代码-a3aba5d2bff0](/@decktonic/how-one-hacker-stole-thousands-of-dollars-worth-of-cryptocurrency-with-a-classic-code-injection-a3aba5d2bff0)
2.  [https://medium . com/@ deck tonic/following-the-trail-what-we know-the-hacker-behind-the-ether delta-attack-9ac 6015 fc2e 1](/@decktonic/following-the-trail-what-we-know-about-the-hacker-behind-the-etherdelta-attack-9ac6015fc2e1)

到目前为止，我认为不太可能会出现任何有助于识别黑客的信息，但一些新的信息已经浮出水面，老实说，我甚至不能编造。

*首先我要感谢* [*鲍比·鲍博因*](https://medium.com/u/b5702cc58b1f?source=post_page-----a00b2a5f4e50--------------------------------) *揭露了这一点:他找到了确凿的证据，我只是转述一下。*

原来我们臭名昭著的黑客无法抗拒 ICOs 的诱惑。Bobby Bowboin 发现，其中一个用来收集 EtherDelta 黑客资金的钱包也被用来资助那个后来被用来制造[现在臭名昭著的](https://www.bloomberg.com/news/articles/2017-10-11/buyer-beware-as-70-000-goes-up-in-smoke-on-broken-ico-trade) AirSwap ICO 尝试的钱包，该尝试花费了价值 7.5 万美元的 ETH(现在价值超过 8 万美元)却以失败告终。更好的是，黑客又尝试了一次，花费了价值超过 22k 美元的 ETH，但也失败了。**想知道谁会如此鲁莽，在两笔交易中花费近 10 万美元？很明显，有人在玩偷来的钱。**

这是我在上一篇文章中提到的账户:

[](https://etherscan.io/address/0x563b377a956c80d77a7c613a9343699ad6123911) [## 以太坊账号 0x 563 b 377 a 956 c 80d 77 a 7c 613 a 9343699 ad 6123911 信息

### 以太坊区块链浏览器、API 和分析平台

etherscan.io](https://etherscan.io/address/0x563b377a956c80d77a7c613a9343699ad6123911) 

该账户创建了恶意以太坊合同，用于支持 XSS 攻击，从多个不知情的以太网用户那里窃取了价值数万美元的以太网。这也是这个钱包的资金来源:

[](https://etherscan.io/address/0xf51ec864d5fb2f184198e369fe063fc77045a3ad) [## 以太坊帐户 0 xf 51 EC 864 D5 FB 2f 184198 e 369 Fe 063 fc 77045 a3 ad 信息

### 以太坊区块链浏览器、API 和分析平台

etherscan.io](https://etherscan.io/address/0xf51ec864d5fb2f184198e369fe063fc77045a3ad) 

如果你查看交易历史，用户进入 AirSwap 白名单众卖的交易是 3.3 ETH(这是白名单参与者的限制)。因此，据推测，在某个时候，这个用户成功注册了 AirSwap 白名单，通过了 KYC(很可能是用假信息)，并在他们的系统中留下了踪迹。然后，后来，他们做了这个臭名昭著的交易:

[](https://etherscan.io/tx/0x167b6e3217536e66e69f906a457b2457c6cc4f95928a47b0443ad895b23c6e76) [## 以太坊交易 0x 167 B6 e 3217536 e 66 e 69 f 906 a 457 b 2457 c 6 cc 4 f 95928 a 47b 0443 ad 895 b 23 c 6 e 76

### 以太坊区块链浏览器、API 和分析平台

etherscan.io](https://etherscan.io/tx/0x167b6e3217536e66e69f906a457b2457c6cc4f95928a47b0443ad895b23c6e76) 

试图在 AirSwap 售罄之前进入公开销售，1，700 ETH(当时价值超过 50 万美元)和高达 7.5 万美元的交易费(这听起来很疯狂，除非你认为 AirSwap AST 令牌的交易价格甚至会略高于 ICO 价格，在这种情况下，你最终会以不错的利润出售这些令牌)。

该用户还在此处以$22k 的交易费进行了另一次失败的尝试:

[](https://etherscan.io/address/0xb2a43375d2a07f5d5c6867f139fb053ba522f51c) [## 以太坊账户 0xb2a 43375 D2 a07 F5 d5c 6867 f 139 FB 053 ba 522 f 51 c 信息

### 以太坊区块链浏览器、API 和分析平台

etherscan.io](https://etherscan.io/address/0xb2a43375d2a07f5d5c6867f139fb053ba522f51c) 

您可以看出这两封邮件来自同一个用户，因为两个钱包最终都将所有资金转移到了这个地址:

[](https://etherscan.io/address/0x85d0fdabbe633106c14268b0e62e14e5d5f6c34a) [## 以太坊账号 0x 85d 0 fdabbe 633106 c 14268 b 0 e 62 e 14 e 5d 5 f 6 c 34 a 信息

### 以太坊区块链浏览器、API 和分析平台

etherscan.io](https://etherscan.io/address/0x85d0fdabbe633106c14268b0e62e14e5d5f6c34a) 

遍历各种钱包和交易，我还发现了几个钱包对 AirSwap 白名单销售的多个贡献，对 Request Network、ETHLend 的多个贡献，对 18.05 ETH 的 Cindicator 的一个贡献(另一个白名单销售，可能是 Tier_3 cap？)等。等等。在这一点上，似乎这个黑客可能不止一个人…但是谁知道呢？最后，该用户/团队向该合同发送了 4，000 ETH:

 [## 以太坊账号 0x 3 bfc 20 f 0 b 9 afcace 800d 73d 2191166 ff 16540258 信息

### 合同源代码副本查找相似的合同//sol Wallet //多签名、每日限制帐户代理/wallet。//…

etherscan.io](https://etherscan.io/address/0x3bfc20f0b9afcace800d73d2191166ff16540258) 

其中包含一系列内部交易:

 [## 以太坊账号 0x 54 a2 d 42 a 40 f 51259 dedd 1978 f 6 c 118 a 0 f 0 eff 078 信息

### 合同源代码副本查找相似的合同//！版权对等技术，2017。//!发布于…

etherscan.io](https://etherscan.io/address/0x54a2d42a40f51259dedd1978f6c118a0f0eff078) 

这显然是波尔卡多特公司的合同地址。

用鲍比自己的话说:

> 这 4000 个 ETH 是在官方 ICO 日期之前的过去 30 天内发送到 0x 3 bfc 20 f 0 b 9 afcace 800d 73d 2191166 ff 16540258 的超过 230，000 个 ETH 的一部分(可能是 Polkadot 令牌预售的一部分？).有趣的是，Polkadot ICO 需要护照识别步骤(通过 picops.parity.io)，但我不确定黑客会傻到用自己的护照进行 pico 验证。

现在，我想在这里说得非常清楚:我非常怀疑，我的意思是，我认为这是 100%不可能的，这个黑客使用他们自己的 id 参与白名单令牌销售。更有可能的是，他们利用偷来或购买的身份证照片冒充他人通过 KYC 的控制(在某些情况下，多次)。因此，虽然这些 ICO 销售人员可能从这个人那里收集了一些信息，但我认为披露这些信息没有任何用处，因为这很可能会导致识别出错误的人(我无意鼓励互联网私刑)。

我真正希望的是，通过公开发布这些信息，如果有人认为这里有一个值得追查的法律案件(可能涉及执法机构，尽管我不知道这将如何运作)，那么这些信息，甚至只是电子邮件或 IP 地址，将能够为这个过程提供信息。

*我还必须在这里提到，这是区块链力量的一个完美例子。由于有了公开、可核实和永久的分类账(在本例中为 Ethereum)，我们能够跟踪此人的行为并披露大量信息。*

最后，在发表这篇文章之前，我已经和来自 AirSwap 和 EtherDelta 的代表谈过了。

AirSwap 的团队解释说，此人在多次尝试参与白名单令牌销售后被从白名单中删除(我查看了使用的多个钱包，发现仅 3 个钱包就有 40 次尝试参与白名单销售)。这也可以解释为什么这个人 50 万美元的捐款尝试失败了。无论如何，TL；博士在这个问题上是，你不应该感到抱歉，无论是谁花了 7.5 万美元，在一个单一的失败的交易，因为他们是一个小偷，这是所有被盗的钱。

EtherDelta 对这个人无能为力，因为他们没有比我更多的信息。

此时，我的问题很简单:谁能够有效地利用这些信息？是否有执法机构或类似机构会继续调查类似的案件？如果提起法律诉讼，有很多信息可以提供……如果你有想法，请告诉我。

感谢您的阅读，并在评论中畅所欲言。

*附言:我碰巧持有一些空对地游戏代币。*

喜欢我的工作吗？留下小费以示感谢:

BTC:1 pkqkngzpufmxaytzlg 1q 6 e 5xusppfnbg

ETH:0x 3c 83504968 a5fd 197 E3 af 58 e 36153 ed 3502 b 072 e

DOGE:dpwkqr 5 rhwcceyadsxvhnzwmamhbs 5 ip5g

谢谢！