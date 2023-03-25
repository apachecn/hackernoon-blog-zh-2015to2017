# 服务人员测试变得简单

> 原文：<https://medium.com/hackernoon/service-worker-testing-made-easy-9a2d8e9aa50>

在 Playstation 和 Pinterest 都有大量用户的情况下，没有什么比零测试代码更让我害怕的了。我是说，我知道我很好，但是我的虫子不同意。

当我开始调查[的服务](https://hackernoon.com/tagged/service)工人时，我真的对这种潜力感到兴奋。更快的资产缓存，离线模式，以及最终让移动网络*感受到*原生*的能力。*越学越觉得没有简单的方法可以考服务人员，很恐怖。一个服务人员缓存错误可以以一种几乎看不见的方式削弱你的站点。最好小心点。

喊出[测试服务人员](https://medium.com/u/17196521ef2d#.bd5jbe6cj)。测试服务人员的答案是:没有简单的方法，但你可以这样做。我不喜欢那个答案。

所以我做了一个东西。[服务人员模仿](https://github.com/pinterest/service-workers/tree/master/packages/service-worker-mock)。

这个想法是，您可以编写您的测试，就好像测试本身是在一个服务工作者环境中执行的一样。这意味着您可以**要求**您的服务工作者脚本，并针对您的缓存、注册的侦听器和可见通知的内容做出断言。超级简单，而且是开源的。让我们看看它是如何工作的。

对于每个测试，我们将当前的全球环境转换为服务人员环境。这意味着当您需要您的服务人员时，像 **self** 和 **addEventListener** 这样的变量将可供您的脚本使用。注意**jest . reset modules()**；这一点很重要，这样当您在测试中多次需要您的服务人员时，它实际上每次都会执行脚本。如果您没有使用 jest，您应该能够清除 **require.cache** 。

现在让我们编写我们的第一个测试！以下测试基于谷歌的[基本服务人员示例](https://github.com/GoogleChrome/samples/blob/gh-pages/service-worker/basic/service-worker.js)。这里我们想测试当激活时，它会清除旧的缓存。

使用 [service-worker-mock](https://github.com/pinterest/service-workers/tree/master/packages/service-worker-mock) 这是微不足道的。我们需要做的就是执行服务工作器(它将添加我们的事件侦听器)，打开一个缓存，并触发“激活”事件。我们可以使用 ServiceWorkerMock 全局“快照”函数来检查缓存的当前内容。没有比这更简单的了。

那么**取**呢？那是服务人员的生计。缓存响应，并为将来的请求决定使用缓存数据的策略。如果没有服务人员的模拟，测试这个真的很难。让我们看看使用助手是什么样子的。

首先，我们将一个响应放入缓存，然后触发一个获取请求。响应应该与我们放入缓存的响应完全相同。很简单！您还需要模拟出 **fetch** 方法，以测试您何时没有得到缓存的响应。

总的来说，我对模拟环境的结果非常满意。让我们开始为服务人员编写更多的测试！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！