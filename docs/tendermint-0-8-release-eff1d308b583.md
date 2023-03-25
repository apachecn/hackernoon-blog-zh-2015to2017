# Tendermint 0.8 版本

> 原文：<https://medium.com/hackernoon/tendermint-0-8-release-eff1d308b583>

*注:感谢我们的* [*团队*](https://tendermint.com/about)*[*社区*](http://forum.tendermint.com:3000/) *，特别是* [*伊森·布赫曼*](https://twitter.com/buchmanster) *为使这个版本成为可能所做的辛勤工作(老实说，还有这篇文章的大部分内容)。**

*Tendermint 0.8.0 是四个月工作的高潮，它提供了许多新功能，并大大提高了网络部署的稳定性，无论网络部署规模大小。*

*更多细节参见 [v0.8.0 发行说明](https://github.com/tendermint/tendermint/releases/tag/v0.8.0)。*

***Tendermint 和应用之间的接口，以前的 *Tendermint Socket 协议* (TMSP)，现在叫做*异步区块链接口*(**[**ABCI**](http://github.com/tendermint/abci)**)。***

*Tendermint 的独特优势之一是它能够支持用任何编程语言编写的应用程序。它通过采用一个套接字协议来实现这一点，该协议被称为 *Tendermint 套接字协议*，或 TMSP。虽然我们已经习惯了首字母缩写词 TMSP，但我们将名称改为更后端不可知的“ABCI”(异步区块链接口的简称，或者原子广播接口的简称)，以强调其目的是作为任何 BFT 共识引擎和任何应用程序之间的通用接口。通过此次更名，我们向机构群体发出信号，表明我们致力于设计有益于整个区块链生态系统的协议。我们期望在未来的中看到其他共识引擎采用 ABCI，无论它们是基于 Tendermint、Raft 还是任何其他容错共识算法。*

***ABCI:在建工程，带插座，还是带 GRPC***

*在 Golang 中实现 ABCI 的应用程序可以继续用 Tendermint 编译成一个二进制文件。用 Python、Java 或 JavaScript 等其他语言编写的应用程序可以通过网络或 Unix 套接字与 Tendermint 进程连接。有了 0.8.0，应用程序现在还可以通过谷歌的 GRPC 公开 ABCI 端点，这可能会为各种各样的语言提供更快的原型。*

*对于需要高吞吐量的非 Golang 应用程序，我们鼓励开发人员使用 ABCI 套接字，因为事务可以按顺序异步推送到应用程序。由于 GRPC 的设计限制，GRPC 上的 ABCI 以同步方式传递消息，因此它应该只用于最初的概念验证，或者不需要高事务吞吐量的应用程序。*

*更多详情，请参见我们的[应用程序开发指南和 ABCI](https://tendermint.com/docs/guides/app-development) 。*

***坚持和握手***

*Tendermint 的早期版本没有很好地处理应用程序状态持久性。也就是说，当一个 Tendermint 节点重新启动时，它必须重新运行本地存储的[区块链](https://hackernoon.com/tagged/blockchain)的所有事务，以恢复同步。这对于我们的用户来说是很好的概念验证构建，但是很明显这对于生产系统来说是行不通的。*

*在 0.8.0 版本中，我们实现了一个 ABCI 握手协议，让应用程序告诉 Tendermint 它已经知道哪些块。因此，在重新启动任一 Tendermint、应用程序或两者时，只需要将最近的块推送到应用程序。这标志着一个重要的里程碑，*允许 Tendermint 用于实际生产环境。**

*事实上，在 2016 年末，我们成功完成了一个投票区块链应用的生产部署，使用了 [Votem](https://votem.com/) 。这是该软件的第一次生产运行，运行顺利，在 AWS 上的 8 节点集群上两个月内处理了超过 150 万次投票。*

***宇宙和基础硬币***

*“区块链互联网”Cosmos 是一个公共和私人区块链的网络。Cosmos 的基本公共区块链，包括 Cosmos Hub 和 Cosmos DEX(分布式交换),是由 Tendermint 支持的 ABCI 应用程序。忠于良好的软件工程原则，我们打算用更小的模块来构建这些应用程序，我们称之为基础币。*

*Basecoin 是一种简单的多资产加密货币，为最终成为 Cosmos Hub 奠定了基础。这是一个用 Golang 编写的 ABCI 应用程序，具有类似以太坊的帐户和简单的插件架构，非常容易扩展。它的目的是作为其他实现者的基线加密货币，以及在更复杂的状态机中支付费用的基本模块。所以，如果你想在 Golang 中创建一个新的具有实验特性的公共加密货币应用程序，或者创建一个需要用户付费的私人/财团区块链应用程序，请考虑从 Basecoin 开始！*

*关于 Basecoin 的设计和使用的更多细节，请看我们最新的博客文章。*

***嫩薄荷路线图***

*下面是我们接下来要讨论的一些项目:*

*   *更新 ABCI 以包括投票信息(例如双重签名的证据)*
*   *更新 ABCI 以支持轻型客户端查询*
*   *实现事务索引和查询*
*   *保护 RPC 服务器*
*   *更好的 Basecoin 指南、文档和工具*
*   *将区块链间通信(IBC)实现为 Basecoin 插件。*

*要了解更多信息，请查看[我们的 GitHub 问题](https://github.com/tendermint/tendermint/issues)，在 [Twitter](http://twitter.com/tendermint_team) 上关注我们，或者直接在[我们的 Slack](http://forum.tendermint.com:3000) 上向我们提问。*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*