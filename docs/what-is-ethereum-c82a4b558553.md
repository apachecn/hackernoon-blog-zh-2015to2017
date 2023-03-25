# 以太坊是什么？

> 原文：<https://medium.com/hackernoon/what-is-ethereum-c82a4b558553>

我写了一些关于用[以太坊](https://github.com/ethereum/wiki/wiki)做很酷的事情的文章，但是我发现有些人不知道我在说什么。如果你偶然发现了我的一篇文章，问自己“以太坊到底是什么？”，恭喜你——你正是我想要接触的**观众！所以我要花一分钟的时间来描述这种奇怪的技术是什么，它能为你做什么。**

# **技术解释**

**如果你在这里，我猜你是一个相当专业的人。下面是从技术角度对以太坊是什么的描述。**

## ****加密货币****

**我猜你听说过[比特币](https://en.bitcoin.it/wiki/Main_Page)和其他[加密货币](https://en.wikipedia.org/wiki/Cryptocurrency)。如果你还没有，你应该看看它们，因为我怀疑你会发现这个话题很有趣。**

**广义来说，以太坊是一个对等网络，是加密货币网络的**扩展**。**

**因此，虽然我将加密货币网络(例如[比特币](https://hackernoon.com/tagged/bitcoin))定义为:**

> **一个对等网络利用一种叫做[区块链](https://en.wikipedia.org/wiki/Blockchain)的数据结构来创造和传播数字稀缺**

**以太坊更像**

> **一个对等网络，利用一种叫做[区块链](https://en.wikipedia.org/wiki/Blockchain)的数据结构来创建和传输数字稀缺**以及可以用图灵完整指令集更新的任意数据的公共、全局状态****

**以太坊与比特币等大多数其他加密货币网络的关键区别在于全球网络状态的表现和运作方式。**

## **UTXO vs EVM**

**比特币的状态很简单:它是一大组叫做[未用交易产出](http://gavinandresen.ninja/utxo-uhoh) (UTXOs)的东西。UTXOs 的全球集合告诉你比特币网络中每个地址的比特币余额。每当交易被[处理](https://en.bitcoin.it/wiki/Script)，比特币从一个用户转移到另一个用户，这个全局状态就会更新。值得注意的是，因为比特币的整个状态是一组 UTXOs，所以本质上没有“用户”——只有持有比特币的地址。**

**以太坊的状态要复杂得多，因为它可以用一组更健壮的操作码来操作，这些操作码组成了[以太坊虚拟机](http://solidity.readthedocs.io/en/develop/introduction-to-smart-contracts.html) (EVM)。这个指令集实际上是图灵完整的，每条指令[定价不同](http://ethereum.stackexchange.com/questions/52/how-were-gas-costs-chosen-for-the-ethereum-virtual-machine-instructions)。考虑到这种设置，以太坊使用*账户*来代表用户。账户和数据都是组成以太网的全局状态的子集。**

# **以太坊能做什么？**

**加密货币正在成为互联网的一种支付方式。任何用户都可以在未经许可的情况下移动数字稀缺单元(其真实世界价值由市场决定)。*密码术确保只有拥有这些数字稀缺单元的用户才可以移动它们。这就是互联网本应发挥的作用——作为一个自由开放的平台，任何人都可以通过它与他人联系，而无需通过中介。***

**以太坊优于比特币或该领域其他任何东西的地方在于它对开发者的友好。说到开发者工具，以太坊绝对是最先进的加密货币技术。我曾经写过如何将以太坊整合到典型的 web 开发人员工作流程中，以及它实际上并不像你想象的那么痛苦。**

**因此，使用以太坊，您可以轻松地将以下任何内容添加到您的 web 应用程序中:**

*   **任何用户向任何其他用户付款**
*   **用户向您支付的款项**
*   **带有时间戳的不可变记录**
*   **[无密码登录和自动用户管理](https://hackernoon.com/never-use-passwords-again-with-ethereum-and-metamask-b61c7e409f0d)**
*   **你自己的货币和可编程的货币政策(我还没有写这个，但它即将到来)**
*   **很多其他我没想到的很酷的东西**

# **今后**

**以太坊还处于起步阶段，但它正在快速成长和成熟。我们在[以太坊社区](https://www.reddit.com/r/ethereum/)的许多人相信，这是我们这一代人的决定性技术发明，它将以一种深远的方式影响许多(如果不是所有)主要行业。如果你觉得这东西有趣，我建议你做一些研究。[这里](https://github.com/ethereum/wiki/wiki)是一个好的起点。希望这篇短文能激起你的兴趣。**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**