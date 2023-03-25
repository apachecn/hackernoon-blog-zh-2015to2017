# 优步是如何在删除应用程序后标记和识别 iOS 用户的

> 原文：<https://medium.com/hackernoon/how-uber-could-have-tagged-and-identified-ios-users-even-after-app-deletion-c643a8e5364d>

约翰·格鲁伯今天早上为《纽约时报》写了一篇很好的分析文章，指责优步做了一些非常可怕的事情。

Gruber 接着说，一种技术可能是使用私有 API。

> 【更新 2: [Will Strafach 检查了优步 iOS 应用](https://twitter.com/chronic/status/856250223777206273)的 2014 年版本，发现他们使用私有 API 使用 IOKit 从设备注册表中提取设备序列号。可能还有更多，但仅此一项就公然违反了 App Store 政策。[斯特拉发奇证实](https://twitter.com/chronic/status/856333895050178560)优步使用的技术在 iOS 10 中不再有效。]

然而，从字里行间来看，我怀疑一种额外的技术可能是分析、广告和大多数公司使用的一种相当普遍的技术。**诀窍是让设备生成一个唯一的 ID，并保存到 iOS 钥匙串中。iOS 钥匙串，** [**直到 10.3**](https://forums.developer.apple.com/message/210531#210531) **，在重新安装之间保持不变。** [这篇文章](https://blog.onliquid.com/persistent-device-unique-identifier-ios-keychain/)展示了如何**，**虽然我不确定它是否在工厂重置之间持续存在，但我可以有把握地假设是这样。

我甚至可以说，优步可能导致这个漏洞被苹果工程师优先考虑和修补。

就我个人而言，我很高兴这种技术将不再起作用，随后将为 iOS 用户提供更多的隐私。

至于优步……\_(ツ)_/……市场将决定他们的命运。

**更新:**正如[威尔](https://twitter.com/wtsnz/status/856678126641790977)指出的，苹果打退堂鼓，并没有在 10.3 中做出上述改变。太好了，苹果。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！