# 创建具有持久性的 todolist 后端

> 原文：<https://medium.com/hackernoon/creating-a-todolist-backend-with-persistence-a1e8d7d39f62>

这个 [todolist](https://hackernoon.com/tagged/todolist) 项目有助于展示为小应用程序构建一个[后端](https://hackernoon.com/tagged/backend)。本文将涵盖以下几个方面

*   拥有基于 SQL 的持久化数据库(sqlite)
*   定义待办事项和项目实体
*   部署在云上(Heroku)
*   使用 curl 测试新的 API

一段时间以来，我一直在用 Daptin 做小应用。它允许你快速迭代并为一个应用程序创建一个背景，而不需要太多的工作。这似乎也是一系列教程的完美开端，在这些教程中，我们将构建一个“待办事项”应用程序。

# 设置

你要做的第一件事是在 [heroku](http://heroku.com) 上注册(免费实例足以测试)，因为**我们将在 heroku** 上部署我们的后端。然后按照这个链接**在 heroku** 上部署一个 daptin 实例。

 [## 赫罗库

### 部署 Daptin

dashboard.heroku.com](https://dashboard.heroku.com/new?button-url=https%3A%2F%2Fgithub.com%2Fdaptin%2Fdaptin&template=https%3A%2F%2Fgithub.com%2Fdaptin%2Fdaptin) ![](img/72b35199a51e2f2208a6e4ec943e5d2d.png)

Choose a name for this app

为此实例选择一个应用程序名称(我选择了 todolist-tutorial)。单击“部署应用程序”后，将需要几分钟时间来运行实例。

![](img/0ec487fef37983e51f439a58afda4a0d.png)

Deploying app can take 3–5 minutes

部署完成后，您将获得实例的链接。单击“查看”转到新部署的实例。

![](img/70818071f7e1f0e097c0ecad07ace708.png)

“View” will take us to the newly deployed instance

![](img/465555269967fa5e44bf05d9e4ce7f08.png)

Create an account using Sign Up, Then Sign In

一旦实例准备就绪，我们将在 daptin 上创建一个用户帐户。使用“注册”创建帐户，然后登录。

# 定义模式

我们需要**定义我们的 todo 实体**。让我们借此机会创建一个稍微复杂一点的 todo-list 实现。我们将有“项目”将有一个名称，“待办事项”将属于这些项目之一。

我们将**创建一个 YAML 文件来定义模式**(您也可以使用 JSON)并将它上传到 daptin 以获得我们的 API

*   Todo 将有一个“标题”、“描述”和“日程日期”。
*   项目将有一个“名称”。
*   我们还需要定义 todo 和项目实体之间的关系。总的来说，它将如下所示:

![](img/9532407fd1f1796874758f8ff0aaadb4.png)![](img/bc5bbde803cd09505509900a21fa9e00.png)![](img/f775b148859fac57b6c791660c475b35.png)

你可以在这里找到我使用过的完整文件。保存此文件，以便我们可以将其上传到 daptin。

![](img/13741a4c0be2f71ed675891f92e1d021.png)![](img/f8cbea949181294b6f2d2ddc1fd5533f.png)

Select Upload Schema and choose the previously created file

上传还需要 15-20 秒。完成后，我们的 API 就准备好了。

# 测试 API

让我们试试吧。我们将使用 curl 从终端调用 API:

```
curl “[https://todolist-tutorial.herokuapp.com/api/todo](https://todolist-tutorial.herokuapp.com/api/todo)"
```

反应

```
{
 “links”: {
 “current_page”: 1,
 “from”: 0,
 “last_page”: 0,
 “last_page_url”: “//api/todo?page[number]=0”,
 “next_page_url”: “//api/todo?page[number]=2”,
 “per_page”: 10,
 “to”: 10,
 “total”: 0
 },
 “data”: []
}
```

因为我们还没有创建任何 todo，所以我们在“data”键中得到一个空数组。让我们创建一个新的待办事项

```
curl “[https://todolist-tutorial.herokuapp.com/api/todo](https://todolist-tutorial.herokuapp.com/api/todo)" — data ‘{“data”: { “type”: “todo”, “attributes”: {“title”: “get this done”, “schedule”: “2017–10–19”}}}’
```

回应:

```
{
 “errors”: [
 {
 “status”: “500”,
 “title”: “Unauthorized”
 }
 ]
}
```

哎呀，因为我们调用 create API 时没有 Auth 令牌，所以不允许我们这样做。让我们使用终端登录以获取新令牌。

```
curl “[https://todolist-tutorial.herokuapp.com/action/user/signin](https://todolist-tutorial.herokuapp.com/action/user/signin)" — data ‘{“attributes”: {“email”: “[tester@gmail.com](mailto:tester@gmail.com)”, “password”: “<password>”}}’
```

登录响应中的令牌:

```
[
 {
 “ResponseType”: “client.store.set”,
 “Attributes”: {
 “key”: “token”,
 “value”: “**eyJhbGciOiJIUzI1NiIsInR5cCI6IkpXVCJ9.eyJlbWFpbCI6InRlc3RlckBnbWFpbC5jb20iLCJleHAiOjE1MDg0MDA4OTcsImlhdCI6IjIwMTctMTAtMTlUMDc6MTQ6NTcuNjUxMjY1NTU5WiIsImlzcyI6ImRhcHRpbiIsImp0aSI6ImI1ZjJlMDA0LTRlMGMtNGMwZi05MDc3LWZmM2NmNThmNmI1OSIsIm5hbWUiOiJ0ZXN0ZXIiLCJuYmYiOjE1MDgzOTcyOTcsInBpY3R1cmUiOiJodHRwczovL3d3dy5ncmF2YXRhci5jb20vYXZhdGFyLzhjM2ZlMWFkMjVlNmQ1ZjQ3NTEyZWE3MzY1NDE5OTY2XHUwMDI2ZD1tb25zdGVyaWQifQ.gM8SdgCpr_iCsI0zGN0nCnKTYiDhkwXZanWrYD1fMRg**”
 }
 }
]
```

我们现在将使用这个标记来创建一个 todo。我们在“授权”报头中发送令牌。该令牌是 Oauth2 令牌。

![](img/7aca0e9d3e5c6ef097e2d50d4d6078e4.png)

我们现在可以使用第一个 api 来获取 todos 列表。我们还需要使用授权令牌。

我们还允许游客创建和获取这些待办事项。但是让我们在下一篇文章中讨论这个问题。

几个结尾注释:

*   这里使用的数据库是 SQLite。我们也可以使用 MySQL 或 Postgresql。同样，我们将在另一篇文章中讨论这个问题
*   Heroku 在空闲 30 分钟后关闭自由实例。因此所有更改都将丢失。付费实例有这个问题，每月花费大约 5 美元。
*   部署的实例将允许来宾注册和登录。单击 daptin 仪表板上的“成为管理员”以关闭注册。您可以稍后启用它。