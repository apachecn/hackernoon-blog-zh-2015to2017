# Node8 的 util.promisify 太牛逼了！

> 原文：<https://medium.com/hackernoon/node8s-util-promisify-is-so-freakin-awesome-1d90c184bf44>

![](img/c0e6f41540eba0d296972c0aee688f0b.png)

在本指南中，你将学习如何使用 Node 的`util.promisify`来保证你自己的功能。这个实用函数包含在 NodeJS 版本 8 中。如果你打算遵循这个指南，你需要安装最新版本。作为额外的收获，我还会告诉你如何使用`promisify`和`await`和`async`。

# TLDR；

*   `util.promisify`将常规函数转换成异步函数，即返回承诺的函数
*   传递给`util.promisify`的函数必须遵循 NodeJS 回调风格。该函数必须传递一个回调函数作为最后一个参数，并且该回调函数必须按以下顺序接受以下参数:`(err, value) => { /* … */ }`
*   promise 函数可以与`await`和`async`一起使用，以帮助避免混乱的 promise 链，并引入一种更干净、更合理的方式来进行异步编程。

# util.promisify 是做什么的？

官方节点文档说:

> 取一个遵循 common Node.js 回调风格的函数，即取一个`(err, value) => ...`回调作为最后一个参数，返回一个返回 promises 的版本。

还有…

> `promisify(original)`假设`original`是一个在所有情况下都以回调作为最终参数的函数，如果不这样，返回的函数将导致未定义的行为。

所以基本上它是一个效用函数，接受一个常规函数，并将其转换为一个返回承诺的函数。

传递给`util.promisify`的函数必须遵循几个约定:

1.  传递给`promisify`的函数的最后一个参数必须是回调。
2.  回调必须遵循节点的回调样式。

下面是一个有效函数的示例:

**注意:**对于不熟悉语法的人，我使用的是 [ES6](https://github.com/lukehoban/es6features) 。我也使用 Airbnb 风格指南。

为了演示`util.promisify`如何工作，让我们从创建一个在很长一段时间内执行任务的常规函数开始。然后，我们将把这个功能转换成一个承诺。

# 创建常规函数

下面我有一个函数叫`wait`。它的目的是让你等待一段未知的时间:

出于演示的目的，我编写了等待一段未知时间的函数。它通过使用`setInterval`每隔`x`秒调用一个匿名函数，并将该数字与一组值进行比较，以决定是继续等待还是停止:

*   如果数字大于`0.95`，函数将停止运行，并调用回调函数。
*   如果数字小于`0.01`，一个错误将被传递给回调函数。
*   最后，如果随机数在范围内，一条消息将被打印到 STDOUT。

# NodeJS 回调样式

`util.promisify`期望一个符合 NodeJS 回调风格的函数。要了解更多关于 Node 回调风格的信息，请阅读本文:[The Node . js Way——理解错误优先回调](http://fredkschott.com/post/2014/03/understanding-error-first-callbacks-in-node-js/)

在`wait`函数中，你会注意到我用了两种不同的方式来使用回调。当随机数大于`0.95`时，这被认为是成功的，等待结束。因此我们可以这样调用回调函数:

```
callback(null, ‘Congratulations, you have finished waiting.’);
```

这里我将`null`传递给了第一个参数。没有发生错误。没什么可报告的。使用第二个参数，我传递一些在回调或承诺链中可用的数据。

当`wait`功能失败时(在这种情况下，当随机数低于`0.01`时)，错误消息被传递给第一个参数。第二个参数设置为`null`。

```
callback(‘Could not wait any longer!’, null);
```

这遵循节点的回调风格。

# 使用

这个函数是这样调用的:

该函数需要两个参数。第一个是在`setInterval`调用回调函数之前等待的毫秒数。第二个参数是回调。

注意我是如何检查错误的:`if (err) throw new Error(err)` …如果`err`是`null`，回调继续。否则会引发异常。

# 试运转

运行上述代码会在成功时产生以下输出:

…以及错误时的以下输出:

我们确认函数如预期的那样运行。接下来我们将把它变成一个承诺。

# 承诺正则函数

神奇的事情就发生在这里。

现在我们有了一个普通的函数，我可以演示如何把它变成一个返回承诺的函数。这出奇的简单…

就是这样。在两行代码中，我将函数转换为承诺。第 7 到 9 行显示了如何使用新的承诺。你会同意它更干净，更容易阅读。

# 引入异步和等待

等等，我们还没说完。我有更多有趣的东西要和你分享。Node 8 还提供了对`async`和`await`的支持。太酷了。

除了使用承诺链(这很快就会变得非常混乱)，您还可以用正常的过程化风格编写代码，如下所示:

不再有承诺链:)

关于 async/await 的更多信息，我强烈推荐以下文章:[https://blog . rising stack . com/mastering-async-await-in-nodejs/](https://blog.risingstack.com/mastering-async-await-in-nodejs/)

# 😊

附言:如果你在推特上，给我一个关注。我经常写关于软件开发和安全的文章:【https://twitter.com/JamesJefferyUK 