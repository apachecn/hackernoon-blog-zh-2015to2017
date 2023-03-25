# express-fp:类型安全请求处理程序的快速包装器

> 原文：<https://medium.com/hackernoon/express-fp-an-express-wrapper-for-type-safe-request-handlers-f8c411cc4a7b>

如果您是一名 [TypeScript](https://hackernoon.com/tagged/typescript) 用户，希望在您的节点 web 服务器中充分利用类型系统，我一直在做一些您可能感兴趣的事情: [express-fp](https://github.com/OliverJAsh/express-fp) ，这是一个 [Express](https://expressjs.com/) web 服务器库的包装器，在我们的请求处理器函数中提供更多的类型安全性。通过确保请求和响应的类型安全，我们可以避免许多常见的错误。

虽然我在个人项目中积极地使用它，但这在很大程度上仍然是 WIP。虽然没有真正的文档，但是如果您想尝试一下，静态类型和示例应该足以让您了解要点并开始学习。

当前的 API 非常类似于 [Play 框架](https://www.playframework.com/)(特别是 Scala Play)，因为 Play 是类型安全 web 服务器的一个很好的例子，也是我目前所熟悉的唯一一个类型良好的 web 服务器。

说完这些，让我们来看看 express-fp 试图改进的一些事情。

# 不要改变响应，而是将响应作为数据返回

在 Express 中，通过改变响应对象来构造响应，并在完成后调用`res.send`。如果您忘记调用`res.send`，您的请求将被挂起，直到超时。

在 express-fp 中，响应必须作为对象返回(使用 [express-result-types](https://github.com/OliverJAsh/express-result-types) )。如果你忘记返回，或者返回了错误的类型，类型系统就会抱怨。

# 代替中间件和变体，构建纯粹的功能

在 Express 中，请求通过一系列用户定义的“中间件”来处理，这些中间件通常会改变请求和响应对象。错误的一个常见来源是中间件出现的顺序错误。例如，B 假设首先调用 A，并且响应已经设置了响应头。然而，我们可以很容易地打破这种排序，我们将不会从类型系统得到关于这一点的警告。

中间件的常见用例包括:

*   解析请求体和查询
*   异常处理

在 express-fp 中，中间件被功能组合所取代。请求不会通过多个处理程序，也没有`next`功能。

通过组合纯函数，类型系统完全知道所有的输入和输出，保证正确的顺序。

根据需要，解析可以在请求处理程序中处理。

多亏了纯函数，我们不需要异常处理程序。在需要错误的地方，我们可以使用`Either`将它们作为值返回。

# 类型安全传入值的验证(请求正文，查询)

在 Express 中，请求体和查询可以是任何东西。我们可以很容易地断言类型，例如`req.body as MyBody`，但这无助于在运行时验证类型——`req.body`在运行时可以是任何东西。

express-fp 使用奇妙的 [io-ts](https://github.com/gcanti/io-ts) 来验证从外部进入应用程序的值。如果你以前使用过 [Joi](https://github.com/hapijs/joi) ，那么 [io-ts](https://github.com/gcanti/io-ts) 是类似的，除了它可以使用 TypeScript。一旦一个值被验证， [io-ts](https://github.com/gcanti/io-ts) 将自动用它的静态类型注释这个值。

在 express-fp 中，我们只能通过定义用于验证的“运行时类型”来访问请求体。作为回报，我们得到一个`Either`类型— *包含验证错误的*或被验证的主体。如果主体有效，[类型系统将已经知道主体的形状](https://github.com/gcanti/io-ts#typescript-integration)，而不必在单独的类型接口中重复它。

在 express-fp 中，请求查询被公开为`Map<string, string | string[]>`，它与定义良好的 URL 查询参数相匹配。由您来提取和验证查询参数。

# 结论

要查看所有这些功能的组合，请查看[示例](https://github.com/OliverJAsh/express-fp/blob/e6c13ddc4a2c337f319e1409f941b4a53e228dc9/src/example.ts#L23)。你可以在 [OliverJAsh/express-fp](https://github.com/OliverJAsh/express-fp) 的 [GitHub](https://hackernoon.com/tagged/github) 上找到资源库。

我写这篇博客的主要动机是想得到一些反馈，所以如果你动心了，请试一试，并让我知道你的想法。如果你有任何想法，请在评论中联系我，[在 Twitter 上](https://twitter.com/OliverJAsh)，或者通过 [GitHub 问题](https://github.com/OliverJAsh/express-fp/issues)。沿着这条路走下去，我希望写一篇更深入的文章，用内联的例子。