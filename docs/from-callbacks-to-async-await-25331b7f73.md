# 从回调到异步/等待

> 原文：<https://medium.com/hackernoon/from-callbacks-to-async-await-25331b7f73>

![](img/bbbab6fc99628470add15052bb51d73c.png)

我用 4 种不同的方式构建了同一个程序。我从回调开始，到承诺，使用生成器，最后用 async/await 结束。

该计划:

1.  向 Github 的用户端点发出请求
2.  撤回我的 Github 档案
3.  记录响应

这是我想到的。

# 复试

我努力使用回调发出一个 HTTP 请求。我大多使用 Promises 来编写异步的 JavaScript。在我的应用程序中，我通常使用 [axios](https://github.com/axios/axios) 或 [fetch](https://developer.mozilla.org/en-US/docs/Web/API/Fetch_API/Using_Fetch) ，它们都是基于承诺的。

我最终不得不求助于 [XMLHTTPRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest) 来获得这个版本的应用程序，这是我以前从未使用过的！

Using Callbacks

我将带您了解这段代码的作用:

1.  我定义了一个叫做`makeHTTPRequest`的函数。它被设计成实际上向 Github 发出请求。
2.  我将三个方法传递给`makeHTTPRequest`、`url`、`methodType`和`callback`。`url`是我要打的终点。`methodType`是我要用的 HTTP 方法。而`callback`是我实际得到 Github 返回的响应时想要调用的函数。
3.  我定义了一个名为`getLogin`的函数，并把它`response`作为参数传递。该函数获取我从 Github 收到的响应，并将其解析为 JSON。然后，它记录解析后的响应。
4.  我将`getLogin`作为`callback`传入`makeHTTPRequest`。这意味着`getLogin`将接受来自 Github 的响应。

# 承诺

在通过复试实现我的目标后，我尝试承诺。这感觉很简单，因为我以前做过很多次。

Using Promises

1.  我定义了一个名为`makeHTTPRequest`的函数，并给它传递了一个用户名。
2.  我用`fetch`向 Github 发出请求。
3.  我使用`.then()`等待对 Github 的请求完成，然后将响应转换成 JSON。
4.  我记录响应

# 发电机

这是我第一次涉足发电机。在面对这个挑战时，语法和概念对我来说完全陌生。

Using Generators

1.  我用*语法定义了`getUser`并说它是一个生成器。我将`username`作为参数传入。

2.我创建了一个名为`response`的变量，并将其设置为使用`fetch`发出 HTTP 请求后从 Github 收到的响应。第 3 行的重要部分是我使用了关键字`yield`。`yield`告诉我的程序我确实想将`response`设置为我从 Github 得到的响应，但只是在请求完成之后。

3.当我设置`parsedResponse`等于`response.json()`时，我再次遵循相同的模式。在设置变量之前，我必须等待承诺解决。如果我不使用`yield`，当我尝试登录`parsedResponse`时，我得到:`Promise {<pending>}`返回。

# 异步/等待

最后，我使用 ES7 async/await 再次编写了应用程序。因为我是在用生成器构建了相同的应用程序之后才这么做的，所以 async/await 是如何构建在生成器之上的就变得非常明显了。

1.  我定义了一个名为`getUser`的`async`函数，它将`username`作为参数。
2.  我创建了一个名为`response`的变量，并将其设置为向`/users/:id`端点发出请求后从 Github 收到的响应。关键是我使用关键字`await`告诉我的程序在设置`response`等于我得到的响应之前等待请求自行解决。
3.  我在第 4 行再次使用相同的模式。
4.  然后，我记录解析后的响应。

# 外卖食品

出于几个原因，这是一项有价值的努力。首先，我没怎么用过回调，所以我真的不知道承诺有多大。我也一直认为 fetch 和 axios 是理所当然的，因为它们使得 HTTP 请求变得更加简单。

我也从未使用过生成器或 async/await。尽管我发现 async/await 比 generators 更容易使用，但了解 async/await 是如何构建在 generators 之上的还是很有帮助的。接触一些 ES7 语法也很有趣。