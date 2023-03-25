# 在工具上

> 原文：<https://medium.com/hackernoon/on-tooling-1676f1c28c53>

我开始用两种方式编码。当我 13 岁的时候，我尝试了一点 c 语言。我没有走多远，事实上，在我感到困惑和无聊之前，我写了这样的东西:

![](img/7b012dbc7bc9058e1f3ca9b191ef100b.png)

Basic C program — Hello World.

我尝试的另一种方法是使用 iWeb，一种可视化的所见即所得的网站编辑器。我当然用它吐出来的 CSS 和 HTML 到处乱砍。但是我问你——你能猜到我更喜欢哪一个吗？

工具+语言，而不仅仅是语言本身，让一个开发者变得高效。在某个地方，我觉得我们失去了它。工具可以弥补语言中的缺陷，但也可以提高刚刚好的语言的生产率。

昨天，当我用 Xcode 8.1 编写 Swift 3 代码时，编辑器崩溃了。重启 Xcode 并没有解决这个问题，我甚至无法使用 autocomplete 来填充一些重要的块语法。

雪上加霜的是，我当然也不会重构我的东西！事实上，尝试使用 Xcode 8(7 或 6！)‘重构’菜单，你会得到这个令人沮丧的对话框。

![](img/66ab4974af8195b59c9266cdd29dc57a.png)

Error message stating Xcode can’t refactor Swift

> 我们是 Swift 的第三年。版本 3。工具还是很烂。

我刚刚去了新西兰的[微软 Ignite](http://samjarman.co.nz/blog/microsoft-ignite-first-impressions/) ，我不得不称赞微软是工具制作如此出色的公司。我花了几年时间。Net，坦白地说，我喜欢它。C#是一种可靠的工作语言，具有可爱的面向对象和函数(LINQ)功能，围绕着它，有一堆令人敬畏的工具，如 Visual Studio、intellisense、 [ReSharper](https://visualstudiogallery.msdn.microsoft.com/EA4AC039-1B5C-4D11-804E-9BEDE2E63ECF) 等等！

坦白地说，我花了一周的时间非常嫉妒这些工具。从 VS 你可以直接发布到 Azure 和其他各种东西。甚至 Azure 也是一个出色的工具集合，它们比一盘 linguini 更好地交织和集成。

Typescript，微软新的类型化 javascript 超集，实际上是他们所谓的[语言服务器协议的引领者，](https://github.com/Microsoft/language-server-protocol)意思是只要你的编辑器使用该协议，你就可以获得所有的功能，比如智能感知和自动完成。连[锈都跟着](https://internals.rust-lang.org/t/introducing-rust-language-server-source-release/4209)。这样的创新。哇哦。

在另一个阵营，我们在 Android land 的友敌有 Android Studio 是超棒的，他们爱哀叹 Eclipse 还是比 Xcode 好。当然，他们不得不忍受 Java，但是自从 Java 7 问世以来，它已经变得非常好了，而 Java 8 刚刚问世，看起来也很棒。见鬼，甚至 PHP 从 v5 开始就很好了。

你可能会说“哦，我对 IDEs 太好了，我只是用 Vim”。当然..但是你想告诉我你没有 vim 插件吗？没错。工具。

听着，我不是想尝试语言创新(这很棒！)，但是我们对构建新语言的痴迷，例如 Swift 及其特性*必须与工具的创新相匹配。重构、重命名、样式检查、单元测试等等都必须在新版本中实现。 [Rust 做得很好，但有错误](https://blog.rust-lang.org/2016/08/10/Shape-of-errors-to-come.html)。埃尔姆也在效仿。有人可能会说，Go 在关注真正快速的编译时间和标准化的语法格式(通过 *gofmt* 工具)方面有显著的影响。Swift 为什么滞后？*

当然，官方的说法似乎是“它在列表上”，但我认为我们至少可以在 Swift 的几个版本之前进行类重命名。他们增加其他语言的新特性是好事，但以牺牲周围开发者的体验为代价就不好了。也就是说，苹果真的希望我们支持 Swift 吗？服务器和其他地方呢？

还是不明白吗？这样如何:既然我们喜欢称自己为工程师，那就用土木工程朋友更直接的比喻吧。雨燕是一种新型材料，但我们没有东西来塑造它。工具和材料是密不可分的，我们已经忘记了这一点。和软件工程没什么区别。

***嘿克里斯·拉特纳。*** 热爱您的工作，但是明年我们不需要一周的迁移过程，而是需要一些更好的 Swift 工具，怎么样？

**别忘了，工具+语言，而不仅仅是语言本身，能让开发者变得高效。**

¯\_(ツ)_/¯

/rant
所以这就是我目前对这个话题的*思考。你有什么想法？在 Twitter 上发表评论或 ping 我——我是 [@samjarman。](https://twitter.com/samjarman)*

*PS:这最初发表在我的[博客](http://www.samjarman.co.nz/blog/)上。*

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个大家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)*