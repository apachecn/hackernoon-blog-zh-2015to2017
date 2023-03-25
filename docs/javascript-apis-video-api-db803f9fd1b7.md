# JavaScript APIs:视频 Api

> 原文：<https://medium.com/hackernoon/javascript-apis-video-api-db803f9fd1b7>

![](img/0ddfa42e98623547b528146159217e0c.png)

在本系列中，我将探索可在浏览器环境中使用的不同 JavaScript APIs。有些是众所周知的，有些可能对你来说是全新的。希望每个部分都能为您提供一些新的信息和有用的实际技巧！那么，我们开始吧。

**不仅仅是 YouTube**

如果您以前没有使用过它，您可能甚至不知道视频 API 的存在，或者您可能认为它只能用于创建自定义视频播放器。然而，如果你只是需要将一个视频嵌入到你的站点中，而不想创建你自己的花哨的视频播放器，视频 API 仍然有适合你的东西！

**记住用户设置**

视频 HTML 标签可用的所有选项，如`autoplay`、`controls`、`loop`、`src`都可以用 JavaScript 而不是 HTML 来设置。所以，假设你有一个自动播放的视频，但是你想让你的用户退出这个可能很烦人的功能，你可以做以下事情:

Autoplay based on user settings.

现在你只向那些对这个功能满意的人展示自动播放！

**根据连接播放正确大小的视频**

下一个选项更具体一点，也更复杂一点，但是它在用户体验方面有巨大的好处。您必须对它进行试验，以便为您和您的用户找到正确的平衡，但是这应该为您提供了一个良好的起点。

视频 API 附带了一组在视频生命周期中发生的事件。`oncanplay`、`oncanplaythrough`、`onplay`、`onpause`都是可以使用的好的生命周期事件的例子，但是我们不关心什么时候事情进展顺利，我们想知道什么时候视频加载不正常。

因此，我们将关注`onstalled`、`onwaiting`、`onerror`，以便在这些事件发生时做出改变。

当我们得到`onerror`事件时，我们会给用户一个重新加载视频的选项。当我们有`onstalled`或`onwaiting`时，我们将检查这种情况以前是否发生过，发生了多少次，如果次数太多，我们将把`src`改为更小比特率的视频。

下面是一个出错时重新加载视频(或给出这样做的选项)的例子:

Add an error button/reload option when video errors occur.

以下是视频加载过于频繁时更改`src`的示例:

Change the video src if it buffers/pauses too often.

最后，视频 API 的一个好资源是[http://www.w3schools.com/tags/ref_av_dom.asp](http://www.w3schools.com/tags/ref_av_dom.asp)。

请继续关注更多关于 JavaScript APIs 的帖子！

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不打算以任何方式代表我的雇主*。所有代码样本都是我自己的，与美国银行的代码完全无关。*

我也很想收到你的来信，请随时通过 [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/) 、 [Github](https://github.com/justindfuller) 或 [Medium](/@justindanielfuller) 与我联系。再次感谢阅读！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！