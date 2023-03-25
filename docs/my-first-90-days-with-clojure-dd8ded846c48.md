# 我在 Clojure 的头 90 天

> 原文：<https://medium.com/hackernoon/my-first-90-days-with-clojure-dd8ded846c48>

大约一年前，我辞去了我的日常工作，去做一些我想了很久的事情。我曾经摆弄过 [Clojure](https://hackernoon.com/tagged/clojure) ，但是想到要用一种我几乎不懂的语言从零开始建立一个网站，我就感到害怕。这篇文章是关于我在进入 Clojure 世界的前三个月中遇到的一些坎坷。

## 真正程序员的编辑器

尽管大多数 Clojure 开发人员会告诉您应该简单地使用自己熟悉的编辑器，但是使用 Emacs 还是有一种微妙的趋势。从一本很受欢迎(也确实很好)[的书](http://www.braveclojure.com/clojure-for-the-brave-and-true/)中的整整一章专门讲述 Emacs，到关于使用它你能有多高效的评论，这种迹象无处不在。我花了如此多的时间试图爱上 Emacs，以至于我花了比写任何代码更多的时间来学习它并根据自己的喜好定制它(似乎有无穷无尽的方法可以做到这一点)。

我最终放弃了 Emacs，选择了草书。我还玩了 LightTable、Atom 和 SublimeText。由于各种原因，没有一种像草书那样适合我。这不是对 Emacs 的诋毁。它从 70 年代就有了，这是有原因的。也许在未来的某个时刻，我会再给它一次机会。另外，你得承认，C-x M-c M-butterfly 很牛逼。

![](img/fa23895b3a1f54dcb1ac171badb8e67d.png)

[https://imgs.xkcd.com/comics/real_programmers.png](https://imgs.xkcd.com/comics/real_programmers.png)

## 强迫一个面向对象的 peg 进入一个函数洞

我做了大多数人在面对不熟悉的事物时会做的事情——通过将它变形为他们已经知道的东西来理解它。对我来说，这意味着采用在面向对象环境中有效的模式，并将它们应用到我的 Clojure 程序中。以名称空间为例。我浪费时间绘制图表，比如类图，名称空间看起来像什么，它将公开什么功能，以及它将如何与其他名称空间交互。只有当我开始强迫自己用简单的数据结构来思考时，我才觉得自己对 Clojure 的理解有所进步。帮助我的工具是 REPL。

使用 REPL 对我的开发工作流程来说是一个巨大的改变，就像曾经切换到 Git 一样。在您的 REPL 中加载您需要的东西，创建一个数据结构来存储一些测试数据，并开始编写函数来处理这些数据以获得您的最终结果。在 REPL 执行您的功能以获得即时反馈。想看看如果数据发生变化，您的函数会如何运行吗？只需在 REPL 中更新函数的输入，看看会发生什么。对我来说，REPL 就像一个交互式测试环境，除了我不是简单地写那些首先失败的测试。我通过创建小函数，逐步地、交互式地构建我的应用程序。一旦我对我所拥有的感到满意，我会将 REPL 中的内容添加到当时对我有意义的名称空间中，因为我知道我的代码库足够健壮，可以支持任何重构，以防我需要移动功能。

绘制不同名称空间如何相互依赖的图表变得不那么重要了。我意识到，我认为帮助我理解“大画面”的模式和工具，如类图，只是推迟了我本来应该做的事情——编写有效的代码，并通过组合简单、纯粹的函数来逐步构建我的应用程序。这使我想到…

# 思想和功能的纯洁性

最重要的是，这是让我成为初学者的原因。当我看着自己的代码时，我读到的关于函数式[编程](https://hackernoon.com/tagged/programming)和纯函数的一切都让我觉得自己不是一个合格的 Clojure 开发者。你在 web 应用程序中做的几乎所有事情——在 UI 中呈现元素、更新数据库、触发电子邮件等——都需要副作用。

> 套用[西蒙·佩顿·琼斯](https://en.wikipedia.org/wiki/Simon_Peyton_Jones)的话——一个没有副作用的程序只会让盒子变热。

当应用程序的任何有意义的改变都需要副作用时，函数怎么可能是纯粹的呢？对我来说，关键的见解是不要担心副作用本身，而是要确保有副作用的函数被隔离。

假设您正在编写一个处理用户注册特性的函数。您通常会验证用户输入，检查电子邮件地址是否已被占用，如果检查通过，则更新数据库并返回响应。这段代码可以这样写。您只需要知道`user-data`是一个包含电子邮件和密码的散列图。

```
(-> user-data
    validated-user-data
    check-if-email-is-taken
    register-user
    response)
```

简单地说，`->`是一个宏，它从一个函数获取响应，并将其作为第一个参数传递给下一个函数。所以`validated-user-data`是一个将`user-data`作为输入并验证值的函数。其结果被传递给它下面的函数，依此类推。`check-if-email-is-taken`仅当电子邮件地址被之前的功能视为有效时，才会触发对数据库的调用。类似地，`register-user`将仅在先前的函数确定电子邮件地址未被占用时触发数据库插入。

在这些函数中，`validated-user-data`和`response`是纯函数，使用描述其响应的名词来命名。他们完全依赖于他们的输入，只要输入保持不变，他们的反应就会相同。另外两个函数使用动词来命名，以表明它们有副作用，它们的名称描述了它们执行的操作。我不够聪明，自己想不出这个约定。如果你是 Clojure 的新手，帮你自己一个忙，读读这篇很棒的博客文章。

# 懒惰的副作用

我相信大多数新的 Clojure 程序员在某个时候都会面临懒惰序列的问题。像`map`、`filter`、`for`等操作产生一个惰性序列。

> 从文件上看…
> 一个懒惰序列是一个不*立即实现*(计算)的序列。相反，它的价值只有在你要求的时候才能实现。

一个经常被引用的例子是`range`，它返回一个无限序列。如果您带着一些参数在 REPL 中执行`range`，您将得到您所期望的。

![](img/00dfd005d80db64044df1daefbdb17e8.png)

不带任何参数地执行 range，您也会得到您所期望的(如果您阅读文档的话)，因为文档声明`range`将返回一个*无限序列*。

![](img/eae769de861a8fd61b3b3dbade788029.png)

显然，你不能在没有任何参数的情况下使用 range，对吗？不完全是。您可以编写类似这样的代码`(take 10 (range))`。

![](img/14f4cd3be94c8b8fb5e9e600112e99cc.png)

`take` 确保`range`返回的(无限)惰性序列只实现对它的要求，即序列的前 10 个元素。

正如你在上面看到的 with OutOfMemoryError，*REPL 导致惰性序列被实现*。这就是我犯错的原因，因为 REPL 给了我一种渴望评价的错觉。

当我不得不在我的 UI 代码中呈现一个集合时，我遇到了这个问题。我必须将集合的每个元素包装在一行中，并使用一个`for`来处理集合。尽管一切都应该正常工作，但什么也没有呈现出来。几个小时后，我意识到渲染问题与懒惰序列有关。我通过使用`doall`强制进行急切评估来“修复”它。即使我对 Clojure 的经验有限，我也知道这不是正确的方法。我不应该为了一个副作用而使用一个懒惰的序列操作(在 UI 中渲染某些东西就是这样)。解决方案是使用类似`doseq`、`reduce`和`transduce`的替代品。

虽然我承认在我的代码中仍然有一些注释是这样的，“修复它！！！这里不应该使用懒惰序列！!"(我们的代码中都有这样的注释，对吗？)，我已经努力只使用纯函数和懒惰的计算。

# Clojure 的杀手级应用是什么？

人们常说，Clojure 不如 Ruby 主流的原因是它没有像 Rails 那样流行的 web 框架。Clojure 社区更喜欢库而不是框架。Clojure 没有一个强迫你遵循其模式的固执己见的框架，而是允许开发人员从看似无穷无尽的只做一件事的小库中挑选。想要数据库迁移吗？使用 [Migratus](https://github.com/yogthos/migratus) 。SQL 用 DSL 怎么样？你可以选择 [HoneySQL](https://github.com/jkk/honeysql) 。一个使用函数抽象处理 HTTP 请求和响应的库怎么样？有[环](https://github.com/ring-clojure/ring)。想要一个构建工具吗？在或[靴](https://github.com/boot-clj/boot)中有[雷宁。](https://leiningen.org/)

虽然我最初很难找到使用哪个库来完成特定的任务，但我最终开始享受库的混合和匹配给我带来的自由和灵活性。对于那些不喜欢或不关心这种自由的人来说，有一些 web 框架。如果我能重新开始，我会选择鲁米诺。

# 不放弃的好处

我也许可以用 Java、Ruby 或 Groovy 更快地从零开始建立一个网站，但我肯定不会有这么大的乐趣。仅仅是找出 Clojure 的过程就几乎和用它编码一样令人愉快(我还在学习)。

所以，如果你刚刚开始使用 Clojure，希望这篇文章能在某种程度上帮助你。有时你会对错误信息感到不舒服。实际上，你会一直诅咒错误信息*(它们有那么糟糕)*。*有时候你会读到声称它是一种[即将消亡的语言](https://www.reddit.com/r/Clojure/comments/6d9say/simple_and_happy_is_clojure_dying_and_what_has/)的帖子。你的朋友可能会像这样取笑括号…*

*![](img/966a41195961c40fd4ae0cd32e856b5d.png)*

*[https://imgs.xkcd.com/comics/lisp_cycles.png](https://imgs.xkcd.com/comics/lisp_cycles.png)*

*忽略所有的噪音。只要坚持下去。最终，它会让你成为一名更好的程序员，因为学习 Clojure 会迫使你考虑用你不知道的方式解决你的问题。*

*下面是我在学习 Clojure 时发现非常有用的资源列表。*

1.  *[*https://stuartsierra.com*](https://stuartsierra.com)*—Stuart 的博客是任何试图学习 Clojure 的人的必读之作。我学到的很大一部分是阅读他的博客的直接结果。**
2.  *[*http://www . spac jer . com/blog/2014/09/12/ClojureScript-JavaScript-interop/*](http://www.spacjer.com/blog/2014/09/12/clojurescript-javascript-interop/)*—我的 app 在前端使用 ClojureScript/* [*重框*](https://github.com/Day8/re-frame) *。我给这个页面加了书签，每次需要与 JavaScript 库交互时都会回到这个页面。**
3.  *[*【https://yobriefca . se/blog/2014/05/19/the-verged-and-wonderful-characters-of-clo jure/*](https://yobriefca.se/blog/2014/05/19/the-weird-and-wonderful-characters-of-clojure/)*——我不确定这有多流行，但这篇博文帮了我大忙。我的意思是尝试搜索“Clojure 中的~是做什么用的”。**
4.  *[*https://clojure.org/api/cheatsheet*](https://clojure.org/api/cheatsheet)*——快速浏览 Clojure 中函数/宏的好方法。我喜欢将操作分成有意义的部分的方式。**
5.  *[](https://www.clojure-toolbox.com/)**—就像我之前说的，Clojure 社区更喜欢库而不是框架。这个站点列出了大部分的库和工具。***
6.  **[*http://cljsjs.github.io/*](http://cljsjs.github.io/)*——除非你正在使用 ClojureScript，否则你不需要这个网站。我花了一段时间才明白这里发生了什么，但这很有帮助。***