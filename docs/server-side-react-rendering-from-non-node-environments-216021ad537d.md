# 非节点环境中的服务器端 React 渲染

> 原文：<https://medium.com/hackernoon/server-side-react-rendering-from-non-node-environments-216021ad537d>

如果你的大部分前端是使用 [React](https://hackernoon.com/tagged/react) 组件构建的，那么使用相同的代码从后端呈现你的视图是有意义的。这个**预渲染**，也就是说，在组件被渲染之前，不要等待你的 [Javascript](https://hackernoon.com/tagged/javascript) 加载到网络浏览器中，这对于 SEO 来说尤其重要。幸运的是，React 提供了将组件呈现为 HTML 字符串的功能，然后可以从 web 服务器返回该字符串。然而，React 是一个 Javascript 库，因此需要一个 Javascript 运行时环境。如果您使用 NodeJS 作为后端，那就可以了。但是如果使用 Ruby on Rails、Go、Django 或者其他任何非 Javascript 环境，就无法直接使用 React 的服务器端渲染功能。

输入 [preact-rpc](https://github.com/musawirali/preact-rpc) 。

Preact-rpc 集三种功能于一身:

1.  一个 RPC 服务器，由连接到它的客户端完成 React 组件呈现请求。
2.  一个在 Javascript 包中注册 React 组件的库，以便 RPC 服务器可以找到它们。
3.  用于连接 RPC 服务器并向其发送呈现请求的特定于语言的库的集合。

> 基本思路是这样的。运行 NodeJS 套接字服务器，它可以代表客户端呈现 React 组件并返回 HTML 字符串。这种方法非常有效，因为服务器在开始时加载包含组件的 Javascript 包，并将其保存在内存中。

整个系统非常简单，但是让我们通过一个例子来使事情变得非常清楚。

假设我们的后端是一个 Go 应用程序，我们希望从那里呈现 React 组件。假设我们有这样一个简单的组件:

这是一个简单的组件，它使用一个道具 ***来调用*** 并打印出一条 Hello 消息。

现在让我们安装模块: **npm 安装 preact-rpc**

接下来，我们需要**注册**我们的组件，以便我们的 RPC 服务器知道如何在给定某种 ID/名称的情况下识别特定的 React 组件。为此，我们将 Javascript 修改如下:

我们已经用名称“hello”注册了组件 Hello。当我们以后想要发送渲染请求时，我们将使用这个名称。

尽管我在上面的例子中只使用了一个组件，但是您可以根据需要注册任意多个组件。实际上，您需要创建一个包含所有必需组件的 Webpack 包。

您的 Javascript 现在已经准备好了。我们现在可以用这个 Javascript 文件启动 RPC 服务器:

这将启动一个带有 UNIX 套接字的服务器。您可以通过为端口命令行参数提供一个数字来使用 TCP 套接字。

服务器启动后，它准备好满足组件呈现请求。协议非常简单。该请求采用 JSON 的形式:

服务器期望请求以特殊的字节序列结束。默认情况下，这定义为: ***\r\n.***

我们现在可以用我们选择的语言编写一个客户端来连接到服务器。如果愿意，您可以自己编写这段代码，或者可以使用 preact-rpc 中包含的一个助手库。对于 Go，包含了 github.com/musawirali/preact-rpc/goclient**套餐**。下面是我们的 Go 后端，它使用这个包连接到前面启动的 RPC 服务器:

运行应用程序，查看我们简单的 Hello 组件呈现。

在当前状态下，preact-rpc 包是可用的，但仍然非常小。许多事情正在酝酿中，以充实该系统:

*   加载前端 Javascript 时，React 组件生命周期从后端到前端的无缝连续性。
*   支持 Redux 商店。
*   为更多的语言/环境构建助手库。

希望这个项目对社区有帮助。欢迎投稿！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！