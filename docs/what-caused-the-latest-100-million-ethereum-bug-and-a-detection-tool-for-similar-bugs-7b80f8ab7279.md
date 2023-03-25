# 是什么导致了奇偶校验 multisig 钱包的意外死亡&如何检测类似的错误

> 原文：<https://medium.com/hackernoon/what-caused-the-latest-100-million-ethereum-bug-and-a-detection-tool-for-similar-bugs-7b80f8ab7279>

*[*MythX*](https://mythx.io)*的创造者伯恩哈德·穆勒(Bernhard Mueller)展示了如何检测以太坊智能合约中的漏洞。**

*11 月 6 日，一名玩奇偶校验 multisig 钱包库合同的用户[“意外”触发了其 kill()函数](https://github.com/paritytech/parity/issues/6995)，有效冻结了所有与库的代码相关联的奇偶校验 multisig 钱包上的资金。根据早期的估计，这可能会使价值超过 1 亿美元的以太网无法使用(更新:同时，这个数字已经上升到 2.8 亿美元)。*

*碰巧的是，就在几周前，我写了关于使用符号分析检测无保护自杀指令的文章。最新的奇偶校验事故是由该漏洞的一个实例引起的。在这篇博客文章中，我将检查在最近的攻击中发生了什么，并介绍一个新的 [Mythril](https://github.com/ConsenSys/mythril-classic) 分析模块，它可以用来检测 Solidity 代码和链上契约中的类似问题。*

*![](img/e35318e4e5d11407c6ab26ce14a16042.png)*

*Ethereum rendition by Shed Designs for [源代码](https://medium.com/u/6c0e21b7d1c2#code)中看到的，使用`only_uninitialized`修饰符来限制对该函数的访问:

```
/ throw unless the contract is not yet initialized.
 modifier only_uninitialized { if (m_numOwners > 0) throw; _; }
// constructor — just pass on the owner array to the multiowned and
 // the limit to daylimit function initWallet(address[] _owners, uint _required, uint _daylimit) only_uninitialized
```

这个修饰符允许钱包初始化一次。通常，当一个新的钱包被建立时，这个库函数在创建过程中被立即调用。

然而，`WalletLibrary`本身从来就不应该被用作*钱包*。它唯一的目的是通过`DELEGATECALL`为调用它的其他契约提供代码。因此，非常不幸的是，库契约实例本身从未被初始化。这是开发人员的疏忽，部分原因是以太坊混乱的库/调用语义。这个主题在某种程度上应该有自己的博文，但是现在让我们把注意力集中在特定的攻击上。

作为合同所有者，攻击者现在调用第二个事务中的`kill`函数:

```
Function: kill(address _to)
MethodID: 0xcbf0b0c0
[0]:000000000000000000000000ae7168deb525862f4fee37d987a971b385b96952
```

这终止了图书馆合同，导致所有依赖于图书馆代码的钱包不可用。

## 检测 Bug

[Mythril 的](https://github.com/ConsenSys/mythril-classic)“未检查的自杀”模块试图检测类似`WalletLibrary`中的暴露的 kill 函数。使用`-x`选项运行 mythril 时，该模块会自动执行。

要进行测试，您可以对[平价钱包](https://github.com/b-mueller/mythril/blob/master/examples/walletlibrary.sol)进行分析。遗憾的是，Mythril 的链上扫描特性不能在 dead contract 实例上使用，`WalletLibrary`代码只能用 solc 4.10 编译。让它工作的最好方法是首先在 [remix](https://remix.ethereum.org/#version=soljson-v0.4.10+commit.f0d539ae.js) 中编译代码，然后用`-c`选项将运行时字节码传递给 Mythril。您应该得到以下输出:

```
./myth -x -c "60606040(…)0029"
=== Unchecked SUICIDE ===Type: WarningThe function kill(address) executes the SUICIDE instruction.The remaining Ether is sent to an address provided as a function argument.There is a check on storage index keccac_1461501637330902918203684832716283019655932542975_&_caller. This storage index can be written to by calling the function 'initMultiowned(address[],uint256)'.There is a check on storage index 0\. This storage index can be written to by calling the function 'initMultiowned(address[],uint256)'.There is a check on storage index 1\. This storage index can be written to by calling the function 'initMultiowned(address[],uint256)'.
```

该算法的工作原理如下:

*   [列出不受 msg.sender 约束的所有`SSTORE`指令的清单](https://github.com/b-mueller/mythril/blob/master/mythril/analysis/symbolic.py)(即任何人都可以调用)；
*   寻找`SUICIDE`说明并检查每个说明的约束条件。
*   如果找到的每个相关存储索引都有一个“污点”`SSTORE`，则尝试解决包含`SUICIDE`块的基本块上的约束，以确保它是可达的。

这是一个非常简单的检查，但足以检测像奇偶校验这样的小缺陷(尽管它不是防假阳性的)。

# 关于 Mythril 和 MythX

Mythril 是一款免费的开源智能合同安全分析器。它使用符号执行来检测各种安全漏洞。

[MythX](https://mythx.io) 是一个基于云的智能合约安全服务，它无缝集成到智能合约开发环境中并构建管道。它将多个领先的安全分析流程捆绑到一个易于使用的 API 中，允许任何人创建专门构建的智能合同安全工具。MythX 兼容以太坊、Tron、Vechain、Quorum、Roostock 和其他基于 EVM 的平台。*