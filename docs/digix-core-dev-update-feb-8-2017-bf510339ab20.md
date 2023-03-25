# Digix 核心开发更新-2017 年 2 月 8 日

> 原文：<https://medium.com/hackernoon/digix-core-dev-update-feb-8-2017-bf510339ab20>

*克里斯·希区柯克(Digix 核心开发)*

我从本周开始继续我上周讨论的重构；将 Spectrum 的 redux 数据存储转换为更易管理、更高效、对开发者更友好的格式。它非常接近完成；最终确定如何高效地生成 web3-redux action-creators api，但这需要相当多的思考和实验来获得正确的结果(同时平衡在需要之前不过度优化的想法)。

但是，在周中，让我惊讶的是，蒂姆·库尔特(和他的团队)发布了奇妙的松露~~4.0~~ 3.0。太神奇了！而且，老天作证，它与我预期的 3.0 测试版完全不同(也更好)。这意味着我们现在(第一次)拥有了利用 NPM 的智能合同的适当模块化(最后，我不必在每次“以太布丁”更新或需要修补时重新发布每个合同依赖关系)。

本周我研究了新的块菌，并移植了现有的 solidity 代码库。随着大部分契约的迁移(等待一些与导入的契约相关的次要学习机会)，以及“[@ digix/truffle-light wallet-provi](http://twitter.com/digix/truffle-lightwallet-provi)der `( RIP sigmate)的创建，我对新版本 Truffle 的工作方式有了相当的了解，到目前为止，我的印象是 Tim 在将代码库分解成解耦单元方面做得非常出色。让它说:从我的角度来看，新的松露是该死的好，我预计它在未来一段时间内会相对稳定(或至少不会有巨大的检修)。

另一方面，现在我将继续在 NPM 工作一段时间，负责合同包管理。虽然 EPM 肯定会是生产包部署的一个大问题，但现在 NPM 是一个我们还不太愿意放弃的发展舒适毯。私有回购、“npm 链接”、离线开发(通过 yarn)，再加上不必处理额外的链上注册管理机构的复杂性，这些都是(对于开发而言)NPM 无法超越的。

我们现在非常高兴能够推出品牌稳定的松露，我对这种新合同模块化将实现的未来生态系统感到非常兴奋。

**每周提交报告**

# Project github . com:digix global/sigmate
-9b9d 4 ce—[maint]用` expose`
- ea278a5 — [feature]创建时不显示种子，添加` sigmate expose `命令
- 51c9e73 — [bugfix]创建正确数量的帐户
- e7e4d5f — [feature] [3.0]使用` eth-lightwallet` HD 密钥库，删除松露特定代码

# Project github . com:digix global/Spectrum
-35fe 521—[maint]删除旧的 restoreBakcup 方法(现在由 redux-persist 处理)
- 25b0121 — [maint]删除对 Acocunt 的潜在错误重新呈现检查
- d6da238 — [maint]更新网络配置面板的更好的 UI
-60b 7936—[bugfix]` status `对初始加载时未捕获的错误进行存在性检查

# Project github . com:digix global/solidity-core-libraries
-9 bf2b 97—[maint]使用最新的 light wallet-provider，在 truffle.js 中添加' prefund '
-6fa 8521—[maint]修复 gitignore
- 9223909 — [maint]移除另一个 doxity 遗留问题
-7ba 84 CD—[maint]v 0 . 0 . 8
-9f 228 aa—[maint]使用<U+1F60E>
-b 473622—【maint】移除 doxity 残留
- 9c28aa6 —合并提交' f9bb 06575 db 9112413 e 51 a 025 da 6298d 699d 27 ab '
-637123 c—【maint】使用 truffle-light wallet-provider
-f9bb 065—【maint】凹凸版本

- ef5ea7b — [maint]重新编译 math utils
-1c 536 a6—[maint]Bump NPM 版本
-1333 f68—[功能]将滞期费信息函数移入 math utils
-5f 596 cc—[maint]Bump NPM 包版本
- 06f8db3 — [maint]对函数和变量使用下划线
- a9c129d — [maint]更新以匹配最新样板文件

# Project github . com:digix global/core 2-storage-library-contracts
-1a 54 f 88—[maint]fix math utils 导入&部署到 ropsten
- 56e925d — [maint]迁移到 truffle 3.0[WIP；找出 math utils]
-8c5d 287—[maint]将实体-核心-库提升到 v0.0.5
- 1b79be0 — [maint]实体-核心-库依赖版本
- 2708973 — [maint]修复测试
-976 dc21—[功能] Digix 用户存储类型

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！