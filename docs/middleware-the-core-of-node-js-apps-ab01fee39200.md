# 中间件:node.js 后端应用的核心

> 原文：<https://medium.com/hackernoon/middleware-the-core-of-node-js-apps-ab01fee39200>

我做 node/express 应用已经有一段时间了。我交谈过的许多其他人也喜欢它的简单性，他们可能使用不同的框架，如哈比神，但在核心上，他们非常相似。核心概念之一是中间件。我很少看到初学者充分发挥它们的潜力，所以我想分享我的观点，为什么我认为它们是任何后端应用程序最重要的核心。

# 概念

[中间件](https://hackernoon.com/tagged/middleware)在您的路由处理器执行之前运行。你可能已经使用了一些中间件，例如 [bodyParser](https://www.npmjs.com/package/body-parser) 。可以用各种方式插入，但概念是一样的。它用额外的信息增加了您的请求对象，并且它也已经可以响应客户端，但是您的路由处理程序可以(但不是必须)在之后被调用。

## 中间件定义

一个功能很少的基本中间件如下所示:

```
function emptyMiddleware (req, res, next) {
  req.somedata = 42
  next()
}
```

中间件基本上是一个接收请求和响应对象的功能，就像路由处理器一样。作为第三个参数，你有另一个函数，一旦你的中间件代码完成，你应该调用它。这意味着您可以等待异步数据库或网络操作完成，然后再继续下一步。这可能如下所示:

```
function myMiddleware (req, res, next) {
  getSomeData()
  .then(function (data) {
    req.somedata = data
    next()
  })
}
```

如果有一个错误，并且您不想执行任何进一步的代码，就不要调用那个函数。请记住在这种情况下发送响应，否则客户端将一直等待响应，直到超时:

```
function myMiddleware (req, res, next) {
  getSomeData()
  .then(function (data) {
    req.somedata = data
    next()
  })
  .catch(function (error) {
    res.status(500).send(error.message)
  })
}
```

## 中间件的使用

然后，您可以以各种方式使用中间件，最简单的方式是全局使用您的所有路线:

```
var app = express()
app.use(myMiddleware)

//your normal route Handlers
app.get('/someroute', handler)
...
```

另一种选择是仅针对特定路径包含它:

```
app.use('/withmiddleware', myMiddleware)//route handlers without middleware
app.get('/someroute', handler)
//routes with middleware
app.get('/withmiddleware/someroute', handler)
```

第三个选项通常被忽略，它直接位于路由处理程序中:

```
//middlewares passed as array in second argument to a route definition
app.get('/someroute', [myMiddleware], handler)
```

无论如何，路由处理程序现在接收到增加的请求对象:

```
var handler = function (req, res) {
  console.log(req.somedata) //prints out what was inserted by the middleware
}
```

中间件可以访问在执行时解析的所有请求参数。因此，全局中间件没有任何“req.params”选项，而具有特定路径的中间件或插入到路由定义中的中间件具有在该路径或路由中定义的所有参数。这在很多情况下非常方便，请看下一部分的例子。

## 中间件链接

您可以在上面最后一个示例中显示的中间件数组中或者通过使用多个 app.use 调用来链接中间件:

```
app.use(middlewareA)
app.use(middlewareB)
app.get('/', [middlewareC, middlewareD], handler)
```

中间件将按照它们的使用调用顺序或它们在数组中的顺序执行。此外，它们将从以前的中间件接收增强的请求对象，因此它们可以依赖彼此的功能，就像您的自定义中间件被插入到 bodyParser 之后，从而能够使用被解析的主体。

# 中间件使用示例

在这里，我想快速展示一些我认为中间件可以用来真正改善你的代码的方法。所有这些背后的基本思想是减少路由处理程序中的重复代码。

## 从数据库中检索重要对象

一个常见的体系结构是有许多以某种方式操作或输出同一数据库对象的数据的路由，因此可以使用中间件来检索该对象，如果找不到该对象或不允许访问，就会出错:

```
function dbMiddleware (req, res, next) {
  getFromDb(req.params.id) //see app.get below
  .then(function(data) {
    req.dbData = data
    next()
  })
  .catch(function (error) {
    res.status(500).end() //replace with proper error handling
  })
}app.get('/data/:id', [dbMiddleware], handler)
```

在前面的示例中，只有当数据成功添加到请求对象中时，才会调用处理程序，因此这里不需要进一步的错误处理或数据库代码。还要注意我是如何使用路由的 id 值的。

## 证明

最常见的例子是身份验证，因此您可以在所有路由中引用当前用户对象:

```
function userMiddleware (req, res, next) {
  getUserViaJWT(req.headers.authentication)
  .then(function(user) {
    req.user = user
    next()
  })
  .catch(function (error) {
    res.status(401).end() //replace with proper error handling
  })
}app.use(userMiddleware)app.get('/someroute', handler)
```

该中间件只是解析一个身份验证头，并使用它来确定哪个用户当前登录，并将它添加到请求对象中。同样，如果有错误，它只会结束响应，而不会调用后续的中间件或路由处理程序。

## 记录

您还可以使用中间件进行日志记录，如下所示，以记录用户的最新活动，这可以与以前的中间件结合使用，以获取用户对象:

```
function logMiddleware (req, res, next) {
  logLastActiveAt(req.user.id, new Date())
  next()
}app.use(userMiddleware)
app.use(logMiddleware)app.get('/someroute', handler)
```

# 结论

我希望这能让初学者更好地了解中间件，以及为什么它们真的能帮助清理你的代码。检查你的代码中重复出现的任务，并把它们放入它们自己的中间件中。有了包含中间件的各种方法，您可以以任何您想要的方式使用它们，并且不受特定 URL 结构的限制，可以在不同的地方使用特定的中间件。我绝不是这方面的专家，但是我发现很多项目使用了很多外部中间件，比如前面提到的 bodyParser，或者一些 JWT 解析中间件，或者一些文件上传包等等，但是他们经常不使用任何定制的中间件，而是一遍又一遍地重复相同的东西和路由处理器。希望这篇文章有助于降低这种情况发生在你的应用程序中的风险。如果你有任何问题，请在评论中提问。我也欢迎任何建议、改进、纠正和批评，如果有什么我会更新帖子。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！