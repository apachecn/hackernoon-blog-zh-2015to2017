# 选择分散存储系统

> 原文：<https://medium.com/hackernoon/picking-a-decentralized-storage-system-5f71974468ab>

![](img/274df0653c9e96f93783739f67c23089.png)

本文为 Blockchain train 期刊的第 2 部分，请从这里开始阅读:[赶上 Blockchain Train](/@pors/catching-the-blockchain-train-9a0945aab958) 。

# 一些阅读材料开始

我在了解分散式存储时发现了几篇有用的文章:

*   [分散存储:第三代网络的支柱](https://media.consensys.net/decentralized-storage-the-backbone-of-the-third-web-d4bc54e79700)
*   [HTTP 已经过时。分布式永久网络](https://ipfs.io/ipfs/QmNhFJjGcMPqpuYfxL62VVB9528NXqDNMFXiqN5bgFYiZ1/its-time-for-the-permanent-web.html)的时代已经到来
*   【Swarm 和 IPFS 有什么区别？

# 比较分散存储系统

我们分散化的博客页面需要某种分散化的托管。从头开始构建看起来需要大量工作，而且可能会导致区块链的火车相撞事故。

让我们来看看我们的项目有哪些可用之处。

我找了一下，想出了一个办法:

*   [Storj](https://storj.io/)
*   [SIA](https://sia.tech/)
*   [MaidSafe](https://maidsafe.net/)
*   [IPFS](https://ipfs.io/)
*   [ZeroNet](https://zeronet.io/)
*   [以太坊蜂拥](https://www.ethereum.org/)
*   [BigchainDB](https://www.bigchaindb.com/)
*   我是否忘记了一位重要的竞争者？请让我知道。

我还没有资格比较这些技术(还在努力赶上火车！)，但我会为每一个增加一些观察:

# Storj

从主页:

> *基于区块链的端到端加密分布式对象存储，只有您可以访问数据。*

这听起来不像是一个方便的博客，在那里我们希望内容是公开的。

Storj 看起来是个很棒的产品，有点像 Resilio Sync，只是增加了一个区块链，为在网络上购买或出租磁盘空间创造了一个市场。

# 希雅·凯特·伊索贝尔·富勒

SIA 是另一个 Dropbox 杀手，或多或少与 Storj 相同。

# MaidSafe

Storj 和 SIA 将他们的服务标榜为更快、更便宜的存储数据的方式，而 Maidsafe 通过吓唬你来推广他们的安全网络。云提供商、政府和黑客不可信是他们视频中的信息。除了名字搞笑，和上面两个服务很像。

# IPFS

现在，这是一个不同的野兽。前三个服务是`incentive platforms`，这意味着他们有自己的加密货币来支持他们的市场。IPFS 也有这样的东西， [FileCoin](https://filecoin.io/) ，但它是从 IPFS 干净利落地分裂出来的，后者更像是一个分散存储协议。

IPFS 代表`interplanetary file system`，但是他们在主页上隐藏了原来的名字。对一个严肃的行业来说太有趣了！

主页上写着:

> *IPFS 是分布式网络*
> 
> 一种对等超媒体协议，使网络更快、更安全、更开放。

IPFS 似乎是我们项目的一个很好的选择。

# 零网

ZeroNet 的主页上没有一个俗气的视频，所以我不得不真的*读*。然而，他们令人敬畏的演示比其他人的四个视频加起来要好 100 倍。

与其他公司相比，它们的运营似乎也不太像一个严肃的企业，表现出更多的理想主义开源精神。

在主页上:

> *开放、免费和不受审查的网站，使用比特币加密和 BitTorrent 网络*

哦，哦，这就是我们正在努力做的事情！竞争对手！不，让我们继续关注代码，也许我们能学到一些东西。

# 以太蜂群

在我们成为区块链开发者的旅途中，我们别无选择，只能一头扎进以太坊。我们将在下一集讨论这个问题。但是这里必须考虑他们的分散式文件存储系统，名为 Swarm。

我花了一段时间才找到这个项目的源代码，但是[在这里就是](https://github.com/ethereum/go-ethereum/tree/master/swarm)！

来自[文档](http://swarm-guide.readthedocs.io/en/latest/introduction.html#introduction):

> *Swarm 是一个分布式存储平台和内容分发服务，是以太坊 web 3 栈的原生基础层服务。Swarm 的主要目标是为以太坊的公共记录提供足够分散和冗余的存储，特别是存储和分发 dapp 代码和数据以及区块链数据。*

看起来 Swarm 是一个使用以太坊智能合约执行的激励平台，所以不是我们的最佳选择。我们必须亲自动手。我们自己以后想加个币！

# BigchainDB

从他们的网站:

> BigchainDB 是对分散式存储、处理和通信构建模块的补充。它可以与更高级别的分散计算平台和应用程序以及身份、金融资产、知识产权和侧链协议一起使用。BigchainDB 填补了分散堆栈中的一个空白。

这是一个绝妙的解决方案，好得令人难以置信。它不仅仅是分散存储，而是一个成熟的数据库。

它建立在 MongoDB 之上，是去中心化的，所以数据是不可变的，没有中央权威。

对于我们当前的项目来说，这可能有点大材小用，但是让我们继续关注它。

# 成功的分散存储系统

好吧，我实话实说，在我做这个快速调查之前，我已经选了一个

***胜利者是 IPFS！耶！***

我们稍后可能会看到的亚军:BigchainDB！

即使回想起来，IPFS 似乎是一个不错的选择:在这个项目的背景下，所有的竞争对手都有他们的“缺陷”。通过“演绎选择”，只有 IPFS 留下来了。

在选择 IPFS 的这个非常科学的方法之后，让我们希望它确实对我们有用。

让我们进入第 3 部分:[了解 IPFS](http://decentralized.blog/getting-to-know-ipfs.html)

*最初发布于*[*decentralized . blog*](http://decentralized.blog/picking-a-decentralized-storage-system.html)*。*