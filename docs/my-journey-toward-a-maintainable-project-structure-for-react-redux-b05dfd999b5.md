# 我的 React/Redux 可维护项目结构之旅

> 原文：<https://medium.com/hackernoon/my-journey-toward-a-maintainable-project-structure-for-react-redux-b05dfd999b5>

当我开始学习 Redux 的时候，我对你可以在网上找到的关于它的讨论和“最佳实践”的数量感到震惊，但这并没有花太多时间来理解为什么: [Redux](https://hackernoon.com/tagged/redux) 对围绕它构建项目的方式不是很固执，当你试图找出哪种结构更适合你的风格和你的项目时，这可能会导致一些烦恼。

在这篇文章中，我想分享一些我实现一个舒适的 Redux 项目结构的信息。

> 这不是一个介绍/教程，需要一些 Redux 的知识来完全理解它。
> 
> 此外，除了 redux-sagas(可以用 redux-thunk 或您喜欢的用于处理异步操作的库来代替)，我不会使用任何外部 redux 库/实用程序。

希望你觉得有趣！

# 第一站:按“类型”对文件进行分组

当我开始使用 Redux 时，我从上到下研究了[官方文件](http://redux.js.org/docs/recipes)，我是这样组织我的项目的:

这种结构是由官方的 Redux repository examples 推广的，在我看来，这仍然是一个非常可靠的选择。
像这样的结构的主要缺点是，即使添加一个小功能也可能会导致编辑几个不同的文件。
例如，向产品商店添加字段(由操作更新)意味着您必须:

*   在***types/product . js***中添加动作类型
*   在***actions/products . js***中添加动作创建者
*   在***reducers/product . js***中添加该字段

这还没有结束！当应用程序增长时，您可能会添加其他目录:

因此，在我的项目中引入了 [redux-saga](https://github.com/yelouafi/redux-saga) 之后，我意识到它变得太难维护了，于是我开始寻找替代方案。

# 另一种方法:按特征对文件进行分组

上述项目结构的另一种方法是按特征对文件进行分组:

这种方法已经被 React 社区中各种有趣的文章所推广，并且被用于最常见的 React 样板文件之一。

乍一看，这种结构对我来说似乎很合理，因为我遵循 React 的组件概念，将组件(容器)、它的状态(存储)和它的行为(动作)封装在一个文件夹中。在一个更大的项目中使用它后，我发现它并不总是阳光和彩虹:如果你使用 Redux，你可能是为了在你的应用程序中共享一部分商店……你可以很容易地看到这与这种结构提倡的封装概念相冲突。
例如，从产品容器调度一个动作可能会对购物车容器产生副作用(如果购物车缩减器以某种方式对该动作做出反应)。

您还必须小心不要陷入另一个概念陷阱:不要觉得被迫将 Redux 存储的一部分绑定到一个容器，因为否则您可能会最终使用 Redux，即使您应该选择简单的 setState。

# 鸭子来救援了

在经历了按特性分组的小冒险之后，我回到了我最初的项目结构，我注意到使用 Sagas 处理异步流有一个有趣的副作用，那就是将 90%的动作创建者放在一行程序中:

```
const login = (email, password) => ({ type: LOGIN_REQUEST, email, password })
```

在这种情况下，为每个动作创建者、动作类型和缩减者创建一个专用文件似乎有点大材小用，所以我决定尝试“Ducks”方法。

[Ducks](https://github.com/erikras/ducks-modular-redux) 是一个将 reducer、action 类型和 action 捆绑在同一个文件中的提议，导致一个简化的样板文件:

第一次采用这种语法时，我就爱上了它。
它很干净，去掉了很多不必要的样板文件，你可以通过改变一个文件轻松地添加一个动作或者一个字段到 reducer 中。

然而不幸的是，使用鸭子很快显示出它的局限性，因为单独输出每个动作创建者和动作类型有一些令人讨厌的副作用。

*   在更大的容器中，您将最终拥有一个巨大的只使用一次的导入动作列表(用于提供 mapDispatchToProps):

```
import { signup, login, resetPassword, logout, … } from ‘ducks/authReducer’
```

*   你不能将鸭子的所有动作直接传递给 mapDispatchToProps。使用 **import *作为动作**是行不通的，因为你最终也会导入减速器。
*   仅仅为了将它传递给 mapDispatchToProps，您就浪费了一个非常宝贵的变量名。
    您甚至不能做类似于 **const { login } = this.props** 的事情，因为您已经通过将 **login** 变量分配给从 duck 导入的 action creator 来定义了它。
*   在更大的传奇故事中，你会在不知道上下文的情况下使用很多不同的动作(你每次都必须滚动到顶部的导入)。

# 定制鸭子

我对上述问题的解决方案很简单:不是单独导出动作类型和动作创建者，而是将它们分组并导出到一个类型和动作对象中:

如果您以这种方式构建鸭子，您将能够轻松地将动作导入到您的组件中:

现在你可能会问:如果我需要在一个组件中分派不同鸭子的动作呢？嗯，你可以这样做:

我知道，有点难看，欢迎任何替代方案！

> *最初我选择了名称****action types/action creators****而不是* ***类型/动作*** *，但是过了一会儿我又选择了后者:第一个选项对我来说太冗长了。*
> 
> *附注:从现在开始，为了简单起见，我将继续把包含动作/reducer/类型的文件称为“ducks ”,即使在我当前的项目中，它们在“reducers”文件夹中。*

# 选择器

在我看来，选择器是 Redux 最容易被忽视的特性。
我必须承认我开始使用它们有点太晚了，但是阅读[丹·阿布拉莫夫](https://twitter.com/dan_abramov/status/730933179511640064)的这条推文让我大开眼界，我开始将选择器视为将商店展示给容器的接口。

*   需要按一定顺序显示列表？定义**getProductOrderedByNameselector**选择器。
*   需要得到一个列表的特定元素？定义 **getProductById** 选择器。
*   需要筛选列表吗？定义 **getExpiredProducts** 选择器。

按照这种策略，您定义的大多数选择器将与特定的缩减器紧密相关，因此定义它们的正确位置是包含缩减器本身的文件。

> 有时你需要定义更复杂的选择器来处理来自商店不同部分的输入。在这种情况下，我将它们放在**reducers/index . js***中。*

# 传奇故事

Sagas 确实是强大的和可测试的，但是被用于管理异步动作和副作用使得很难推理如何将它们添加到你的项目结构中。
我的建议是，开始对同一个动作域内由单个 redux 动作触发的传奇进行分组。
这意味着如果你有一个处理***ducks/auth . js***中的认证的 reducer，你可以创建***sagas/auth . js***，包含由 **authTypes 触发的 sagas。注册请求**，**认证类型。LOGIN_REQUEST** 等等…

有时候，我认为你需要用不同的动作来触发同一个传奇。在这种情况下，您可以创建一个包含这类传奇的更通用的文件。例如，当 React Native 截获一个错误时，它会显示一个警告:

如果通用文件(在这个例子中我称之为 ***sagas/ui.js*** )增长太多，您总是可以稍后重构它，使其更加具体。

另一件不值一提的事情是，在 ***传奇/index.js*** 中，我有一个文件，其中链接了每一个传奇的 take……指令及其实现:

这样，我就可以通过将传奇与触发它的动作类型相关联来轻松地追踪每一个传奇。

# 结论

就是这样！这是我当前的项目结构(正如我在上面预期的那样，我将“ducks”文件夹重命名为“reducers”):

我知道还有改进的空间，这可能是我发表这篇文章的主要原因:欢迎提示和批评！

# 编辑#1:您将没有绑定到特定缩减器的动作/类型放在哪里？

在我的大部分项目中，我有一个(或多个)通用 duck 来处理这类操作。
例如，如果项目足够小，我只需创建 **ducks/app.js** ，这是一个 duck 文件，其中包含 1)不是特定于缩减器的所有动作创建者和动作类型，以及 2)ui/app 的状态:

请记住，这些鸭子可能不需要一个缩减器:它们可以只包含动作创建者和动作类型。

# 编辑 2:更明确的方法

Reddit 和 Twitter 上的一些评论暗示，鸭子可能宣扬行为和减少者之间的关系是**多:1** ，而实际上是**多:多**。我同意他们的看法。
实际上(在我看来)，遵循 Redux 原理的架构甚至会比我之前展示的架构更符合 Redux 原理，具体如下:

不过，我仍然更喜欢使用定制的 ducks，当我的行为没有绑定到减压器(或者相反)时，我会采用上面在 edit #1 中解释的解决方案。
最后，你可能需要自己测试不同的结构，看看是否能在简洁和舒适之间找到更适合自己风格和项目的平衡点。

感谢 [@mxstbr](http://twitter.com/mxstbr) 和 [joshwcomeau](https://www.reddit.com/r/reactjs/comments/55vstu/my_journey_toward_a_maintainable_project/) 。

# **信用和有用链接**

*   [反应/还原链接列表](https://github.com/markerikson/react-redux-links)马克·埃里克森
*   [这个讨论](https://github.com/infinitered/ignite/pull/356)来自于令人敬畏的 Ignite 样板存储库(它使用了与我向您展示的结构类似的结构)的请求
*   [史蒂夫·凯洛克](https://github.com/skellock)，因为他启发了我写这篇文章，也因为我们就 Redux 进行了一次有趣的讨论，你可以在这里找到
*   [丹·阿布拉莫夫](https://twitter.com/dan_abramov)，因为他发布的所有东西都超级有趣，写得很好(也感谢 Redux！:P)
*   这个小样本有点过时，但我会尽快更新它

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[https://upscri.be/hackernoon/](https://upscri.be/hackernoon/)