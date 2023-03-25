# 为什么产品安全性很难

> 原文：<https://medium.com/hackernoon/why-product-security-is-hard-52e3f73178>

当软件安全缺陷可以[获得超过一百万美元](https://www.wired.com/2016/09/top-shelf-iphone-hack-now-goes-1-5-million/)时，研究一下为什么构建安全软件如此困难是很有用的。

我们在安全方面的所有工作都依赖于这些困难，本文旨在收集应用程序安全中固有的细节，以便后续文章可以提供解决方案。这并不意味着失败主义。

## 写软件很难

保护软件是困难的，但即使写得稍微正确也是困难的。在查找软件错误的子类别中查找安全错误。

在[起搏器](http://www.nytimes.com/2016/09/09/business/dealbook/hedge-fund-and-cybersecurity-firm-team-up-to-short-sell-device-maker.html)、[卫星](http://articles.latimes.com/1999/oct/01/news/mn-17288)或[战舰](https://gcn.com/Articles/1998/07/13/Software-glitches-leave-Navy-Smart-Ship-dead-in-the-water.aspx)中的软件可能是完美的，但对于绝大多数软件上市时间来说，更多的功能和其他目标可以理解地放在第一位。

我们知道，作为人类，我们有可能写出完美的软件，但这真的很难，而且往往没有被优先考虑。针对质量/零错误进行优化的一个例子是 [NASA 和好奇号火星车](https://rhodecode.com/blog/70/code-review-learn-how-nasa-codes)，这很合适，因为软件中一个错误的成本非常高。 [Djbdns](https://cr.yp.to/djbdns.html) 又是一个。

## 未知的未知

> 测试显示了错误的存在，而不是不存在

——1969 年报告[中的 Dijkstra](http://homepages.cs.ncl.ac.uk/brian.randell/NATO/nato1969.PDF)

如果我们知道我们正在寻找的所有的 vulns，它将避免首先寻找的需要。

组成服务$foo 的 100，000 行代码中漏洞的数量事先是未知的。宇宙没有给我们一个衡量的标准，也没有“你已经找到了 16/27 vulns，继续找！”进度栏。

虽然编写无 bug 代码已经够难了，但如果我们成功做到了，证明无 bug 就更难了。证明一个否定的答案是困难的。NIST 有一篇关于安全+形式验证的很棒的论文，是这一领域最先进的。

没有证据不一定是不存在的证据。

![](img/10c4d64d3603784f91bb712813c6cead.png)

The potential flaws in this bridge are unknown until we seek them out

## 不一致的

甚至测量代码库中人类可发现的全套错误的过程也因谁在查看、如何查看以及[午餐吃了什么](http://blogs.discovermagazine.com/notrocketscience/2011/04/11/justice-is-served-but-more-so-after-lunch-how-food-breaks-sway-the-decisions-of-judges/#.WD_gvrIrL9Q)而不同。

我在脸书进行了一项试验，10 家安全咨询公司审计了相同的代码。我的团队已经仔细审核过的代码。所有 10 个都发现了相同的浅层错误(大约一半)，但剩下的问题遍布整个地图，包括我们自己错过的一个问题。每个人都有自己的安全知识。与性能(软件中“质量”的另一个属性)相比，度量进展是微不足道的。

不一致是概率的另一种说法。就像开采黄金一样，寻找安全漏洞需要花费精力去寻找发现的可能性，而不是承诺。

## 反对

我们并不总是知道我们的竞争对手是谁。

不是 FUD 说国家可能会积极地[尝试](https://en.wikipedia.org/wiki/Operation_Aurora)这样做。

我有时认为产品安全是一场竞赛。这是一场让我们在其他人利用安全漏洞进行危害之前发现潜在安全漏洞的竞赛。

## 额外的

持续判断创伤的严重程度是很难的。我们每周在工作中都会讨论奖励虫子赏金的问题。一个真正好的帐户接管错误抵得上 10 个不太重要的错误吗？100?任何两位安全工程师都很难就此达成一致，但这是在最有利于整体“安全”的领域优化时间投资的先决条件。 [CVSS](https://www.first.org/cvss) 是目前我们对 bug 的危险程度进行分级的最佳答案。

## 限制

尽管尽了最大的努力和巨大的预算，我们将永远无法深入审查整个代码库的安全缺陷。如果我们以某种方式在完成时做到了这一点，它将会发生显著的变化。这不是宿命论，而是事实。

## 时间表

安全性通常比您预期的时间要长。一个几年后才会有结果的决定可能会损害安全。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！