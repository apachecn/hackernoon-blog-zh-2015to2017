# Redux 中间件日志记录基础

> 原文：<https://medium.com/hackernoon/redux-middleware-logging-basics-a80c1b139782>

## 失去控制的木头

![](img/934c3750619913d2bc2dfaecd689ae51.png)

我以前写过关于 REDUX 和它的一些特性，如果你对更一般的介绍感兴趣，请查看下面的帖子。

[](/@btg5679/an-introduction-to-redux-ea0d91de035e) [## Redux 简介

### 现代数据流模式

medium.com](/@btg5679/an-introduction-to-redux-ea0d91de035e) 

实现这种 [FLUX](https://facebook.github.io/flux/) 风格的架构有很多好处(如果做得正确的话)，但是它对日志记录、崩溃和错误报告的影响经常被忽略。

如果您曾经工作过，或者更好地尝试过对具有复杂数据模型、事件和工作流的复杂 web 应用程序进行故障排除，那么您应该熟悉这个不可再现的 bug。如果能够回过头来查看应用程序在崩溃时的确切状态，甚至是导致崩溃的操作，岂不是很棒？让我们看看如何实现这一点。

**传统的 console.log()** 可能是这样的:

在上面您会看到一行 console.log()代码，这里没有任何动态变化。您必须将这些东西分散到整个应用程序中，以便在变化的不同阶段抓住您的商店。然而，当你在处理较大的应用程序时，拥有一个 ***单一定义点是有好处的。*** 让我们看看可能会是什么样子:

您可以从上面看到，我们组合了 3 个中间件:

*   我们的新**定制记录器**
*   Redux Thunk 用于处理异步调用
*   RouterMiddleware，用于捕获和重定向操作到您的历史记录

你可以组合中间件，它们不需要知道它们之前或之后的软件。我们包装了商店配送方法，以实现我们想要的结果/功能。

动作和缩减器之间的时刻是一个关键的交换，使用中间件清晰地记录状态可以提高应用程序的整体质量。

**进一步阅读和资源:**

*   [Redux 中间件文档](http://redux.js.org/docs/advanced/Middleware.html)
*   [重复生产测井](https://hackernoon.com/redux-logging-in-production-d489d614de78)

在推特上告诉我你的想法。坚持下去。

# 如果你喜欢这篇文章，请推荐一下，帮助别人找到！

![](img/6ca8d8237c62634322efbca85a00bee2.png)