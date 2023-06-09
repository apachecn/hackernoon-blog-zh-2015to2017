# 亚马逊 Go 商店:他们是如何做到的？

> 原文：<https://medium.com/hackernoon/amazon-grab-and-go-how-do-they-do-it-d1055c9deeab>

我有点痴迷于亚马逊新的“走出去”商店。

我的第一反应是:哦，我懂了。给所有物品贴上 RFID 标签。当你离开商店时，你必须走过那些白色的大东西——那些是 RFID 阅读器，在你离开时扫描你的物品。简单。

RFID 是最简单的技术解决方案。但这说不通。

首先，是标签的成本。即使每张标签 2-5 英镑，也会侵蚀低成本商品微薄的零售利润。

第二，T2 给每件商品贴标签会增加运营成本。大多数便利店甚至不再理会价格标签。

我真的认为[亚马逊](https://hackernoon.com/tagged/amazon)会在知道自己的成本结构高于竞争对手的情况下进入一个市场吗？那不是我认识的亚马逊。

于是我又看了一遍视频。原来我以为是 RFID 阅读器的东西实际上是一个十字转门，大型办公楼都有的那种。(它可能也有 RFID 阅读器，但没有理由这样假设。)

此外，视频清楚地显示了你购物时清单上的物品。因此，亚马逊实时跟踪:1)哪件商品被拿走了，2)谁拿走了它。

知道是谁拿走了很容易——天花板上的摄像头可以追踪顾客的位置。软件可以在登记时将一个人与一个负责人联系起来，然后跟踪这个负责人在商店中的位置。

在边缘情况下，这可能会变得棘手——比如，当你站在某人旁边，然后越过他们拿东西。但是这种情况多久发生一次呢？

至于跟踪从货架上拿下来的商品，他们可以在货架上安装传感器。比如体重秤或者存在传感器。他们可能确实使用了一些混合传感器，正如他们隐晦地提到的“传感器融合技术”

但事实是，他们可以用摄像机做所有该死的事情。

因为他们知道产品在货架上的位置，所以跟踪你拿了什么东西相对容易。一些物体识别将有助于捕捉边缘情况，比如有人用一只手从货架上拿走两个瓶子。目前，他们可能还使用传感器来帮助处理这些边缘情况(毕竟，他们称之为“传感器融合”)。

然而，随着时间的推移，相机本身就足够了。我喜欢这个。

通常，技术失败是因为它带来了新的操作复杂性，例如，在库存流程中添加 RFID 标签。

基于人工智能的技术，在这种情况下是计算机视觉，是不同的。随着技术变得越来越先进，部署它的复杂性会降低。不需要更多的专业知识来定制解决方案以满足您的需求，您只需投入更多的摄像头即可。

让电脑来解决剩下的问题。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！