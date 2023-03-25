# 在命令行上快速 JSON.parse()

> 原文：<https://medium.com/hackernoon/quickly-json-parse-on-command-line-42f0e52b01b8>

我试图从 **etcd** ( **kubernetes** )中获取一些数据，我发现该值以字符串形式返回:

![](img/3a3f749f286224437c56e44558a6f6d3.png)

你可以在开始看到引号，所以在玩了一会 sed 之后，我发现了 **jq** 的 **-r** 标志所以…

![](img/027f534f9a991d835a3f6a8084f43eb2.png)

如你所见，现在你有了一个 **json** 对象，我们可以将它传递回 **jq** :

![](img/012366b797d1a3a8d27641ae61396419.png)

就这样。

谢谢你。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)