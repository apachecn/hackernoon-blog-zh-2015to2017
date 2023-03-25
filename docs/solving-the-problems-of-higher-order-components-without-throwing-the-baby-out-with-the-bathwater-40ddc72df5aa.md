# 解决高阶组件的问题，而不用把婴儿和洗澡水一起倒掉

> 原文：<https://medium.com/hackernoon/solving-the-problems-of-higher-order-components-without-throwing-the-baby-out-with-the-bathwater-40ddc72df5aa>

![](img/ac7aa85cf0b34973d1493cc0bbb56390.png)

在最近的文章中有一些对高阶组件的批评——特别是“间接”和“命名冲突”,它们实际上不是特设模式固有的问题，而是可以容易避免的使用错误。

## 显式定义属性名

这是重新组合的

关于道具`counter`和`setCounter`的来源没有混淆，而且既然你能控制道具的命名，碰撞也不是问题。

让我们将同样的原理应用于迈克尔杰克逊的[使用渲染道具](https://cdb.reacttraining.com/use-a-render-prop-50de598f11ce)中的`withMouse`示例！

因为你声明了你将要收到的道具的名称，所以不存在间接或冲突的问题。

## 如果你的 HoC 需要返回多个道具怎么办？

想象一下，`withMouse` HoC 返回了两个 props - `position`，它包含了`x`和`y`坐标，以及`changeCounter`，它记录了位置改变的次数。

要求显式命名每一个属性会使 HoC 与它提供的每一个附加属性一起使用时更加麻烦。但是如果不明确命名这些道具，您可能会得到如下结果:

你只能猜测`position`和`changeCounter`道具来自`withMouse`而不是其他的 hoc，并且你必须假设没有其他的 hoc 返回相同名称的道具。

## 命名空间高阶组件

这种模糊性/间接性和潜在的冲突可以通过命名空间来解决:

您可以更进一步，命名所有的 HoC(这适用于任何返回 props 的 HoC)

Props 现在可以通过名称空间追溯到提供它们的 HoC，由于名称空间是在 HoC 被组合时设置的，所以冲突也可以避免。

`namespace`函数的代码实际上非常简单

Credits to [Jeffrey Burt](https://twitter.com/jephuff) for the [initial code solution](https://github.com/acdlite/recompose/issues/358#issue-221319488) and [Ivan Starkov](https://twitter.com/icelabaratory) for [making it more readable](https://github.com/acdlite/recompose/issues/358#issuecomment-293637999)

这里有一个沙盒，里面有所有的东西

## 渲染道具做得更好

渲染道具确实有好处，因为你可以把它分散到渲染函数中。M *y* 朋友[阿历克斯·威尔默](/@benevolentNinja)有一个很好的例子展示了它的效用，我希望它能很快变成博客的形式，但同时这里有一个更简单、更做作的例子——

假设您在 JSX 的一个大型街区内，并且有一些硬编码的和重复的变量(在本例中为“东亚”)，您可能希望停止使用这些变量。

你有几个选择。

1.  在函数的顶部向上声明变量(坚持“在靠近使用变量的地方声明变量”的人可能不喜欢这样)
2.  创建一个单独的组件并传递重复的值(这有点麻烦)
3.  创建一个名为`<Assign/>`的组件，我们可以通过 props 为其赋值，然后在 render 函数中接收这些值。

让我们进一步说，这些值来自异步请求。您可以制作一个`<Resolve />`组件来完成这项工作…

Find the implementation of the `<Resolve/>` component at [https://codesandbox.io/s/w8vp4k2pw](https://codesandbox.io/s/w8vp4k2pw)

我不确定使用这些组件是否是一个好主意(咨询你最近的思想领袖😄)，但它展示了渲染道具允许某种灵活性的地方，而据我所知，HoC 是不允许的。

## 摘要

我们似乎经历了

1.  “哈，我不知道你能做到这一点”
2.  “这似乎是一个很好的解决方案”*(对于某些使用案例)*
3.  “这就是客观上的优解！”
4.  “存在权衡和陷阱。这是该解决方案更有意义的地方，这是您需要小心的地方，这是最好使用其他方法的地方。”

我认为我们正处于高阶元件的第 4 阶段，有可能陷入的[陷阱，但也有解决方案](https://reactjs.org/docs/higher-order-components.html#caveats)。有了渲染道具，我目前看到的文章好像还停留在阶段 2 和阶段 3 之间。

一切都是交易。我怀疑一个被描述为全是好处而没有成本的解决方案——这通常意味着成本还没有被发现😄

欢迎在评论中告诉我我错过了什么，[在 reddit 上讨论](https://www.reddit.com/r/javascript/comments/75hkrk/solving_the_problems_of_higher_order_components/)，或者发微博给我 [@CheapSteak](https://twitter.com/cheapsteak)

本文由[**npmcharts.com**](https://npmcharts.com/)**赞助📈**

这是一张[重组](https://www.npmjs.com/package/recompose)的互动下载图