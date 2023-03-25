# 类型战争:帝国反击战

> 原文：<https://medium.com/hackernoon/type-wars-the-empire-strikes-back-85a5a172404e>

![](img/9a7a8643cb36a5c625e71f29191f8d2c.png)

罗伯特·c·马丁(又名“鲍勃叔叔”)早在 2016 年 5 月就在 Clean Code 博客上写了一篇题为“ [Type Wars](http://blog.cleancoder.com/uncle-bob/2016/05/01/TypeWars.html) ”的文章。在这篇文章中，他清楚地阐述了通过使用*测试驱动开发*，或 TDD，动态类型语言如 Smalltalk 和 Python 可以像静态类型语言如 Java 和 C++一样安全可靠。鲍勃叔叔说，

> 你看，当一个 Java 程序员习惯于 TDD 时，他们开始问自己一个非常重要的问题:“当我的单元测试已经在检查一切的时候，为什么我还要浪费时间去满足 Java 的类型约束？”那些程序员开始意识到，通过切换到动态类型的语言，如 Ruby 或 Python，他们可以变得更加高效…
> 
> 如果你有 100%的单元测试覆盖率，你不需要静态类型检查。而且，正如我们反复看到的，接近 100%的单元测试覆盖率能够并且正在实现。更重要的是，这一成就的好处是巨大的。
> 
> 因此，我预测，随着 TDD 越来越被接受为必要的专业学科，动态语言将成为首选语言。最终，说话不多的人会赢。

人们普遍认为动态类型的语言比静态类型的语言更有效率。从轶事和统计数据来看， [Smalltalk 被认为是所有语言中效率最高的](/smalltalk-talk/smalltalk-s-proven-productivity-fe7cbd99c061)。

像 Python 一样，Smalltalk 非常容易学习。它实际上没有语法。Smalltalk 语法可以放在明信片的背面！

此外，就其本质而言，阅读和理解 Smalltalk 代码更容易。在 f(x，y，z)等传统的函数符号中，参数 x、y 和 z 没有上下文来理解它们的含义或意图。他们要求的顺序也不清楚。但是在 Smalltalk 语法中，*关键字消息*提供了合理的上下文，因此参数的顺序和意图都很清楚: **f: x 关键字 1: y 关键字 2: z** ，其中关键字 1:和关键字 2:可以是有意义的名称。

Smalltalk 编程实际上是禅宗式的。这种语言实际上没有造成任何认知摩擦。

Smalltalk 有一个非常棒的“实时编码和调试”IDE/运行时环境，使得编程非常快速和高效。您可以对正在运行的程序进行实时更改，并立即看到结果。这几乎完全消除了妨碍几乎所有其他编程语言的编辑-编译-测试-调试循环。而且非常容易使用。在现代编程世界中，没有什么比这更好的了。

Smalltalk 的映像持久性允许您保存应用程序的执行状态，并在稍后的时间准确地从您停止的地方恢复执行！这非常方便，节省了大量时间。

我很高兴看到鲍勃叔叔已经认识到 Smalltalk 的潜力和光明的未来。这与他在 2009 年著名的 RailsConf 演讲几乎是 180 度的大转弯。

我不得不反对的一点是，鲍勃叔叔断言 Smalltalk 作为一种语言曾经“消亡”。说一种语言消亡是什么意思？它不再被使用了吗？只有一小部分疯子才会用它？

在 IT 界，编程语言很少消亡。即使在今天，Cobol 仍然在发挥作用，尽管主要是在遗留的情况下。APL 实际上已经死亡，但它的后代，如 J 和 K，却活了下来。Forth 对于嵌入式应用来说仍然是一种可行的语言。Fortran 仍然是一种重要的科学语言。

如果 Bob 叔叔的死意味着 Smalltalk 不再是 20 世纪 80 年代和 90 年代的首要编程语言，那么这一点很难反驳。不过话又说回来，Smalltalk 陪伴着很多很多其他远离主流的语言，比如 Ceylon、Clojure、Common Lisp、Crystal、Dart、Erlang/Elixir、F#、Haskell、Haxe、Julia、Kotlin、OCaml、Rust、Scheme/rack 等语言。*这些语言都消亡了吗？？？*

[Smalltalk 实际上比大多数非主流语言享有更多的商业用途。欧洲 Smalltalk 用户组，简称 ESUG，是一个庞大而活跃的组织。Pharo 项目是这种语言的一条令人兴奋的发展道路。Redline Smalltalk 承诺将在今年晚些时候为 JVM 发布 **Smalltalk！**](/smalltalk-talk/who-uses-smalltalk-c6fdaa6319a)

> 关于我死亡的报道被大大夸大了。
> ~马克·吐温

所以让我们给 Smalltalk 一个交代。Smalltalk 不仅仅是一个幸存者，它还繁荣了起来……到了任何非主流语言繁荣的程度。我认为这是一种比大多数年轻的暴发户更好的编程语言。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)