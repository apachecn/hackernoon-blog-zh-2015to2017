# 关于以太坊和 Javascript 中数字的一个注记

> 原文：<https://medium.com/hackernoon/a-note-on-numbers-in-ethereum-and-javascript-3e6ac3b2fad9>

![](img/cbd8cd3b5dd5fe03d60c0617f610eb32.png)

在 ConsenSys，我可以和这个领域中一些最好的开发人员一起工作。我最近发表了[这篇文章](https://hackernoon.com/getting-started-as-an-ethereum-web-developer-9a2a4ab47baf)，作为新开发人员的“入门”指南，他们已经迷上了这个神奇的[以太坊](https://hackernoon.com/what-is-ethereum-c82a4b558553)生态系统，不想离开。没过多久，约瑟夫·周(Joseph Chow)就对我没有提到“s”提出了异议，此后不久，吉姆·贝里(Jim Berry)对眼前的危险做了精彩的描述。所以，我要感谢他们俩，也感谢 Aakil Fernandes 向我解释了`BigNumber`是如何工作的。

原来**我一直在用错误的方式**做事。我更新了我以前的文章，并感到有必要写这篇文章，这样我可以 A)卸下我的灵魂，B)展示*你*目前可能在 [Javascript](https://hackernoon.com/tagged/javascript) 和[以太坊](https://hackernoon.com/tagged/ethereum)之间遇到了什么问题。

这很重要，因为如果你在以太坊算错了，你可能会赔钱。

## 以太坊中的数字

令许多开发人员痛苦的是，以太坊根本不支持浮点数。我们被迫像野蛮人一样满足于`int`和`uint`(我建议你使用后者，除非你有非常好的理由使用前者)。这可能有有效的技术原因(我不知道它们是什么)，但这很好，因为我们可以将所有东西表示为整数，只需跟踪小数(回想一下，1 以太= 10**18 魏)。

## Javascript 中的数字

Javascript 很奇怪。因为它是一种动态类型的语言，所以您可以遇到各种有趣的解释场景。这里相关的问题是 JS 中默认的除法行为是*浮点除法*(更确切的说是双浮点除法)。这和以太坊是不兼容的，以太坊(显然)用的是*整数除法*。

## 如何赔钱

你发现问题了吗？哦对了，Javascript 的[double]浮点数最多只能到 16 位小数，但是 1 ether = 10**18 wei。并且**交易中所有以太值必须以卫**计价。

要查看实际问题，请考虑以下情况:

```
var my_money = 10*Math.pow(10, 18);
my_money/3> 3333333333333333500
```

呃……哎呀。你刚刚给了我 167 魏。

你可能在说“我不在乎 167 卫。”这是完全合理的反应。我也不在乎 167 魏。但是，当你的一群物联网设备向 [Raiden](http://raiden.network/) 发送的每一条每日状态推送被关闭 167 wei 时，会发生什么？你的问题越积越多。

## big number——一个[非常烦人]的解决方案

与普遍的看法相反，内置的`toPrecision()`函数绝对不会为您做任何事情。所以我们需要使用一些抽象层(我推荐 [bignumber.js](https://github.com/MikeMcl/bignumber.js/) ，主要是因为这是 [web3.js](https://github.com/ethereum/web3.js/) 返回给你的)来解析我们的数字，然后对其进行操作。

所以现在我们的数学看起来像这样:

```
var my_money = 10*Math.pow(10, 18);
var my_safe_money = new BigNumber(my_money);// Still wrong
my_safe_money/3
> 3333333333333333500// This is how you do it
var less_money = my_safe_money.div(3);
```

那么你怎么处理这个奇怪的东西呢？

```
{ 
  [String: '3333333333333333333.333333333333333333']
  s: 1,
  e: 18,
  c: [ 33333, 33333333333333, 33333333333333, 33330000000000 ] 
}
```

嗯，你必须把它传来传去，用`div()`或`times()`或其他什么东西继续操作它。查看[此处](https://github.com/MikeMcl/bignumber.js/)的完整功能列表。不幸的是，如果你拿出这个值:

```
var free_at_last = less_money.toNumber();
```

你会对结果感到悲伤:

```
free_at_last
> 3333333333333333500
```

所以，是的……你必须继续做`BigNumber`手术。如果你想知道这个数字，你可以这样做:

```
less_money.toString()
> '3333333333333333333.333333333333333333'
```

还要记住，如果你自己形成交易，你必须向以太坊提供**十六进制数**，所以在把它放入交易之前，你需要做以下事情:

```
less_money.floor().toString(16)
> '2e426101834d5555'
```

当然，如果你将这种逻辑抽象到 web3 中，这将为你转换你的数字。

## 永远小心

以太坊应用程序开发人员很难找到工作(虽然不像比特币应用程序开发人员那么难——我认为这种情况仍然存在？).我们需要互相关照，小心我们正在做的事情。请始终记住，您的应用程序可能会转移资金，并采取必要的预防措施。

当然，如果你发现一个专业的提示或捕捉到一些危险的行为，报告它！

—

如果你喜欢我的文章，在 twitter 上关注我，或者去我的公司看看，因为我们做的东西很棒。

一如既往，我们公开邀请您加入[以太坊社区](https://www.reddit.com/r/ethereum/)，帮助我们征服世界。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)