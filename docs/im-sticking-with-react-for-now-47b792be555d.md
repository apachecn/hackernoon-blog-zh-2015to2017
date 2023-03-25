# 我坚持使用 React(目前)

> 原文：<https://medium.com/hackernoon/im-sticking-with-react-for-now-47b792be555d>

上个月，在工作场所获得了很多使用经验后，我把我的个人网站改成了 [React](https://hackernoon.com/tagged/react) 。我把我的网站作为一只试验品，去挑战极限，看看我能从技术上实现什么。

如果你冒险深入 Hackernews、Reddit 或众多[节目](https://hackernoon.com/tagged/programming)新闻网站中的一个，你很可能会听说过众多“反应杀手”中的一个——VueJS、Inferno 或 Preact。假期期间，我决定是时候尝试一些 React 替代品了。

## 转换变得容易？

对于 Preact 和 Inferno 来说，从 react 到(Preact 或 Inferno)的转换被认为是非常简单的——只需在您的 bundle 中将 React 和 React-dom 别名为(Preact 或 Inferno)compact，然后 boom，您的 bundle 就变小了 30kb。

向这些框架的转换非常容易。

虽然我知道这两个框架仍在建设中，但 React 为我的应用程序提供的稳定性太好了，我不能放弃。

# 稳定性

对于 Inferno 和 Preact，我的定制 CSS 转换都没有解决方案——基础设施还不存在。使用 Preact，我发现我的 SVG 有时会随机选择不渲染，这很不幸，因为我的整个首页都是一个 SVG。

![](img/91327198331c1f364ac1c6bf7028ee11.png)

Is Preact trying to tell me to shave?

公平地说，Preact 目前意识到了这个问题，他们正在努力解决这个问题，但在过渡到新框架之前，需要实现这种程度的稳定性。

在稳定性的基础上，React 还提供了它的合成事件处理系统，这使得它比其他框架更大。在使用 React 时，您可能甚至不会注意到这个系统为您做了什么，我相信这是一个巨大发展的迹象。

# 生态系统

我使用 React 的一个包来处理我的 CSS 转换，因为在使用虚拟 dom 时它们很难实现。Preact 在某种程度上为 Preact 转换了这个包，但是它缺少了 react 的许多功能。Inferno 还没有 CSS 过渡的解决方案。

React 最大的好处就是不可思议的生态系统。在一个快速的谷歌中，你可以找到任何作为组件编写的 React 包。每天我都会看到更新更有创意的 React 实用程序和组件。

虽然 Preact 和 Inferno 社区正在努力支持许多这些主要的包，例如 Redux，但我认为是那些不知名的包帮助了 react。

以下是我最喜欢的一些比较晦涩的 React 包:

*   [React-VR](https://github.com/facebookincubator/react-vr)
*   [反应日期选择器](https://github.com/airbnb/react-dates)
*   [胜利数据可视化](https://github.com/FormidableLabs/victory)
*   [反应过来的声音](https://github.com/leoasis/react-sound)
*   [反应视频](https://github.com/pedronauck/react-video)

此外，这些框架都不支持 React Native，这对于那些喜欢在 React 和 React Native 之间共享代码的人来说是一个很大的缺点。

# React 团队都是超级巨星

React 已经找到了一种方法，让他们的开发团队拥有一些令人印象深刻的程序员。以下是一些成员的简要介绍，以及他们对 React 社区的贡献:

丹·阿布拉莫夫又名[盖亚龙](https://github.com/gaearon):

*   [Redux](https://github.com/reactjs/redux) 的共同创建者
*   [反应热加载器](https://github.com/gaearon/react-hot-loader)
*   [创建 React 应用](https://github.com/facebookincubator/create-react-app)
*   [反应过来的 DnD](https://github.com/gaearon/react-dnd)

安德鲁·克拉克又名 [acdlite](https://github.com/acdlite) :

*   [Redux](https://github.com/reactjs/redux) 的共同创建者
*   重组

布莱恩·沃恩又名[博沃恩](https://github.com/bvaughn):

*   [React 虚拟化](https://github.com/bvaughn/react-virtualized)
*   [Redux 搜索](https://github.com/bvaughn/redux-search)

脸书的团队还增加了《地狱》的创作者 Dominic Gannaway。

![](img/e37ddb318b66680f023a9079422c6c3f.png)

React 背后有一个了不起的社区和团队，他们一直在推动它前进。很难忽视目前存在于社区中的智囊团的数量。

# 动力

这并不是说其他两个框架在社区中没有任何动力，而是说 React 感觉像一个不可阻挡的蒸汽压路机。

React 目前正在进行整个重写，以作为 React 纤程发布。这个新代码无疑会使用 React 团队从 Preact 和 Inferno 中学到的一些东西。

关于 React 光纤，我推荐[什么是 React 光纤？](https://gist.github.com/duivvv/2ba00d413b8ff7bc1fa5a2e51c61ba43)

目前，我坚持使用 React。看起来好像它只是最稳定的框架，而且肯定不会失去任何动力。

与此同时，我会密切关注 Vue、Preact、Inferno 和 Svelte。我希望比赛能带来更好的整体发展和用户消费体验。

*在*[T3【https://twitter.com/SeanKilgarriff】](https://twitter.com/SeanKilgarriff)*|*[*进行更正*](https://github.com/Skilgarriff/Blog-Posts) *|原载于 2016 年 12 月 28 日*[*【blog.seankilgarriff.com】*](https://blog.seankilgarriff.com/im-sticking-with-react-for-now/)*。*

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！