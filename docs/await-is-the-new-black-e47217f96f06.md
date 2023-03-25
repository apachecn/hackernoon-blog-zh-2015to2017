# 等待是新的黑色

> 原文：<https://medium.com/hackernoon/await-is-the-new-black-e47217f96f06>

![](img/bf44366032ccec9575c9536de2e30252.png)

This guy uses await. Look how happy he is.

你知道 es7 的新功能有多棒吗？表演时间到了。

## 1.开始的时候，有回调

想象一下，你被告知要订购一个亚马逊包裹，然后去邮局拿，然后回家打开它。

简单的说明。你猜怎么着，大多数人不会花 3 天时间在邮局前等待他们的包裹到达。除了你可能会挨饿的事实之外，这看起来不像是一种组织工作流程的非常有效的方式。类似于等待 amazon 包，web 开发主要是发送服务器/数据库/服务请求，然后等待它返回，并对数据做一些事情。因此，使用能够处理并发性的语言是很自然的。

回调是处理并发性的一种方式。节点用户熟悉这样的代码

Looks familiar to you, but many beginners will scratch their heads before understanding what’s going on here.

它基本上告诉从某个数据库获取一个用户，同时传递两个参数:

*   `userId`、*又名*你的查询
*   回调函数`(err, user) => {...}`解释当查询结果到达时做什么*。*作为一个节点约定，第一个回调参数是一个错误消息，您希望它只是`null`

它可以工作，但是在某种程度上，处理所有的错误处理变得很乏味，您可能会以一些不可理解的、深度嵌套的代码而告终。许多人把这种情况称为回调地狱。我不太确定这是不是地狱，但在我看来肯定很无聊。

## 2.输入承诺

承诺是最近的特性，它有助于链接异步任务，同时保持更好的可读性。我们的第一个例子是

哇，好多了！

但是如果我想同时使用`user`和`result`作为另一个函数的参数呢？这将把我们的代码变成

I skipped the error catching on this example. How would you do it ?

我们又回到了多重嵌套层次。好难过:(

承诺是一件伟大的事情，它们极大地提高了代码的可读性。然而，它们并没有解决我们所有的问题，嵌套/错误捕捉仍然存在，尽管它肯定比回调好。

***编辑:*** *聪明的人指出，用承诺来解决例子 2 的一种更诚实/优雅的方式可以是:*

确实比我原来的解决方案要好:)

## 3.输入 Await

![](img/9239a8f516f73e153ca3e0036bfaeba6.png)

做好准备，ES7 来了。目前`async/await`仍然是一个提议，所以下面可能会有(我希望是微小的)变化。

要使用 ES7 功能，要么[设置 babel](http://jamesknelson.com/using-es6-in-the-browser-with-babel-6-and-webpack/) ，要么使用 [typescript](https://www.typescriptlang.org/) (或者其他我没想到要提的东西)。至于今天，TS 团队选择发布一个`await/async`功能，它与 ES7 具有相同的规格。请注意，这可能有一天会改变，虽然我们不希望如此。

首先，让我们更新我们的例子:

Double rainbow !

以下是一些需要注意的事项:

*   该函数被声明为`async`，所以我们可以在函数内部使用`await`
*   `const user = await User.findById(userId)`将像在同步世界中开发一样工作。虽然它不会阻塞线程(所以它实际上是异步的)，但它允许开发人员真正表达他/她在想什么:做一些事情，命名结果，并在以后使用它们。
*   `try/catch`回好 ol’进行错误处理

# 我们可以用 await/async 做的很酷的事情的例子

**承诺**

异步函数总是返回一个承诺。您甚至可以用它来“许诺”一个常规函数:

注意将函数声明为`async`是如何将`return i +1`(一个数字)变成了`Promise<number>`

## 扔

A `throw`将返回一个错误拒绝的承诺:

*   再举个例子？

return a promise that either resolves to null, or a user, or reject with ‘User not found’

## 睡眠

实现`sleep`功能:

*__*

## Node.js 测试示例

我们来看看编写测试的三种方法怎么样？

这个测试统计用户的数量，检查是否多于 1 个，然后获取一个，并检查它是否有一个`email`字段。

BOOM, two-liner !

你觉得哪个更好看？

## 协议:并行性

`await Promise.all(...)`将等待多个并行承诺解决:

# 结论

如果你还在这里，那么我很肯定你现在对这个非常奇特的`async/await`功能有一个很好的想法。

我希望你现在相信它有多棒。让我知道你的想法！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)