# 以太坊合约容易受到哈希表中毒攻击吗？

> 原文：<https://medium.com/hackernoon/are-ethereum-contracts-vulnerable-to-hash-table-poisoning-attacks-a4d9241e16c4>

**TL；博士**:没有。但是理解其中的原因很好，说服自己也教会了我更多关于以太坊的知识。

在我的前雇主[有一个长期流传的笑话。在某些时候，一些新员工会抱怨 Java 的 HashMap 类是不确定的，而我的同事会笑着附和说“是的，很抱歉，这是我的错”，我们都会开心地笑。笑话是我的同事](https://www.twosigma.com/)[在读博士期间开创了针对哈希表之类的 DoS 攻击](http://taz.newffr.com/TAZ/Reseaux/Techniques_Attaques/dos/CrosbyWallach_UsenixSec2003.pdf)，这导致 Sun 对 Java 的实现进行了修改(其副作用是使 HashMap 上的迭代变得不确定)。

但是尽管这个发现已经有 15 年了，它仍然有点神秘。(正如插科打诨的频率所证明的那样，很少有公司能像 TS 一样对新员工如此挑剔。)所以当我看到 [Solidity](https://solidity.readthedocs.io/en/develop/) (最常用于编写[以太坊](https://hackernoon.com/tagged/ethereum)契约的脚本语言)有一个内置映射类型的[，却没有提到实现细节时，我想知道“使用映射的 Solidity 契约容易受到这种中毒攻击吗？”谷歌返回了关于这个话题的 bupkis，所以我在儿子看](http://solidity.readthedocs.io/en/develop/types.html?#mappings)[自然猫](http://pbskids.org/naturecat/)的时候深入研究了一下。

## 什么是哈希表中毒攻击？

众所周知，哈希表的平均查找时间为 O(1+ε)，但哈希表冲突会将性能降低到 O(n)。如果您知道哈希算法，您可以向它输入会导致其性能下降的特定输入。这在以太坊合同中是非常危险的。从一个天真的观察者的角度来看，你可能会导致合同过早地达到气体限制，可能会永久地影响合同中使用地图的任何行为，或者如果气体限制没有考虑到地图查找的真实成本，则影响采矿本身。

通常，语言在映射实现中使用非常弱的哈希算法，通常围绕梅森素数 2–1。(和固定精度的整数一样，质数总是在循环集内乘法生成。)这是速度所必需的。语言实现通常通过为哈希算法随机化一个 salt 来保护自己免受这种影响(因此 Java 的 HashMap 类具有不确定性)。但是这怎么能在以太坊工作呢？合同的执行必须是完全确定的。

人们可以想象以太坊试图通过使用更好的哈希算法(比如 SHA3)来解决这个问题，但代价是执行速度。但这仍然是可利用的。几乎所有的哈希表实现都采用哈希值模数(内部存储数组的大小)来获取内部位置。即使使用 SHA3，如果有一个默认容量或者说 16，您也有 1/16 的机会生成一个 SHA3 散列，将一个实体放入一个特定的桶中。虽然难度随着容量的增加而增加，但几乎可以肯定的是，容量大小与散列成本之间存在一个最佳平衡点，以完成一份在经济上仍然可行的合同。让我们保守地说，在一个 8 字节的 nonce ( [src](https://bench.cr.yp.to/results-sha3.html) )上计算 SHA3 需要每字节 200 个 CPU 周期。在我的普通桌面上，这仍然是～2M SHA3/s。这将允许我创建一个 100，000 条目的哈希表，其中所有条目都在大约 100，000 秒的时间内处于同一槽中。1 小时。当然，让以太坊合约在预期为 O(1)的地方进行 O(100，000)运算是可以利用的！是时候找到 Solidity 的哈希表实现了。

## “映射”在 Solidity 中是如何工作的？

文档什么也没给你。让我们看看代码:

```
$ git clone https://github.com/ethereum/solidity
$ grep -Ri mapping solidity
```

该死，没那么容易。；)我就不赘述细节了，但是根本不清楚 Solidity 是在 Solidity 代码库的什么地方(或者是否)实现哈希表。相反，让我们来看看由一个小契约生成的程序集:

```
pragma solidity ^0.4.0;
contract TestMapping {
  mapping(uint => uint) aMap;
  function doit() { aMap[10] = 20; }
}
```

给你:

```
JUMPDEST 		function doit() { aMap[10] = 2...
PUSH 14			20
PUSH 0			aMap
PUSH 0			aMap[10]
PUSH A			10
DUP2 			aMap[10]
MSTORE 			aMap[10]
PUSH 20			aMap[10]
ADD 			aMap[10]
SWAP1 			aMap[10]
DUP2 			aMap[10]
MSTORE 			aMap[10]
PUSH 20			aMap[10]
ADD 			aMap[10]
PUSH 0			aMap[10]
SHA3 			aMap[10]
DUP2 			aMap[10] = 20
SWAP1 			aMap[10] = 20
SSTORE 			aMap[10] = 20
POP
```

这看起来一点也不像哈希表存储操作！这是怎么回事？看起来我们用 SHA3 散列密钥，但是没有做任何查找，只是依赖 SSTORE。EVM 在做什么？

在这之前，我没有考虑过 EVM 内存管理。在某种程度上，我想我只是假设它像任何其他 VM 中的任何其他类一样工作。一个 struct + function 表在一个很小但可增长的地址空间里。可变大小的结构，如列表或映射，有一个默认的固定大小，并以某种幂函数自动增长，其ε成本被挖掘代码平均或吞噬。但那完全不是真的。你的合同可以访问一个巨大的 2 ⁵⁶位虚拟地址空间。SSTORE 获取您的密钥的 SHA3，将其附加到您的契约的 SHA3，并获取其中的 SHA3，然后它成为您的值的内存存储位置。这太神奇了，因为这是我见过的第一个映射实现，它通过牺牲几乎无限的内存地址空间，在所有情况下都提供了真正的 O(1)查找(从 EVM 字节码的角度来看)。整个 EVM 内存空间变成了一个极其稀疏(并且全局共享)的哈希表数组！

这也解释了关于实度映射的其他奇怪之处。例如，你不能在 Solidity 中迭代一个映射，根据 Google/StackExchange 的判断，这是一个常见的混淆点。对哈希表的迭代通常包括对其内部数组的迭代，跳过空单元格。当您的表只有大约 25–75%满时，这种方法很有效；当你有对象时，你最多迭代 4 倍的内存位置。但是迭代 2 个⁵⁶地址显然是不可行的。同样，假设结构映射总是让结构初始化，这一事实使得*在这个上下文中更有意义。EVM 不会将指向某个堆的指针保存在某个连续的地址空间中——该结构只是覆盖在一个全为零的地址上，其大小无关紧要。*

## 那么 EVM 如何给你一个 2 ⁵⁶的内存空间呢？

很明显这不是 malloc it。；)我在这里没有做太深入的研究，只知道 EVM 使用自己的内部关联数组跟踪内存就够了。这是有意义的，并且是存储大型/稀疏矩阵的一种非常常见的技术，EVM 的存储空间可以被认为是一个巨大的 256 x 2 ⁵⁶位矩阵。

这一切让我相信，个人以太坊合约是不容易受到哈希表中毒 DoS 攻击的。通过使用安全散列函数，而不是将散列空间减少到固定的数组大小，在散列表中查找冲突与在 SHA3 本身中查找冲突的难度相同。

## EVM 的内部关联数组可以被利用吗？

这个我不确定。我不知道 EVM 是否在使用它自己的内部哈希表，在它自己的内部映射中的那个巨大的地址空间中存储数据。但即使是，在实践中也会非常困难。显然，2 ⁵⁶位内存空间正在缩小到你的电脑内部。我找不到任何关于全球以太坊国家规模的统计数据，但它显然足够大，以至于花费足够多的气体来写足够多的数据来毒害它将是成本过高的。可能不是——我可以在这里看到一个基数树的强有力的例子。但是那不在这次调查的范围之内。

反正就是这样！

![](img/a8bdc83f4face05331e5486315eb45ef.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)