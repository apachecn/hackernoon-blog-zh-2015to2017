# 带 Go 的简单 HTTP 中间件

> 原文：<https://medium.com/hackernoon/simple-http-middleware-with-go-79a4ad62889b>

## 只使用标准库

围绕传入服务器的 [HTTP](https://hackernoon.com/tagged/http) 请求进行各种操作是一种常见的需求。最常见的例子是日志记录和跟踪。尽管有各种框架提供这种现成的功能，但我想说明只使用标准库来创建这种逻辑是多么简单。构建您自己的实现为您提供了完全的灵活性和对行为的控制，减少了对外部依赖性的需求。

理想情况下，在我的简单示例的上下文中，我希望 API 看起来像这样:

在上面的例子中，我使用`chainMiddleware`为我的所有处理程序创建了一个包装器，它用我定义并作为参数传递给这个函数的日志和跟踪中间件[来增强`home`和`about`。](https://hackernoon.com/tagged/middleware)

为了做到这一点，我们需要定义一个中间件类型。它可以定义为:

因此，`middleware`将被定义为返回新处理程序的函数，该处理程序将在调用作为参数提供的下一个处理程序之前(或之后)执行一些操作。为了简洁起见，我们将使用`[http.HandlerFunc](https://golang.org/pkg/net/http#HandlerFunc)`而不是经典的`[http.Handler](https://golang.org/pkg/net/http#Handler)`来举例说明这些例子。行为是相同的。

使用这个模式，我们将虚拟的`withLogging`和`withTracing`中间件定义如下:

我们的中间件纯粹是说明性的，除了记录已经采取的行动之外什么也不做。

理论上，我们已经可以使用这种实现，而不需要`chainMiddleware`，只需将每条路由定义如下:

这很好，也很有效，但是如果我们引入更多的中间件，很快就会变得乏味和冗长。因此，我们的 chain 函数为将多个中间件链接成一个中间件提供了很好的语法糖。

让我们看一下实现:

通过利用 Go 的函数式编程特性，我们可以使用上面的实现来链接一系列中间件，这些中间件按照提供的顺序相互调用，为我们的处理程序返回一个新的包装器。我们以相反的顺序获取列表中的每个中间件函数，并将它的返回值作为参数传递给它前面的函数，从`final`处理程序开始。这样，链就按照传入的顺序发生了。

现在，如果我们运行程序并在浏览器中访问`http://localhost:8080`,在调用最终处理程序之前，我们应该会在控制台中看到类似下面的输出:

`main.go:34: Logged connection from [::1]:54962`
`main.go:41: Tracing request for /`

就这么简单！你可以在这里找到完整的运行示例。