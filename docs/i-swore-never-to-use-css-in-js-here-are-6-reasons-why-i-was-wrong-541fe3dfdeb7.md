# 我发誓绝不在 JS 中使用 CSS，以下是我错的 6 个原因

> 原文：<https://medium.com/hackernoon/i-swore-never-to-use-css-in-js-here-are-6-reasons-why-i-was-wrong-541fe3dfdeb7>

![](img/eeb4b8e4964755b0604636a8db4029e9.png)

# JS 中的 CSS

当我第一次听到这个想法时，我很震惊。几十年来，最佳实践是将 HTML、CSS 和 JS 的关注点分开。你知道，所以你可以只改变 CSS，而不需要接触 HTML 和 JS，对不对？我想我们都在某个阶段发现这在真实的项目中是不正确的。

React 来了，用 JSX 把 HTML 注入我们的 JS。下一阶段是将 CSS 注入 JS。

这就是为什么我认为这可能是正确的方式:

1.  **一个更强大的 CSS。** LESS/SASS 被发明出来，通过为 CSS 编译新的编程语言来为 CSS 增加功能。使用 JS 而不是 LESS/SASS 给了我们同样的语言，我们知道它比其他语言更强大。
2.  **组件。** JS with React 使用组件，JS 中的 CSS 使向组件添加特定 CSS 变得简单。“我应该在哪里编写这些 CSS 规则，以及如何确定它们的范围？”简单地消失——样式就在组件本身中。额外的好处是，共享这个模块是零依赖的，不需要添加一个<链接>标签到 css 文件。
3.  **少造型码**。添加新的 CSS 是很可怕的——你永远不知道会发生什么变化。所以你添加了更多的 CSS。将样式保持在 JS 中应该会减少样式代码。
4.  **干。很多时候，JS 代码需要访问 CSS 文件中的信息(尺寸、颜色等等)。现在都是 JS，所以你永远不会在 CSS 和 JS 文件中写相同的值。**
5.  **开发者体验。**如果你已经设置了 HMR——你会享受到对 CSS(JS 内部)的修改会被热重新加载到浏览器中，不需要重新加载。
6.  **逐步实施**。没有必要对你的代码库做 100%的改变。你可以把它和普通的 CSS 文件放在一起。

我目前选择的工作是 [**glamor**](https://github.com/threepointone/glamor) 。我真的很喜欢这个 API，它很快。我确信在这个领域会有更多的进步。CSS 的未来终于走上了一条激动人心的道路。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果您喜欢这个故事，我们建议您阅读我们的最新科技故事和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实视为理所当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)