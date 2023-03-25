# “OMG，Java 好啰嗦，伙计们”。

> 原文：<https://medium.com/hackernoon/omg-java-is-so-verbose-guys-dca93fec9e03>

![](img/0aa28c0583435a561228ba8273ba27ac.png)

Well, it’s not Java but it looks better than any of my code photos

# 天哪，闭上你的臭嘴。

如今，当你听到有人说…

> 瘦。优雅。简洁。

…他们是在描述一个简洁的超级模特还是一种怪异的计算机语言？你的猜测和我的一样好。

所有这些都与 Java 被认为的样子形成了鲜明的对比:冗长而不优雅，企业的选择，穿着粗花呢夹克的无聊教授。

![](img/59751fba47d3bae4fc23db81ee5b4883.png)

Fuck you, you hipster.

但是请等一下:为什么冗长的语言会自动变得不优雅？为什么简洁是一种优势？

当我听到“在 Kotlin / Go / whatever 中你不必使用分号”带着如此的兴奋和释然说出时，我惊呆了。分号现在成问题了？他们*曾经*是个问题吗？

从 Go 中去掉分号意味着你必须把开始的花括号和 if- / for- / whatever-语句放在同一行，否则编译器就没用了。我没意见(反正我就是这么做的)，但是想象一下，你是一个对花括号的位置很挑剔的人，你更喜欢把左括号放在语句的下一行。呃，不能这样，但是，嘿，没有分号，阿米利特？

你知道吗？我*喜欢*分号。写完一行漂亮的代码后，我喜欢使劲按分号键。出于某种原因，多巴胺被释放出来，感觉被感受到。你凭什么拿走我的分号？

去他妈的，我要写一整篇关于分号的文章。反正我已经准备好了一个和屁眼有关的书名。

## 半结肠镜检查

我和其他人一样讨厌自命不凡和赶时髦。不，*比下一个家伙和他旁边的那个家伙和挨着*那个*家伙的那个家伙多*。我可以继续下去，但这已经越来越混乱了。

这种对省略分号的新奇迷恋让我想起了音乐行业。为什么这是赶时髦的最好表现。

任何记得地球上最后一只雷龙的老人也很可能记得海滩男孩。

> 完全披露:沙滩男孩是我最喜欢的乐队。

当沙滩男孩刚刚起步时，他们是一个音乐上不起眼的乐队，有一些很棒的和声。在有了一系列与冲浪相关的热门歌曲后，布赖恩·威尔逊(他们的领导者和主要词曲作者)开始对他的歌曲创作和制作技术越来越雄心勃勃，直到海滩男孩发布了他们的代表作“宠物的声音”。

“宠物的声音”被誉为当时有史以来最好的专辑之一，但实际上没有他们以前的专辑卖得好。然而，布赖恩·威尔逊并不在乎:他开始创作一张更加雄心勃勃的专辑，名为《微笑》，但沙滩男孩从未发行过，部分原因是布赖恩·威尔逊发疯了。

《微笑》停播几年后，潮流引领者认为沙滩男孩无关紧要。他们不断推出一张又一张非常棒的专辑，但没人再在乎了。不是因为专辑不好，而是因为每个人都认为沙滩男孩不够时尚(T2)。

虽然这个故事有一个快乐的结局(布赖恩·威尔逊又有点功能了，[的微笑获得了巨大的赞誉)，但它说明了时尚创造者的力量。决定某件事是好是坏的人——让几乎所有人都随大流。](https://en.wikipedia.org/wiki/Brian_Wilson_Presents_Smile)

让我问你一个问题:在另一个时间线上，C 或 C++或 Java 没有分号，然后 Kotlin 出现并添加了分号，难道没有人会说分号如何“优雅地”声明一个语句的结束和另一个语句的开始，即使它们在同一行上？虽然没有人能肯定地回答这个问题(可能除了沃尔特·毕晓普之外)，但我很有信心，是的，会是这样的。

![](img/a9b5ace3442e99f35e8ceacb12e07b33.png)

A mother semicolon with its young, a once-proud animal of the savannah, now nearing extinction because of you, you dickhead

另一件事:如果普通人每分钟打大约 200 个字符，这意味着他们每秒钟打 3.3 个字符。这意味着普通的打字员插入一个分号需要大约 300 毫秒，而对于一个编码员来说则需要大约 200 毫秒。这意味着大约需要五分之一秒的时间***才能有人输入分号。***

***我甚至没有足够的时间扇你那张讨厌分号的蠢脸。***

> ***伙计，想想你能省下多少时间！***

***每个人都知道，在编码中，打字占据了大部分时间，而不是计划和测试流程、架构和算法，对吗？ ***对吗？******

## **你真是个白痴，安东尼斯。你知道你可以在 Kotlin 中使用分号，你不需要这样做，对吗？**

**首先，规则没有你想象的那么清晰，你仍然可能遇到一些需要分号的情况。**

**其次，即使我确实因为生气而越轨，我也只是用分号来比喻时尚创造者的暴政。你知道他们还不喜欢 Java 的什么吗？“新”操作符。好像**

```
**Animal animal = new Animal();**
```

**比……**

```
**val animal = Animal()**
```

**好像失去“新”这个关键词会让我变得疯狂高效，把我必须首先投入学习科特林的所有时间都打了折扣。**

**哦，是的，很高兴我提到了这一点——有没有任何研究表明，你通过学习(和使用)Kotlin 节省的时间比你首先必须投入学习 Kotlin 的时间还要多？没想到会这样。**

**当你是一个 Kotlin 初学者时，你必须处理的所有边缘情况又如何呢？通过多年的血汗和泪水，你已经学会了如何在 Java 中处理所有这些边缘情况？(哎，押韵！)**

**[](https://www.buymeacoffee.com/XozUExS) [## 给安东尼斯·查加利斯买杯咖啡——BuyMeACoffee.com

### 我是一名 Android 开发人员和设计师，热爱漂亮的用户界面！

www.buymeacoffee.com](https://www.buymeacoffee.com/XozUExS) 

## 结论？

听着，这不是科特林或围棋什么的热门话题。我其实很喜欢学习科特林和(有点)围棋。我喜欢创建扩展函数，并获得它们的价值。我喜欢能够从同一个函数返回多个值。拥有默认的参数值很酷。我不否认这些。

然而，我对这种遍及 Android 社区的“Kotlin 或者你停留在过去”的情绪感到有点困扰。

目前，我更喜欢使用 Java，我认为，只要官方 Android 文档将 Java 作为他们选择的语言，假装选择 Kotlin 是明智的(或者更糟糕的是，唯一可行的)做法将会误导(尤其是对初学者而言)。

所以，让我们给这个回声室添加一些填充物，在反馈变得震耳欲聋之前消除反射。

*对于一些有趣的 Android 小事故和随之而来的欢闹，请访问我的* ***WTF，Android SDK 系列*** *at* [*WTF，Android SDK 第一部分*](/@sebastian212000/wtf-android-sdk-part-i-animate-withendaction-504f0172fdaf#.in5k9wbzl)*&*[*WTF，Android SDK 第二部分*](/@sebastian212000/wtf-android-sdk-part-ii-why-u-no-crash-3e9455af8438#.bmtgyok56)

*用 Java 制作:我们基于位置的照片请求 app，*[*loo Xie*](https://play.google.com/store/apps/details?id=com.saladdressing.looksie)*！*

*我们还做了一个基于车牌的通讯 app 叫做*[*Karkoona*](https://play.google.com/store/apps/details?id=com.threeeplusplus.karkoona)*。那里也找不到科特林。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)**