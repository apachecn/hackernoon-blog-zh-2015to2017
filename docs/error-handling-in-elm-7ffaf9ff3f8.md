# Elm 中的错误处理

> 原文：<https://medium.com/hackernoon/error-handling-in-elm-7ffaf9ff3f8>

## 以及它与 Javascript 的区别

在 [Javascript](https://hackernoon.com/tagged/javascript) 中，有几种方法可以处理不顺利的情况。

*   你可以**通过保护性检查值**和**返回安全响应**来约束自己。
*   尽可能多地做 TDD 可以让你得到彻底的训练。
*   您可以使用 **try-catch 和 throw** 来模仿检查异常的语言。
*   您可以**什么都不做**，让运行时错误对用户体验产生不利影响。

在编写 Elm 应用程序时，当您需要处理不愉快的情况时，您不可避免地会遇到这种情况。您的第一直觉可能是寻找您在那个 Javascript 项目中使用的策略，但是其他方法可能对您来说不可用。

在这篇文章中，我将概述你在编写 Elm 应用程序时会遇到的“错误”情况的类型，并且我将描述 Elm 如何使处理这些情况变得愉快和安全。

# 意外类型

您将在 Javascript 中发现的第一种类型的错误是**类型错误**。这就是众所周知的 *undefined 不是函数*或者*不能读取属性“某物”的 undefined* 。

在 Elm 应用程序中，您将**永远看不到这个错误**。Elm 是一种静态类型语言，它有以下保护措施。

## 编译器

例如，如果你声明一个常量为字符串，编译器将保证该常量只能被当作字符串处理。不能是 Int。不能为空。不可能是未定义的。如果你试图把它变成字符串以外的任何东西，应用程序*将不会编译*。

## 不可变常数

Elm 没有可以重新分配给不同值或类型的变量。Elm 有**不可变的常量**，或者更好地认为是*零参数纯函数*。因此，你的声明不能改变你的类型。

## 可能

许多语言将 null 和 undefined 视为子类型。这就是臭名昭著的[十亿美元的错误](https://en.wikipedia.org/wiki/Tony_Hoare#Apologies_and_retractions)。在 Elm 中，null 的等价物被称为 **Nothing** ，它是被称为**或者**的标记联合中的一个标记名。

> Elm 将*可空性视为一个单独的类型*

换句话说，Elm 将*可空性视为一个独立的类型*。因此， *null* 的概念必须作为类型系统的一部分进行显式处理，从而避免许多常见的运行时错误。

# 意外数据

从外部世界接收输入的应用程序不可避免地会以一种*的形状或类型接收数据，而这并不是它所期望的*。在 Elm 中，有几个入口点可以接收来自外部世界的数据。

## 来自 Html 的消息。事件处理程序

从 Html 发送的消息。事件有[特定的类型](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html-Events#onInput)，因此保证向 Elm 应用程序提供正确类型的消息和有效负载。这是类型系统的又一个例子，它保证你不用做任何额外的工作就能处理正确类型的值。

## 来自任务的消息

任务是您执行大多数副作用的方式，例如执行 Http 请求。执行 [Http 请求](http://package.elm-lang.org/packages/elm-lang/http/1.0.0/Http#get)必须提供的参数之一是解码器。一个[解码器](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Json-Decode#Decoder)是一个可组合的类型，Elm 用它将 JSON 转换成您可以使用的类型。Elm 通过向您传递一个[结果](http://package.elm-lang.org/packages/elm-lang/core/5.1.1/Result#Result)来实现这一点，这个结果是一个专门化的[类型](https://hackage.haskell.org/package/base-4.9.1.0/docs/Data-Either.html)，它要么包含一个错误(转换 JSON 失败)，要么包含一个成功值(转换后的值)。

> Elm 为您提供了一个专门的任一类型，称为结果

如果有错误，编译器会强迫你处理这个错误。这是您用收到意外数据的指示来更新应用程序状态的机会。

# 异步故障

当发出 HTTP 请求时，这些请求最终会因为各种原因而失败。使用 Javascript，您将传递一个*成功回调*来处理成功响应，还有一个*可选的失败回调*来处理失败。

榆树不一样。不是公开一个可选的失败回调，而是要求你提供一个回调接受一个结果(与上面讨论的任一类型相同)作为输入，这个回调返回一个消息，这个消息可能会根据结果的不同而不同。

这意味着异步错误必须被明确处理，而不是被*意外遗忘*。

# 通过精心设计的数据结构防止错误

到目前为止，我最喜欢处理的错误类型是*完全没有错误*。如果一个不好的情况可能发生，而你可以合理地预防它，为什么不预防呢？

理查德·费尔德曼的 [*让不可能的状态变得不可能*](https://www.youtube.com/watch?v=IcgmSRJHu_8) 的演讲很好地阐述了实现这一点的方法，但我目前最喜欢的两个应用这一思想的例子是克里斯·詹金斯的 [RemoteData](http://package.elm-lang.org/packages/krisajenkins/remotedata/4.3.0/RemoteData#RemoteData) 包和马克斯·戈尔茨坦的[elm-non-empty-list](http://package.elm-lang.org/packages/mgold/elm-nonempty-list/latest)包。

这里有一个使用远程数据的演示。

RemoteData Example

在使用这个包的过程中，我们防止了某个“错误场景”,在这个场景中，视图可能与请求过程中实际发生的事情不同步。因为 RemoteData 在模型中是一个*类型*，编译器将迫使您处理 RemoteData 可能是四个不同的标签，从而迫使您在视图层显式地处理异步请求生命周期的不同部分。

# 警告

我在上面的**意外数据**部分撒了谎。实际上，Elm 应用程序还可以通过另外两种方式从外界接收数据。

1.  用 [Html.programWithFlags](http://package.elm-lang.org/packages/elm-lang/html/2.0.0/Html#programWithFlags) 初始化应用程序
2.  当从端口接收消息时

在我看来，当前版本的 Elm (0.18)在涉及到*错误处理*时，并没有很好地处理这些情况。当使用 programWithFlags 时，当应用程序用一个它不期望的值初始化时，有可能出现一个运行时异常[。当使用端口订阅时，当一个意外的值被发送到 Elm 应用程序时，有可能再次出现运行时异常](https://ellie-app.com/kMCYQWs2SY/0)。*(打开控制台查看错误)*

我特别询问了这两个问题，我得到的回答表明端口和 programWithFlags 早于解码器，我们不应该期望[设计](https://hackernoon.com/tagged/design)决策在不久的将来会改变，这很好。

还有一个有用的建议是使用 Json。解码值。换句话说，将数据声明为 JSON 值将迫使您使用解码器将 JSON 值转换为您的 Elm 应用程序可以使用的类型。

所以我对这两种情况的最佳回答是**遵守纪律**并使用 **Json。使用 programWithFlags 和端口订阅时解码. Value** 。这种模式与显式处理错误和防止运行时异常是一致的。

# 结束语

我发现在 Elm 中处理错误情况是一种相当愉快的体验。Elm 将迫使您基本上通过利用类型系统来处理错误，该系统在简单性和由此带来的开发人员体验方面都很棒。

我想这篇文章最大的收获之一是，如果你正在开发一个 Javascript 应用程序，你可以通过添加一个*静态类型*实现来使用这些相同的策略，这个实现也有*标记的联合*。我意识到说起来容易做起来难。但是对我来说，至少知道我可以通过将静态类型引入到我工作的 Javascript 应用程序中来利用 Elm 带来的一些好处是件好事。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！