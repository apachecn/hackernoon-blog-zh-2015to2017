# 去抓你

> 原文：<https://medium.com/hackernoon/go-gotchas-de5b0b2f2b33>

Go 语言是由谷歌开发的，它正迅速流行起来。它也有自己的缺点，这些缺点可能会让熟悉其他表达能力更强的语言的开发人员感到沮丧。

**优势**

1.  戈兰河的黑仔特色是河道和河道。这个特性使得编写并发代码变得非常简单。
2.  生成单个可执行二进制文件，您可以直接在服务器上部署该文件，而无需任何其他外部依赖。
3.  类型系统简单明了，大多数时候都有效。
4.  更快的编译时间。有利于开发人员的工作效率。
5.  出色的测试和文档支持。
6.  补充 vim 等编辑器的 CLI 开发工具使开发变得轻而易举。

# 现在抓到你了

**依赖管理**

依赖性管理是不合格的。最近有人努力充实某种供应商管理。但它正在工作中，处于初级阶段。围棋需要 npm/yarn 或者 bundler 之类的东西。有像 glide 这样的第三方工具可以在 Go 开发者犯错的地方继续工作。仅仅基于品牌价值来期望一种语言长期流行是不现实的。

**仿制药**

这是 Go 语言的痛点之一。Go 对排序之类的普通任务有通用的支持，但是对于除了典型排序之外的任何事情，你必须为你想要使用的每种类型编写单独的代码版本。你可以使用像空接口这样的技巧来模拟泛型，但是这并不是最佳的。

Go 开发者给出的不添加泛型的理由是，这会对编译时间产生相当大的影响，并增加语言的复杂性。这可能是真的，但这个问题带有政治色彩。没有人希望看到反/亲模板无政府主义团体。:)

**错误处理**

你基本上检查一个函数返回的错误是否为零。如果它不为零，那么你要么惊慌失措，要么以正确的方式处理它。看起来简单，实际上写起来也很简单。但是它非常冗长，会让那些编写和阅读 Go 代码的人丧失斗志，尤其是在大型代码库上。

Go 应该有类似于选项类型的东西，它存在于 Haskell 和 Rust 这样的语言中。例如，Rust 标准库中的大多数函数返回[选项](http://rustbyexample.com/std/option.html)值，这些值可以是 None (null)或 Some (Value ),并提供了方便的 unwrap 函数，该函数在 null 值时会死机。当你只想在空值上终止[程序](https://hackernoon.com/tagged/program)时，这很简洁。编译器在进行模式匹配等操作时也会强制执行空值检查。在 Go 语言中，如果没有某种形式的模板支持，很难实现这样的东西。

**结论**

现在有这么多的语言正在开发中，它们提供了比 Go 语言更丰富的特性。Go 语言是现代的 Java，任何人都可以学习，特别是公司会更容易编写/维护 Go 代码。对于任何一个喜欢小变化的人来说，围棋看起来就像一把锤子。就我个人而言，我喜欢处于 Go 简单性和 Haskell 表现力中间的语言。最后，Go 开发者应该看看他们的语言在公司之外的使用模式。指望开发者直接从 url 导入第三方库有点可笑。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！