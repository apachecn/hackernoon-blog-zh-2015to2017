# 数学:React.js 方法

> 原文：<https://medium.com/hackernoon/math-the-react-js-way-49ece7b6471f>

![](img/64744ca59ec58bc8293c83b92b2f1edb.png)

你有没有发现自己在使用奇怪的[符号](https://hackernoon.com/tagged/symbols)如+ *、*、【和】 ,做激烈的[数学](https://hackernoon.com/tagged/math)题？你喜欢 React.js 的速度和开发者体验吗？多亏了 *react-real-math* 你现在可以利用 React.js 虚拟 DOM 的力量让你的数学更加精彩🔥快！

*react-real-math* 免费提供了一个非常容易理解的 API。多好的交易啊！让我们来看一个 *react-real-math* 的例子。考虑一下这个老派的几乎令人困惑的数学问题:

> 1+1×2=?

难以置信的难以解析。但是有了 *react-real-math* 这就很容易理解了:

```
import { computeValue, Multiple, One, Two } from 'react-real-math'console.log(computeValue(
  <Multiply by={<Two/>}>
    <One/>
  </Multiply>
))
```

这就是我所说的💯💯💯家庭。超快的速度，惊人的 DX，和伟大的 DSL。这不仅非常容易阅读，新开发人员也很容易理解这里发生了什么。如果你想为 *react-real-math* 做贡献，请查看该项目的 Github:

[https://github.com/btnwtn/react-real-math](https://github.com/btnwtn/react-real-math)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)