# 在 ReactJS 中使用本地条件语句

> 原文：<https://medium.com/hackernoon/use-native-conditional-statements-for-the-win-in-reactjs-4e8c8f8d479c>

![](img/b246ea09dcf7bc865a913d46ef602e80.png)

在 [ReactJS](https://hackernoon.com/tagged/reactjs) 中，我们可以使用组件渲染方法导出一些标记。到目前为止还不错，但是当我们想在这里应用一些 if…else 逻辑时会发生什么呢？

来自不同背景的许多开发人员可能会寻找另一种选择**ng-if = " some logic here "(angular js)**甚至**{ { if some logic here } }(ember js 中的 handle bars)**。答案是没有这种东西。这听起来可能很奇怪，但是在 ReactJS 中，我们可以通过使用非常简单的 JavaScript 来应用一些 if…else 逻辑。

# 简单 if 逻辑

当我们想要应用一些基本的 if 逻辑时，我们可以创建一个包装器方法，在其中运行我们的逻辑，然后像我们通常做的那样呈现该方法:

# 具有逻辑 AND 的简单 if 逻辑

这看起来不错，满足了我们的需求，但它有一些额外的代码，我们可以通过使用逻辑 and 运算符来删除。让我展示给你看:

它之所以有效，是因为在 JavaScript 中，`true && expression`总是计算为`expression`，而`false && expression`总是计算为`false`。

因此，如果条件为`true`，紧接在`&&`之后的元素将出现在输出中。如果是`false`，React 会忽略并跳过。

不错吧？如你所见，我们可以通过对简单的 if 逻辑使用逻辑 AND 运算符来保持简洁。

# 带逻辑或的简单 if 逻辑

同样，当缺少一个选项时，我们可以使用逻辑 OR 来呈现一些默认内容:

如果我们的用户没有工作，那么我们将打印他失业。

# 高级 if…else 逻辑

让我们创建一个更高级的例子。有些时候，我们正在加载一个用户集合，所以我们想显示一个预加载程序，直到这个过程完成。当这结束时，我们想要呈现那个列表或者声明我们没有找到任何用户。我们如何做到这一点？

我们又一次创建了一个包装器，并在其中运行了一些高级的 if…else 逻辑。这是我们的无状态组件在这里呈现的内容，因为我们返回了那个方法。

# 高级 if…else 逻辑与生活

我们可以通过使用 IIFE(立即调用的函数表达式)并省略那个包装器来使它变得更简单。让我展示给你看:

通过使用一个生命，我们不需要使用包装方法，但是我们可以通过在那里运行我们先进的 if…else 逻辑，立刻导出那个生命。

# **带有 do {…}表达式的高级 if…else 逻辑(阶段-0)**

你可以使用目前(2017 年 3 月)处于第 0 阶段的实验性`do {...}`表达式，而不是使用 IIFE:

记住你需要在你的`.babelrc`设置中使用预设的`babel-preset-0`或`transform-do expressions`插件。

# 带有三元运算符的高级 if…else 逻辑

我知道你预见到了。三元运算符在大多数情况下是救命稻草，我们可以嵌套多个表达式。我不建议这样做，因为它真的很难看，而且你的代码变得不可读，但是知道你可以在 ReactJS 中使用它并做你想做的任何事情是很好的:

# 更多条件语句

在上面的例子中，我们可以使用更好的东西，如 switch 或 if…else if…else etc ReactJS 让我们可以灵活地使用本地条件语句进行过滤，而不必掌握带有自定义指令和表达式的新模板语法。这使得 ReactJS 极其强大。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)