# JavaScript APIs:控制台

> 原文：<https://medium.com/hackernoon/javascript-apis-console-23ebce270419>

![](img/0ddfa42e98623547b528146159217e0c.png)

控制台 API 可能是最著名的 JavaScript API。几乎每个编写了大量代码的开发人员都不得不做一些事情。如果你写过任何种类的库或服务，你可能会使用`console.warn`或`console.error`来通知用户他们做错了什么。但是控制台能为我们做的远不止这些，所以今天我们要看看如何利用这一点！

# **断言**

首先我们有:`console.assert(*assertion*)`，它允许我们在控制台中运行一个*断言*。

> 断言是程序中特定点的布尔表达式，除非程序中有错误，否则该表达式为真。—[http://wiki.c2.com/?WhatAreAssertions](http://wiki.c2.com/?WhatAreAssertions)

如果断言为真，控制台不输出任何内容，如果断言为假，它将输出断言失败，以及您提供给它的任何消息或数据。

Using an assertion to send a message only when the value is false.

*注意:*在 Node.js 中 falsy 断言会抛出一个`AssertionError`并停止代码的执行。因此不建议在生产环境中使用。

# **计数**

有时候，知道一条给定的线路被调用了多少次是很重要的。也许你还没有准备好编写你的单元测试，断言它只被调用一次或两次；或者，也许你只是在调试并试图追踪比预期发生更多次的事情。这就是我们可以依靠`console.count`的地方。

这个很简单，添加`console.count(*message*)`并运行你的代码。每次运行该行时，您都会看到一条消息和一个数字弹出。

Outputting the count plus a generic log message.

本例中需要注意的一点是，每个输出的`count`都被设置为 1。那是因为信息变了。没有一个用户被调用超过一次，所以`count`永远不上去！当你最后再次调用用户 4 时，你看到`count`最终转到 2。

# **痕迹**

最后，我们到了我最喜欢的控制台方法！这里我们有:`console.trace`它会吐出一个[栈迹](https://developer.mozilla.org/en-US/docs/Web/API/console#Stack_traces)。它从调用`console.trace`的方法开始，一路返回到初始调用。

当您知道某个方法正在被调用，但不知道如何调用时，这个方法非常有用。

但是我现在能听到你说，“只要在代码中放一个调试器，你就能得到这个，还有更多！”对此，我只能说，“是的”。通常你可以使用一个调试器，但是有时候你不希望代码停止(比如如果涉及到计时，或者重复+计时)，或者如果由于某种原因你需要调试精简的代码。

这里有一个小例子:

# **格式化**

您不需要 ES6 来格式化控制台字符串。它们带有内置的格式化程序。

`%o`和`%O`将格式化一个对象。

`%d`和`%i`会格式化数字。

`%s`将字符串格式化。

`%f`将格式化一个浮点数。

你可以这样使用它们:

这种方法的一个优点是对象在浏览器中仍然是可扩展的，这些浏览器支持在控制台中漂亮地打印/扩展/折叠对象。

# 最后一句话——一些“明白了！”

如果你使用 Node.js，就要非常小心控制台。它同步打印到`stdout`，这可能会损害性能。

然而，在一些浏览器环境中，一些控制台语句是异步的，所以使用`console.log`将在输出时拍摄任何变量的快照，而不是在实际第一次调用时。这意味着你可能没有看到你所期望的。在这种情况下，你可以使用`console.dir`来确保你看到的是你所期望的。

出于这个原因和其他原因，我也反对在 web 浏览器环境中过于频繁地使用控制台语句。通常情况下，您可以在浏览器中使用调试器语句或断点来处理您想用控制台语句做的任何事情。有了调试器或断点，你就可以研究你的变量，而不必担心异步问题。

如果你错过了第一部分，你可以在这里找到它。请继续关注我们对一些鲜为人知的 JavaScript APIs 的深入研究。我们开始时很容易，但我们很快就会潜得很深！

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不代表我的雇主。所有代码样本都是我自己的，与美国银行的代码完全无关。

我也很乐意收到您的来信，请随时通过 [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/) 、 [Github](https://github.com/justindfuller) 或 [Medium](/@justindanielfuller) 与我联系。再次感谢阅读！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！