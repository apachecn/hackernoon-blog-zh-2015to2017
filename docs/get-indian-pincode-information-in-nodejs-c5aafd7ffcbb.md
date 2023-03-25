# 获取 NodeJS 中的印度密码信息

> 原文：<https://medium.com/hackernoon/get-indian-pincode-information-in-nodejs-c5aafd7ffcbb>

密码是 npm 中的一个包，您可以通过提供密码或区域名称来搜索密码信息。这将是有用的人谁想要在印度所有地区的目标。根据 [pincode](https://www.npmjs.com/package/pincode) ，它是一个节点模块，可以方便地查找印度 pincode 信息。

示例代码:

**var pin = require(' pincode ')；**

**pin.seachByPin('560057 '，函数(响应){**
**response.forEach(函数(数据){**
**console.log(数据)；**
**})；**
**})；**

存储库 URL:[https://github.com/codingdefined/pincode](https://github.com/codingdefined/pincode)

更多信息:[http://www . coding defined . com/2015/08/lookup-Indian-pincode-Information-in . html](http://www.codingdefined.com/2015/08/lookup-indian-pincode-information-in.html)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！