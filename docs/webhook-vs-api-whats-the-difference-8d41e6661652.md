# Webhook vs API:有什么区别？

> 原文：<https://medium.com/hackernoon/webhook-vs-api-whats-the-difference-8d41e6661652>

![](img/6a48586845d5746998024f587c8e0015.png)

通信是数字时代的关键驱动力。作为人类，我们希望[技术](https://hackernoon.com/tagged/technology)帮助我们更快、更容易地与更多的人交流。但是为了实现这一点，我们必须首先找到一种让技术互相交流的方法。

这就是 API 和 webhooks 发挥作用的地方。

webhooks 和 API 都有助于在两个应用程序之间同步和转发数据。然而，两者都有不同的方法，因此服务的目的略有不同。

为了澄清这两者之间的任何混淆，让我们来看看 webhook 和 API 的区别，以及它们各自最适合什么样的场景。

# Webhook 与 API:简单来说的区别

简而言之，API 在你要求的时候做事情，而 [Webhook](https://hackernoon.com/tagged/webhook) 在满足特定标准或场景发生时自己做事情。让我们再深入一点。

可以从服务器使用 API 与 example.com 通信。通过该通信，API 可以列出、创建、编辑或删除项目。尽管如此，这个 API 还是需要一些说明。

另一方面，Webhooks 是从 example.com 到服务器的自动调用。这些电话是在 example.com 发生特定事件时触发的。例如，如果一个新用户注册了 example.com，自动呼叫可以被配置为请求服务器发送一封欢迎电子邮件。

# 什么是 Webhook？

有时 webhooks 被称为反向 API，但这并不完全正确。它们不会向后运行，而是不需要在您的一端发起请求，只要有新数据可用，就会发送数据。

要设置一个 webhook，你所要做的就是向提供你所请求数据的服务的公司注册一个 URL。该 URL 将接受数据，并可以激活一个工作流，将数据转化为有用的东西。在大多数情况下，您甚至可以指定提供商将向您交付数据的情况。

Webhooks 和 API 在发出请求的方式上有所不同。例如，无论是否有数据更新响应，API 都会调用数据。而 webhooks 只有在您所连接的外部系统有数据更新时才通过 HTTP POSTs 接收调用。

# 何时使用 WebHook

webhook 通常用于执行较小的请求和任务，但是有些情况下 web hook 比整个 API 更合适。

一种常见的情况是，您的应用或平台需要实时更新，但您不想浪费资源。在这种情况下，网钩将是有益的。

在 API 上使用 webhook 的另一种情况是当 API 很差，或者根本没有 API 的时候。您可以创建一个变通解决方案，为您提供应用程序运行所需的数据。

然而，有一个关于 webhooks 的警告。因为它们不用于定期请求数据，并且只在有新数据可用时才这样做，所以如果系统由于某种原因脱机，您可能永远也不会了解到新的更新。您对总数据流的控制也将减少，因为您必须接受给定更新可用的总数据量。

# 现实生活中的 Webhook 示例

许多应用程序和工具确实依赖于 webhooks，但主要用于较小的数据请求，而不是使用它们来形成服务的主干。尽管如此，还是有很多 webhooks 被有效使用的例子。

1.  ButterCMS webhook 会在任何人发布新的博客文章或更新其 CMS 内容的时候触发。
2.  Zapier 本质上是一个巨大的网络钩子。你将某些应用程序连接在一起，每当一个应用程序发生事件，就会触发另一个应用程序的动作。
3.  [Stripe 有一个 webhook](https://stripe.com/docs/webhooks) ，每当订阅支付失败时，它会自动向客户发送电子邮件。

# 什么是 API？

API 代表应用程序编程接口。API 是应用程序和平台通过通用通信方法与其他应用程序和平台连接的一种方式。要让 API 工作，需要有一个数据请求，然后是对请求的响应。数据通常以 JSON 这样的格式交付。

API 往往是许多现有软件和工具所依赖的框架。例如，一个创建 Twitter 趋势报告的应用程序可以依靠一个 API 不断地从 Twitter 获取最新的数据。大多数大型应用程序都集成了多个 API 来扩展它们的服务，如下所示。

# 何时使用 API

当您知道数据会不断变化时，API 会工作得非常好。如果您需要的数据相对停滞不前，那么使用 API 就毫无意义。例如，如果你是一个电子商务商店，定期更新其运输和跟踪数据，那么你会不断提出要求。

每次你轮询 API，你都会得到新的数据。如果您的数据没有不断更新，那么就不能保证在另一端会有准备好的数据。当这种情况发生时，你只是在浪费资源。然而，如果你开始使用 API，你可以设置一个调用限制，这将限制你在一段时间内调用的次数。一些应用程序甚至从一开始就限制你打电话的次数，以减少他们端的资源使用。

# 现实生活中的 API 示例

就像我们上面提到的，API 无处不在。根据 ProgrammableWeb 的最新结果,目前已有超过 17，000 个 API。下面你会发现一些使用 API 的工具:

1.  作为一个 [API 优先的 CMS](https://buttercms.com) ，ButterCMS 拥有自己的 REST API，具有可预测的、面向资源的 URL，并使用 HTTP 响应代码来指示 API 错误——其功能由这个 [React 通用博客构建](https://www.programmableweb.com/news/build-react-universal-blog-nextjs-and-buttercms/how-to/2017/05/02)展示。
2.  优步还依靠谷歌地图 API、Twilio API、Braintree API 和 SendGrid API 来帮助他们的应用程序。
3.  Slack 有一个 API ，可以让你将他们的消息功能集成到你的第三方应用中。

# Webhooks 和 API 运行在不同的圈子里

这不是哪个更好的问题，因为不存在一种方法胜过另一种方法的普遍情况。这更多的是关于您的应用程序的目的和您所请求的数据类型的问题。

举个例子，你可以把 API 想象成你发送给朋友的文本消息，以获取他们举办的活动的更多信息。你问一个问题，他们会给你回复。

有了 webhook，你可以告诉你的朋友，只要他们组织另一个活动，就给你发短信，让你知道。您提交最初的请求，当有新的信息出现时，他们会不断地向您发送更新。

最后，大多数应用程序最终会同时使用 API 和 webhooks 来创建一个可以在正确的时间传递正确类型的数据的系统。

*这篇* [*文章*](https://buttercms.com/blog/webhook-vs-api-whats-the-difference) *原载于* [*ButterCMS 博客*](https://buttercms.com/blog/) *。ButterCMS 是一个* [*托管的 API-first CMS*](https://buttercms.com/api-first-cms/) *和* [*博客引擎*](https://buttercms.com/blog-engine/) *让你构建基于 CMS 的应用。*