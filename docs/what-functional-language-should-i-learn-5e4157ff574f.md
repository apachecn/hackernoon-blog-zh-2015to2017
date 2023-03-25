# 应该学什么函数式语言？

> 原文：<https://medium.com/hackernoon/what-functional-language-should-i-learn-5e4157ff574f>

![](img/0f74320aaa3b57e6c11bdb3dd4b4683c.png)

如果你正在读这篇文章，我假设你已经准备好或者至少正在考虑迎接新的挑战:学习(更多)函数式编程，并考虑学习一门函数式语言。这是一个伟大的想法！但是有这么多函数式语言，你应该选择哪一种呢？

# 语言还是范式:先学哪个？

下面的评论扩展了我的书《C# 中的 [*函数式编程》的后记，所以当读者读到后记时，我假设他已经学会了函数式思考，并在 C#中利用函数式技术。事实上，以下两者之间有一个重要的区别:*](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)

*   功能性思维
*   用函数式语言编码

功能性思维是真正棘手的一点。你接近这两者的顺序取决于你的背景。

如果你是编程新手，你很幸运:直接跳到语言列表。

如果你已经精通一种命令式和/或面向对象语言(比如 Java、C#或 JavaScript)，那么学习功能性思维是很困难的。它需要改变你的习惯，挑战你的假设，重新成为一个初学者，并逐渐建立一个新的直觉和思考过程，关于如何以功能性的方式解决问题。

在学习一门新语言的语法、库和工具的同时做所有这些事情会让人不知所措。出于这个原因，我建议**你的函数式编程的第一步应该是学习用你熟悉的语言**编写更多函数式的代码。(如果这个语言是 C#，那么[我的书](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)就是为你写的。)

我这样说是因为大多数主流语言都是多范式的，所以它们为函数式编程提供了一些支持。在某种程度上，您可以用任何语言进行函数式编程，这些语言具有:

*   作为一级值的函数(您可以将函数作为参数传递)
*   自动内存管理(垃圾收集器)

(关于这一点的更多信息，请参见我以前的文章[c# 7 开始看起来像函数式语言了吗？](https://hackernoon.com/is-c-7-starting-to-look-like-a-functional-language-d4326b427aaa))

# 为什么要学习函数式语言？

因此，让我假设您已经到了在日常工作中经常使用函数技术的地步，尤其是高阶函数、不可变数据结构等等。

你还应该学习函数式语言吗？当然啦！如果你想更深入，你需要。在多范例语言中，您可以在该语言支持的不同方法之间混合搭配。另一方面，函数式语言会强迫你从头到尾使用函数式方法——例如，根本不允许任何状态突变。自然，函数式语言比多范式语言对函数式技术有更好的语法支持。

学习函数式语言的另一个好处是，它可以让你利用其他学习资源:书籍、博客、演讲等等。今天，大多数关于 FP 的学习材料都有 Haskell 或 Scala 的代码样本。

您选择哪种语言将取决于您想从中获得什么，因此我将在接下来讨论这一点。

# 哈斯克尔

自然的选择是学习 Haskell，它是函数式参考语言，也是函数式程序员的通用语言。*大多数关于函数式编程的文献——不仅仅是学术论文，还有博客和其他在线资源——都有 Haskell 代码示例。*

为此，我推荐你阅读 Miran lipo va a 的[*Learn You a Haskell for Great Good*](http://learnyouahaskell.com/)。你可以在网上免费阅读完整内容，但一定要考虑购买一本，以奖励作者的辛勤工作。

学习 Haskell 的另一个好方法是在学习埃里克·梅耶尔在 [edX](https://www.edx.org/) 上的函数式编程 MOOC 的同时学习，你可以学习 Haskell 或其他几种语言中的一种。

Haskell 是每一个称职的函数式程序员都知道的语言，至少在某种程度上，但另一方面是很少有人以编写 Haskell 为生。行业中的采用非常有限(但正在增长)，所以请注意这一点:如果您学习 Haskell，那是因为学习它有价值——而不是使用它。

# 斯卡拉

Scala 是一种多范式语言，强调运行在 Java 虚拟机上的函数式编程。到目前为止，Scala 是您进行函数式编程并获得报酬的最佳机会，Scala 社区在解决函数式编程的思想(通常源于学术界)如何最好地应用于工业的问题上最为活跃。

因为 Scala 与 Java 兼容，所以你用 Scala 写代码并不意味着它是功能性的。这就是为什么我建议如果你只是在学习函数式编程，你应该使用纯函数式语言——而不是像 Scala 或 F#这样的多范例语言。

换句话说，我建议你只有在学习了一门纯函数式语言，或者已经有了很好的基础，能够区分函数式和命令式技术(例如，阅读了我的书或类似的材料)之后，才能接触 Scala。

如果你想学习 Scala，我建议你跟随马丁·奥德斯基在 [Coursera](https://www.coursera.org/) 上的 MOOCs。这些课程坚持 Scala 的功能子集。

我喜欢的两种比较年轻的函数式语言是 Elm 和 Elixir，这两种语言都受到热情的用户社区的支持，并且越来越受欢迎，尤其是在初创公司中。我希望看到这两种语言在未来几年内获得更广泛的采用和认可。

# 榆树

[Elm](http://elm-lang.org/) 是一种强类型、纯功能的客户端语言，可以编译成 JavaScript。语法很简洁，类似于 Haskell 或 F#，但是语言
和工具更加用户友好。

它包括一个负责管理状态和执行副作用的框架。结果就是程序员只写纯函数(我在我的书[第二章](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)中讨论了这些)。简单地说，Elm 让任何现有的 JavaScript 框架都相形见绌。如果你是一个全栈 web 开发人员，考虑使用 Elm 作为前端。

作为第一个要学习的函数式语言，Elm 符合很多条件，因为:

*   很平易近人
*   它在语法上类似于 Haskell(因此它将允许您理解一些文献)
*   它很有用:因为它可以编译成 JavaScript，所以对于一个思想开放的公司来说，允许你用 Elm 编写一个浏览器应用程序，甚至是浏览器应用程序中的一个小组件，比在后端采用一种新语言要容易得多

# 长生不老药

[Elixir](http://elixir-lang.org/) 是一种运行在 Erlang 虚拟机上的动态类型语言(这是基于 actor 模型的，我在我的书[的第 15 章](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)中对此进行了讨论)，因此，如果您对具有高度并发性的系统感兴趣，并且希望探索消息传递并发性，那么它非常适合。

还有许多更多的函数式和多范例语言。仅举几个流行的:OCaml、Clojure、F #——如果我忘了包括你最喜欢的，对不起！这些语言中的每一种都有其独特之处和吸引人的理由，但是我觉得上面列出的语言有一些真正让它们与众不同的地方，使它们值得首先学习。

要记住的最重要的事情是，函数式编程是一种独立于语言的思维方式。一旦你知道了这些技巧，学习一门新的函数式语言就很容易了:作为我的书研究的一部分，我学习了其中的 6 种，我可以告诉你，一旦你知道如何用函数式思维，这可以在几天内完成。
了解更多信息(和折扣代码！)下载免费的[第一章](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)或者看这个[幻灯片演示](http://www.slideshare.net/ManningBooks/a-pragmatic-approach-to-functional-programming)。

更多信息(和折扣代码！)关于 C# 中的*函数式编程，下载免费的[第一章](https://www.manning.com/books/functional-programming-in-c-sharp?a_aid=functional-programming-in-c-sharp&a_bid=ad9af506)或查看此[幻灯片演示](http://www.slideshare.net/ManningBooks/a-pragmatic-approach-to-functional-programming)。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)