# 编写流畅的功能代码

> 原文：<https://medium.com/hackernoon/writing-fluent-functional-code-384111431895>

![](img/bf51966106c7363db7154b0319a962a0.png)

Fluent code should make you feel happy, like this image does.

[流畅 Api](https://www.martinfowler.com/bliki/FluentInterface.html) 是读起来像一句话的代码。如果您曾经编写过测试代码，您可能知道这一点，它读起来像一个句子:

如果您使用过表达式生成器，您可能会注意到它非常容易阅读:

要启用这个 API，您可以使用一个从它的每个方法返回`this`的`class`:

非常简单，实际上很容易实现，但是它也有缺点——必须从每个方法返回`this`有点烦人。

如果你像我一样，不喜欢使用`this`，你可能已经尝试过寻找替代品。

最直接的函数式方法，也是我认为最好的方法，是对无状态 setters 使用“pipe()”。我用拉姆达来介绍一下:

这里的想法是，`set(lens, value, src)`是一个接受

*   `lens`(基本上是一种定义路径的方式)，我会在下一段中稍微解释一下。
*   `value`:写入镜头定义路径的值
*   `src` : set 会根据源对象返回一个新的对象(永不变异)，带有新的属性和值。

最后，这与类构建器的结果相同，但是更加简洁，不需要处理多余的`this`。

关于这种方法的几点注意事项:

1.  现在对 Typescript 来说没有什么用，因为 curried 函数需要使用[变量类型](https://github.com/Microsoft/TypeScript/issues/5453)，而 Typescript 目前还不支持它。尽管它在[路线图](https://github.com/Microsoft/TypeScript/wiki/Roadmap)上。你可以在这里[阅读](https://gist.github.com/donnut/fd56232da58d25ceecf1)了解问题。
2.  如果您需要写入一个比单个级别更深的路径，对于(一个虚构的)示例`site.services.securityEnabled`，您应该使用`lensPath`:

好的，如果你正在阅读这篇文章，你可能已经注意到，虽然它看起来很棒，但这种模式不允许你链接，虽然我建议流利的定义应该扩展或放在更广泛的上下文中，但我们中的一些人需要链接才能在阅读代码时感到舒服。

让我们看看如何在允许链接的同时保留表达式生成器的功能。

这是一个基于泛函的表达式生成器的简单例子:

它仍然易于阅读，开始看起来类似于类版本，但是不需要从每个方法返回`this`。

仍然有一些重复，但是我们可以通过一些简单的重构来消除它:

它有它的优点和缺点，我相信它可以做得更好看。(随意建议)

但底线是——它是一个无状态的、功能性的、流畅的、表达构建器。

WDYT？