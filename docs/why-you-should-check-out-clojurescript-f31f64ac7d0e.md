# 为什么您应该查看 ClojureScript

> 原文：<https://medium.com/hackernoon/why-you-should-check-out-clojurescript-f31f64ac7d0e>

![](img/02de57485790cec7ffc89a0fdb317045.png)

最近，我坐下来整理了一份对我的日常工作产生积极影响的编程特性列表:

-函数式编程
-一级不变性
-一级 REPL
-面向数据编程
-静态类型化

就是这样。结束了。

您可以在 TypeScript 或 [JavaScript](https://hackernoon.com/tagged/javascript) + Flow 中获得所有这些要点。但我撒谎说已经结束了。还有一个要点:

-简单

# Clojure 很简单

《Clojure 的作者 Rich Hickey 在他的演讲“[简单易做](https://www.infoq.com/presentations/Simple-Made-Easy)”中做了最好的表达。演讲主要是关于架构的简单性，但是语言本身在语法上也是简单的。

特殊符号屈指可数。其他的都只是数据+函数的组合。async/await 没有特殊的语法。比较或条件没有特殊的语法。都只是函数(或者宏)构图而已。

多亏了 Clojure，每次我涉足不同的语言——即使客观上很好的语言——相比之下都觉得很脏。

# Clojure 是面向数据的

我有大约 15 年的 C#经验。我目前的工作是 Rails 堆栈。Rails(以及 C#和大多数 OOP 堆栈)鼓励的一件事是数据隐藏。“在这里插入 fanci API 调用”得到什么回报？一些物体。它的形状是什么？谁知道呢？您可以访问哪些数据？享受在继承、公共、私有、受保护的方法和访问器等各层中涉水的乐趣。为什么您想要的那部分数据隐藏在私有方法后面？啊！

另一方面，Clojure 是一个暴露狂。它向全世界展示了一切。这是一件好事。“在这里插入 fanci API 调用”得到什么回报？地图、列表、集合或向量。几乎可以保证。数据的形状是什么样的？它是基本原语的简单组合，可以很好地打印到 REPL，并且可以使用一组简洁、通用、易于理解的函数进行访问和操作。

> 面向数据的编程可能是我最喜欢 ClojureScript 的一点。这听起来可能不像是一个游戏规则改变者，但它确实是。

顺便提一下，我认为面向数据的设计与静态类型有些不兼容。很难静态分析地图列表的形状。我认为这是可以做到的，但我怀疑这将否定 Clojure 的动态系统所获得的一些(或大部分)组合优势。如果我必须在面向数据编程和静态类型之间做出选择，我会选择面向数据。

# 首先，Clojure 是不可变的

这与前一点非常吻合，可能会争夺“我最喜欢的东西”这一类别。在 Clojure 中，您几乎不会改变数据。你只是改变它。这使得 Clojure 代码比典型的 mutate-all-the-things Ruby/JavaScript 代码更容易预测。

这里有一个说明性的轶事。我最近在我们的支付处理逻辑中发现了一个 bug。这是因为 Stripe JavaScript 库改变了传递给它的对象。Stripe 实际上是从我的对象中删除属性并添加新的属性。多谢了，条纹！*

*郑重声明，我*爱*条纹。继续前进。

在 Clojure 中，状态的突变得到了很好的控制和理解。同样，这是一个游戏规则的改变。

# 但是等等，还有更多…

已经有很多关于函数式编程的文章，所以我在这里就不再赘述了。这不是全部，但总的来说，我发现由小而简单的函数组成的代码库比由类层次结构、继承链等组成的代码库更容易阅读、维护和推理。

一个好的 REPL 也很难被击败，Clojure 得到了他们。

ClojureScript 的动态特性使得 JavaScript 互操作比静态类型的替代方法容易得多。

ClojureScript 似乎拥有所有可选功能 JavaScript 栈中最经得起考验的前端库。

Clojure 的社区充满活力，乐于助人，热情好客(在 Slack 和 Reddit 上查看)。

想要更多的理由来看看 ClojureScript 吗？我把你转给德里克·斯拉格:

Still skeptical? Take a look…

# Clojure 的土地并不总是阳光明媚

应该很明显我喜欢 Clojure 和 ClojureScript。但如果我说一切都很美好，那我就是在撒谎。以下是我发现的一些缺点和缓解措施。

## 启动时间很慢

并且它需要 JVM(在我看来，这比缓慢的启动更糟糕)。自托管的 ClojureScript 消除了这两个问题，但它离成熟还有一段距离。

查看[普朗克](http://planck-repl.org/)或 [lumo](https://github.com/anmonteiro/lumo) 快速启动 ClojureScript REPLs。

## 错误消息是一头野兽

如果你不是一个 Java 爱好者(我不是)，那么当你第一次看到 Java 堆栈跟踪时，你可能会吓一跳。当不可避免地出现这种情况时，这里有一些简便的技巧，可以帮助你弄清事情的真相:

-[http://Eli . the green place . net/2017/notes-on-debugging-code/](http://eli.thegreenplace.net/2017/notes-on-debugging-clojure-code/)
-[https://aph yr . com/posts/319-clo jure-from-the-ground-up-debugging](https://aphyr.com/posts/319-clojure-from-the-ground-up-debugging)

## 无效的

嗯，它是建立在 JavaScript 和/或 JVM 之上的，所以空值是生活的一部分，但如果不是这样就好了。

## (或文档“待办事项…”)

你喜欢文档吗？是啊。我也是。Clojure 有一些很棒的文档。但它也有许多可怜的或不存在的文件。(可以有很多不存在的文档吗？不知道。英语是我的第一语言，但我说不好…)

也就是说，我还没有发现这是一个太大的问题。不管遇到什么阻碍，我通常都能找到出路。代码通常很容易查看和推理。如果我真的需要帮助，Reddit 和 Slack 上的人都非常乐意。

## 我认为是缺点但不是的三件事

静态打字。我真的不怀念。我希望 Clojure 可以把它撒进去，但它并没有我想象的那么大。随着我的应用变得越来越复杂，我怀疑 Clojure.spec 会有所帮助。

没有好的调试器，但是我发现(到目前为止)函数式编程+一个好的 REPL 比可变的命令式/OO 编程+调试器要好。

括号。是啊。每个人都抱怨他们。你猜怎么着有了一个好的编辑，你不会真的去想或注意他们。而且语法的规律性是*物超所值。*

# 试一试

如果你感兴趣(你应该感兴趣)，请到这里来试试:

https://learnxinyminutes.com/docs/clojure/-[-
-](https://learnxinyminutes.com/docs/clojure/)[http://www.braveclojure.com/](http://www.braveclojure.com/)

如果没有别的，Clojure/ClojureScript 值得一看，仅仅是为了体验它。如果你还没有，我鼓励你去看看。解决最初的棘手问题。会有回报的。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！