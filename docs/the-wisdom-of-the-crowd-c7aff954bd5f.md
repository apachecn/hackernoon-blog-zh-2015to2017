# 群众的智慧

> 原文：<https://medium.com/hackernoon/the-wisdom-of-the-crowd-c7aff954bd5f>

![](img/ba5ffed407464acd0ed3c19e5d591f76.png)

如果你在谷歌上搜索“最佳编程语言”，你会发现许多博客列出了他们的最佳推荐。然而，他们的“最好”总是等同于“最受欢迎”。受欢迎程度不是衡量什么是最好的唯一标准。语言设计的质量如何？程序员生产力的程度呢？好玩的因素呢？教学或教学目的的适用性如何？

受欢迎意味着学习这些语言更容易找到有报酬的工作。然而，这样一种重商主义的态度否定了你诸如 Elixir、Haskell、Julia、Rust 和 Smalltalk 等优越技术的所有好处。此外，在某些情况下，流行使你陷入不愉快或更令人沮丧的编程经历，就像 [PHP、JavaScript 和 C++](/smalltalk-talk/the-three-worst-programming-languages-b1ec25a232c1) 的情况一样。保持流行会阻碍未来编程技术的进步。

> Slant 是一个产品推荐社区，旨在让您毫不费力地找到最适合自己的产品、应用或游戏。再也不用花时间在充斥着论坛和评论的标签上做产品研究了。

[斜面](https://www.slant.co)在选择好的开发工具时非常有用。我在 Slant 网站上发现了两个有趣的问题，它们提供了惊人的答案。[第一个](https://www.slant.co/topics/5984/~productivity-enhancing-well-designed-and-concise-rather-than-just-popular-or-time-tested-programming-la)，

*什么是“最好的”(提高生产力、设计良好、简洁，而不仅仅是流行的或经过时间考验的)编程语言？*

和第二个[，](https://www.slant.co/topics/25/~best-programming-language-to-learn-first)

*先学什么编程语言最好？*

对于第一个问题，排名前十的结果是(更新于 2017 年 10 月 23 日):

1.  **闲聊**
2.  计算机编程语言
3.  偷
4.  长生不老药
5.  球拍
6.  计划
7.  普通 Lisp
8.  哈斯克尔
9.  锈
10.  榆树

对于第二个问题，十大结果是:

1.  计算机编程语言
2.  **闲聊**
3.  球拍
4.  计划
5.  C
6.  红宝石
7.  左上臂
8.  榆树
9.  Java Script 语言
10.  戈朗

Smalltalk 在这两方面都做得非常好，这是应该的。Smalltalk 是一种非常好的编程语言。这是一种极其高效、优美优雅的语言。虽然它目前并不是特别受欢迎，但它肯定是经过时间考验的，因为 Smalltalk 已经被全球各地的企业商业化使用了 30 多年，包括像摩根大通、Desjardins、UBS、Florida Power & Light、德州仪器、Telecom Argentina、[Orient Overseas Container Lines](http://smalltalk-lang.blogspot.ca/2017/04/a-smalltalk-success-story-orient.html)、Siemens AG、 [ALLSTOCKER](http://pharo.org/success/AllStocker) 等等。在我的祖国，Smalltalk 被加拿大国家密码机构通信安全机构(CSE)所使用。

事实上，在 21 世纪初，美国联合军队使用 Smalltalk 编写了一个名为 JWARS 的百万行战斗模拟程序。它实际上胜过了美国空军用 C++编写的类似模拟程序 STORM。这本身就是语言能力的惊人证明。

Smalltalk 也非常适合教授编程。它是由 PARC 施乐公司的艾伦·凯和他的团队为教育年轻人而设计的。它的语法非常简单，可以总结在明信片的背面！因为 Smalltalk 是一种最高级的面向对象语言，所以没有更好的方法来学习这种非常重要的编程范式。

群众确实是聪明的。这种智慧也出现在最新的 StackOverflow 调查中。在“最受欢迎的语言”下，Smalltalk 明显排在第二位(在 Rust 之后，在 TypeScript、Swift、Go、Python、Elixir 和 C#之前)。这表明使用过 Smalltalk 的人喜欢这种语言，并且忠于它。

[![](img/e832362f1d21eaf6a0a8891e7750bdda.png)](https://medium.com/p/apple-has-been-using-smalltalk-for-years-f9df22dd29fe)

这也表明，通过推理，编程社区并不知道 Smalltalk 有多好。这个社区的大多数人对此一无所知。如果他们尝试 Smalltalk 编程，这种语言很可能会流行起来。

我认为这是一个非常合理的假设。

然而，我发现有些群体智慧的例子是值得怀疑的。*为什么推荐 C (#5)作为初学者语言？*想不通为什么。这是一种起源于过去的古老语言。它非常基于文件，使用头文件实现模块化；头文件呈现了各种令人头疼的依赖性。它对人工内存管理的依赖与现代编程实践不一致；其他主要语言都使用垃圾收集或引用计数。它对内存指针的使用对于初学者来说是有问题的；指针很危险。它的预处理器和宏系统粗糙得令人难以置信。**它完全缺乏对面向对象编程或函数式编程的支持，这两种编程是当今业界最重要的范例。C 是最纯粹的程序编程语言；没有比这更不合时宜的了。**

可以，C 可以教你低级的计算机架构。汇编语言也可以。这个主题最好留到以后作为高级主题。

不要误解我:我热爱 c。在我的职业生涯中，它为我的提前退休付出了代价！但是我肯定不会推荐它作为初学者的第一语言。

*为什么 JavaScript (#9)被推荐为初学者语言？？？对于初学者来说，JavaScript 是一种可怕的第一语言。会教各种坏习惯。这种语言在很多方面都很疯狂。需要我指出 JavaScript 已经成为[行业](https://youtu.be/2pL28CcEijU) [笑话](/javascript-non-grata/down-the-javascript-hole-f45417ce7d1f)的[笑柄的无数](https://youtu.be/D5xh0ZIEUOE) [WAT](https://www.destroyallsoftware.com/talks/wat) 和 [WTF](https://wtfjs.com) 吗？JavaScript 充满了内部不一致和陷阱(杰夫·沃克称之为“雷区”)。*

尽管 ECMA TC39 对这种语言做了所有的“改进”，他们仍然没有修复从根本上破坏的语义。如果不破坏网络，他们就做不到。ES5.1，ES6，ES7，ES8……都是给猪涂口红而已。

![](img/b8a86a4d856fa7fb8915954b75912538.png)

听着，我明白。如果一定要做 web 编程，一定要学*一些* JavaScript。(而且我强调的是“一些”；我做网络开发，但是我很少使用 JavaScript。)但这并不能让 JavaScript 成为一门好的教学语言。

最后，我对 Common Lisp (#7)作为一种提高生产力、设计良好且简洁的编程语言的建议感到困惑。许多著名的程序员会强烈反对。如果你喜欢 Lisp，我强烈建议 Scheme 或者球拍，而不是 Common Lisp。

> 智慧可以在人群中找到，但人群不是不会犯错的。