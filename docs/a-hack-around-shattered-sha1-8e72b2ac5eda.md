# 围绕“粉碎”SHA1 的黑客攻击

> 原文：<https://medium.com/hackernoon/a-hack-around-shattered-sha1-8e72b2ac5eda>

关于现有系统中由[“破碎的”pdf](http://shattered.io/)——失败的 svn 提交导致的问题以及对伪造 git 提交的担忧的故事开始涌现。对于担心冲突的软件实现者来说，这里有一个现成的解决方案…

## 生成两个校验和。就是这样。

以下是 shattered-1.pdf 和 smashed-2 的 SHA1 校验和:

> 38762 cf 7 f 55934 b 34d 179 AE 6a 4c 80 cadccbb 7 f 0a shattered-1.pdf
> 
> 38762 cf 7 f 55934 b 34d 179 AE 6a 4c 80 cadccbb 7 f 0a shattered-2.pdf

以下是由 cksum 生成的相同文件的 CRC 校验和:

> 338397181 422435 shattered-1.pdf
> 
> 919129914 422435 shattered-2.pdf

SHA1 校验和可能相同，大小也相同，但 CRC 校验和仍然不同。并且 CRC 在计算上是廉价的。这在密码学上并不安全，但是生成两个具有相同 SHA1 *和*相同 CRC 的不同文件要困难得多。

我不知道这是否真的对任何人有用，但这是我第一次想到如何以实用的方式防止问题。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！