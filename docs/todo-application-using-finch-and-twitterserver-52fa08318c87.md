# 使用 Finch 和 TwitterServer 的 TODO 应用程序。

> 原文：<https://medium.com/hackernoon/todo-application-using-finch-and-twitterserver-52fa08318c87>

![](img/32c15ff90c51ae18a9636ffcb6d62001.png)

上周 *ScalaTimes* 展示了 [*使用 Akka HTTP 和 Slick*](https://scalatimes.us2.list-manage.com/track/click?u=ba834c562d82d9aba5eaf90ba&id=11f7ed2c12&e=c3a14fe549) **，**创建一个 TODO 应用程序，这鼓励我在 Finch 中做同样类型的入门教程。

Finch 是一个由主要在 Twitter 上工作的人编写的项目，它是一个类型级孵化器。我最喜欢 Finch 的一点是它的简单性，因为它使用了纯函数式编程结构和高效的底层库。

就我个人而言，我在各种小型项目中使用过 Finch，也曾在非常严肃的项目/环境中使用过一次。我总是发现它非常容易和简单，即使它是一个非常有能力的库。

雀快，只看 [*雀有多快？*](http://vkostyukov.net/posts/how-fast-is-finch/) 你会发现，它是用 Scala 写的第二快的 HTTP 库。

现在让我们关注如何使用 Finch 创建一个简单的 Rest API。

我们的 *build.sbt* 文件如下所示:

我们只是为 JSON 序列化/反序列化添加了 Finch 库和喀尔刻。

现在，我们需要创建我们要服务的第一个端点。

让我们从非常简单的东西开始，然后从那里发展。首先，我们可以定义一个`hello`端点。

在我看来，`Endpoint`是 Finch 中最重要的构造。

`Endpoint`背后思想是它代表了如下一系列的转换:

我们得到一个`Request`，并将其转换成某种类型的`A`。然后，我们将一些业务逻辑应用到异步事务的输入中，以完成到期望的`Response`的另一个转换。

在我们的例子中，我们的`Endpoint`是一个 URL 为`hello`的`get`，要应用的业务逻辑只是返回`hello to you!`。

现在，我们需要运行服务器并公开要使用的`hello`。

我们只创建了一个我们可以执行的应用程序，我们创建了一个`server`来连接到`localhost:8080`，然后它作为一个欺骗服务来服务于`hello` `Endpoint`。

我们可以通过执行`sbt run`或`java -jar <the application.jar>`来运行应用程序。一旦运行，我们可以使用我们的终端发送一个请求到端点:`http localhost:8080/hello`，我们将返回:

## 我们的存储服务

在本例中，我们将使用一个可以通过多种方式实现的存储抽象，特别是我们可以创建一个与内存数据存储区对话的存储抽象。

`Item`是我们将向 API 发送和接收的对象类型，存储负责保存/检索它们。

我们的内存实现就是这么简单。

# 定义待办事项端点

## 1.开始行动

我们的第一个端点将是`todos`，它检索存储中所有可用的项目。

我们可以看到这是多么简单！它只是做一个`storage.getAll` 调用。该端点将响应我们服务器中的`GET /todos` URL。

## 2.待办事项后

现在我们可以定义一个新的端点来添加将在`POST /todo`响应的 todos

这个看起来更有趣一点。因为它是一个`POST`,我们需要从消息体中提取出`Item`。然而，有一个问题，没有`UUID`就不能创建`Item`，这就是`postTodo`函数的作用。换句话说，我们从`POST`消息体中提取出`taskName`，并用它和一个随机的`UUID`创建一个`Item`对象。

这个想法是，如果没有在请求中发送的 id，您不需要有不同的`Item`模型。

然后函数`storage.add(item)`并不真正关心 id，它只是向存储器中添加一个新的`Item`并返回最近创建的那个(它有自己的新 id)。

一旦添加了`Item`，我们就返回最近创建的那个。

我们可以从我们的终端测试它，方法是

它返回

## 3.GET -TODO

检索特定的`Item`是一项简单的任务。我们只需要 id，然后我们可以请求`storage`为我们获取它。

我们可以通过以下方式进行测试:

它返回与我们之前插入的相同的 todo。

注意，如果项目不在存储器中，`.fold`将向客户端返回`NotFound` HTTP 消息。

## 4.删除-待办事项

用同样的方法我们可以做`deleteTodo`

我们寻找具有给定 id 的`Item`。如果它存在，我们就删除它。然后我们使用`.fold`相应地返回。

## TodoService

整个`TodoService`代码如下所示。

# 把所有东西放在一起

我们需要从我们的应用程序中暴露所有这些`Endpoint`。芬奇使用无形状来创作`Endpoint` s，仔细观察`TodoService`我们可以看到这种创作发生在`TodoService.api`中。

让我们看看如何重写我们的应用程序来服务于`TodoService`。

注意`.api`是每个单个`Endpoint`的组合。

这个组合是使用 *Shapeless* 完成的，基本上就是说传入的请求必须匹配其中一个端点。

# 结论

Finch 是一个 HTTP 库，它提供了一种简单的方法来定义端点并将它们组合成一个更复杂的 API。Finch 允许我们为网络编写纯粹的功能性代码，能够以高速率响应服务器请求。