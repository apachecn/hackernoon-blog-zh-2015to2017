# Redux 和 React-路由器 v4:两个数据存储库的故事

> 原文：<https://medium.com/hackernoon/redux-and-react-router-v4-a-tale-of-two-datastores-8dd91f47d14e>

作为这篇文章的预期读者，你可能知道 Redux 将你的应用程序状态放在一个单独的存储中，使用一个漂亮的基于消息的 API 根据需要改变状态。

在 Redux 之前有 Flux 架构，它做的大致相同，但是有许多数据存储和许多实现尝试。事情相当混乱，Redux 带来了秩序和快乐。我❤️ Redux。

由此我得出结论，将所有应用程序状态存储在一个数据存储中是保持代码可维护性的一个重要因素。

E 进入这个故事的第二个主角，React 路由器(RR)。RR 允许你创建一个单页面应用程序，用 URL 假装它是一个服务器渲染的多页面应用程序，就像我们在黑暗时代所做的那样。这对于使用浏览器历史导航和允许搜索引擎链接到应用程序中的特定内容非常重要。

当 RR 最初出现的时候，在 React v0.x 的那些疯狂的日子里，它从其他框架带来了一些关于应用程序路由的想法。你可以使用 React-like-but-not-actual-React 语法指定路由，将你的应用程序包装在路由器中，然后 RR 会神奇地只显示你的应用程序的相关部分。

作为“神奇”部分的结果，应用程序状态的一个非常重要的部分*不在 Redux 存储中*🙀。亵渎！悲伤！哭泣的孩子！如果您想根据当前的 URL 做一些事情，您要么必须使用这个全新的不太具有声明性的 RR API，要么提取 RR 状态并将其输入 Redux。

![](img/d0e9652d5c8d019386182133733cb404.png)

fight for who gets to store the data — [source](https://www.flickr.com/photos/doug88888/8390613308)

以我当时的拙见，当前的 URL 只是应用程序状态的派生，而不是相反。我试验了一个系统，该系统像 React 处理绑定输入一样处理 URL 栏，这样新的 URL 事件被输入 Redux，然后从存储中计算 URL。这工作得相当好，但也不像是最好的解决方案。

这种方法的主要问题是必须在一个位置维护 URL 到 Redux 操作的映射以及 Redux 存储到 URL 的映射。这意味着特定的应用程序逻辑需要放在应用程序中至少两个不同的地方，这很少是好现象。

但是，请注意，RR 一直到 v3 都有完全相同的问题。

现在，我们到了美好未来的部分，事实上，就是现在:React 路由器 v4 带来了一个简单的、模块化的 API，不是“类 React ”,而是真正的 React 组件进行路由。

您可以在应用程序的每一层进行路由，而不是在一个位置放置所有的路由，只需将块包装在一个`<Route/>`组件中，该组件指定何时匹配。您甚至可以通过使用`@withRouter`装饰器来进行超级高级的路由。

这提供了如此多的好处:如果您想在选择某个路由时执行动作，您可以简单地使用 React 生命周期回调。如果你想扩展 API，你可以直接包装它。如果您想在应用程序的某个部分维护特定的路径，请继续。

我非常喜欢的一个具体结果是，现在您可以创建可以在应用程序的任何位置使用的组件，并且它们可以自己操纵路线。例如，您可以拥有一个列表/细节视图，它自动将当前细节项的 id 添加到 URL 中，您甚至可以嵌套它！

所以现在你可能想知道，Redux 怎么样？那单店理想呢？

好吧，应用程序状态仍然被分为两个域，Redux 存储和浏览器历史，但现在它是可管理和可预测的。您可以轻松地获取位置参数，使您的应用程序依赖于 URL，并且您可以非常简单地在组件中将其与 Redux state 结合起来。

当前安装的组件树是应用程序中声明性路由配置的结果，因此您只需知道当前位置及其派生参数，就可以呈现应用程序，Redux 存储包含所有其他应用程序信息。这两个数据存储携带完全正交的信息，一个可以改变，而另一个不必更新。

虽然我对这个 API 不是 100%满意，但是模块化的本质允许[以一种完全可互操作的方式包装它](https://gist.github.com/wmertens/b581322d5212035ffe7e6b4fd6222647)，所以现在我终于可以说:

我❤️反应-路由器 v4。

![](img/95b6163c4a39fabf31a7be0db1081e1d.png)

hugtime, no need to fight!

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)