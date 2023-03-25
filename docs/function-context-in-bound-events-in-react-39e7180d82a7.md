# React 中绑定事件的函数上下文

> 原文：<https://medium.com/hackernoon/function-context-in-bound-events-in-react-39e7180d82a7>

![](img/8cacaace5e959b18bd949ac14b74b9e3.png)

在 [JavaScript](https://hackernoon.com/tagged/javascript) 中，一个恼人的问题是函数上下文，也就是说在任何特定函数中`this`关键字的含义是什么。这在一个很可能将函数作为变量传递的环境中尤其令人恼火(例如 JavaScript)。

当制作 [React](https://hackernoon.com/tagged/react) 组件时，我们几乎每次制作带有某种用户交互的组件时都会遇到这个问题。考虑以下代码:

如果您尝试运行这段代码，将会抛出一个异常:

```
Cannot read property ‘setState’ of undefined
```

这是从上面代码的第 8 行抛出的。这是由于 React 事件系统调用您传递的函数时没有设置`this`的值。这个问题有两种常见的解决方法，我认为这两种方法都有问题。

第一种方法是将事件绑定到箭头函数。这保留了整个调用上下文，其中包括“This”、“super”和许多其他在普通函数表达式中没有保留的内容。看起来像这样:

```
<button onClick={() => this.handleClick()}>Click Me!</button>
```

因为这保留了调用上下文，所以它会工作得很好。但是这种方法有两个问题。首先，你需要从 arrow 函数传入所有正确的参数，本质上是作为一个代理函数(`...args`在某种程度上减轻了这一点，但它是混乱的)，其次，它在每次调用 render()时创建一个新的 arrow 函数，这在 React 应用程序中是很多的。我们不需要那些多余的垃圾。

另一种方法是使用一个**绑定函数。**这是一个函数，其中`this`的值被强制为您喜欢的任何值，但它通常用于保留当前调用上下文，如下所示:

```
<button onClick={this.handleClick.bind(this)}>Click Me!</button>
```

这实质上为 handleClick 创建了一种包装器，它在调用之前设置了`this`的值。尽管这样做，我们还是在每次调用 render()时创建一个新函数。

所有这些问题的解决方案是使用 ES7 中可用的东西(通过 babel)称为**属性初始化器。**(丹·阿布拉莫夫通过 twitter 做了一个小小的更正: **"** 类属性不是 ES7 的一部分。它们是第二阶段提案。不能保证他们会通过。”谢谢，Dan)对我们来说特别有用的是，属性初始化器只在类被实例化时才被计算，所以如果我们将一个类属性初始化为一个箭头函数，`this`的值将被保留。很整洁，是吧？

这意味着我们:

*   不需要在代理对象中传递参数
*   不要每次调用 render()时都创建一个新函数。

代码如下所示:

你还会注意到，我已经将首字母`state`赋值为属性初始化器，所以我们可以去掉整个构造函数作为奖励！

顺便说一句，如果你想知道为什么我把一个函数传递给了`setState`，而不是通常显示的 delta 对象，请在 twitter 上关注我的[，阅读我的下一篇文章😃。](http://www.twitter.com/@CalinLeafshade)

如果你喜欢这篇文章，请记得点击*💚下面的符号，以确保更多的人阅读它！*

*Steven Poulton 是一名居住在英国曼彻斯特的 web 开发人员和技术架构师。在业余时间，他喜欢制作独立音乐，制作独立游戏，和他的独立猫玩耍。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)