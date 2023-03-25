# 我如何劫持包括凯蒂·佩里、夏奇拉在内的顶级名人的推特

> 原文：<https://medium.com/hackernoon/how-i-hijacked-top-celebrities-tweets-including-katy-perry-shakira-fca3a0e751c6>

你可能听说过@realDonaldTrump 的一条推文，它最近被比利时安全研究员 Inti De Ceukelaire(@securinti)劫持了:

Trump 提到了国家成就者大会 nac2012.com 的网站，该域名没有被原所有者更新。因此，Inti 能够购买它并重定向到 YouTube。

在这个假新闻的时代，这可能是制造混乱或欺骗人们点击链接的好方法。难道你不会更有可能点击凯蒂·佩里发的一个链接吗？

所以我想看看排名前 1000 位的 twitter 账户是个不错的主意:

为此，我写了一个小 python 脚本。它有点乱，我稍后会介绍如何改进它，但简单来说，它是这样做的:

*   从用户那里下载所有可以下载的推文
*   获取这些推文中的域名
*   验证域名是否可用于注册

# 结果呢

我确实认为已经有人在积极地做这件事了，我不会找到任何可用的域名，但是我错了。我发现前 1000 个 twitter 账户中有 109 个可用域名，整整 10% &我认为这个结果可以大大改善。

以下是前 10 名:

*   凯蒂·佩里，@凯蒂·佩里，9560 万
*   夏奇拉，@夏奇拉，42.7 米
*   詹妮弗·洛佩兹，39.3 米
*   阿米尔·汗@阿米尔汗，19.8 米
*   阿格涅兹莫，@阿格涅兹莫，16.2 米
*   三 X 电影？？？，@deepikapadukone，17.3 米
*   魔力红，@maroon5，13.7 米
*   沙奎尔·奥尼尔@SHAQ，1320 万英镑
*   塔利亚@塔利亚，8.77 米
*   Pegg 新闻，@simongpegg，6.63 米

我重定向了夏奇拉的这条推文。注意，这条推文中一开始没有嵌入媒体。所以 twitter 重新抓取了那个链接并为我嵌入了视频，很好(:

# 局限性和改进

最有问题的限制是 Twitter API 设置的限制。对于一个用户，在 count=200 的情况下，只能调用 user_timeline() 16 次。这意味着我们只能下载 16*200=3200 条推文。此外，它们通常会限制 API 访问，因此这个过程非常耗时。

最好的办法是要么访问所有的推文，要么开始保存它们。我没有发现任何真正为所有用户提供所有推文的服务，但一些网站，如 T[rumtwitarchive](http://www.trumptwitterarchive.com)正在存档来自 [@](https://medium.com/u/652754dc92a3?source=post_page-----fca3a0e751c6--------------------------------) 总统&和其他政治人物的推文，但他们不提供 API。然而，应该可以使用硒/羊瘙痒爬他们。

我还注意到很多 bit.ly & smarturl.it，如果它们是双缩短链接，我就不会关注。遵循这些可能会得到更多的结果。最重要的是，我使用的 pytonwhois 模块对某些 TLD 提出了一些例外…

![](img/eca600274f2066099559dd9aa38e7d24.png)

In case you’re wondering: no, I have nothing better to do with my life.

随便做个 PR 或者叉在 Github 上。原谅我的 Python(:

[](https://github.com/misterch0c/twitterBFTD) [## misterch0c/twitterBFTD

### twitterBFTD - Twitter 起死回生在用户的 tweets 历史中寻找可用的域名…

github.com](https://github.com/misterch0c/twitterBFTD) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)