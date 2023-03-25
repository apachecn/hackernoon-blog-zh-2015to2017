# 如何在快递网关中实现密钥认证

> 原文：<https://medium.com/hackernoon/how-to-implementing-key-authentication-in-express-gateway-4c9a87221e65>

由[乔丹·卡斯帕](https://jordankasper.com/)

所以，你可能以前用过:密钥认证。基本思想很简单，用给定的服务认证你的应用或客户端，你发送一个密钥来识别(和授权)你自己。此外，这并不一定是针对单个用户的，而是针对系统之间的相互对话。(需要明确的是，对于验证自己身份的用户，您可能需要查看 OAuth2，这也是 Express Gateway 提供的功能。)

在本文中，我将向您展示如何使用 Express Gateway (EG)快速轻松地启动和运行密钥认证。我们将讨论如何设置和配置您的网关、创建凭证以及发送经过身份验证的请求。这是一个简单的介绍，所以在部署 API 网关之前，请务必阅读文档并进行测试！

# 创建和配置网关

第一步可能是生成一个新的 Express Gateway 实例。(如果您已经这样做了，请随意跳过这一步。)您需要首先安装`express-gateway`包，然后生成一个新的网关:

```
~$ npm i -g express-gateway~$ eg gateway create
 ? What's the name of your Express Gateway? widget-factory
 ? Where would you like to install your Express Gateway? widget-factory
 ? What type of Express Gateway do you want to create? Getting Started with Express Gateway
 created package.json
 created server.js...To start widget-factory, run the following commands:
 cd widget-factory &amp;&amp; npm start
```

太好了！在我们第一次启动网关之前，让我们继续进行配置。

# 网关配置

Express Gateway 在新项目的`/config`目录中有两个主要的配置文件(加上模型配置):`gateway.config.yml`文件和`system.config.yml`文件。在系统配置文件中，您将设置数据库访问(针对网关，而不是您的单个微服务)等内容，以及 OAuth2 等内容的某些安全设置。我们今天不会在那个文件中工作(这也意味着我们的**用户和凭证不会被保存**。

网关配置文件是配置 HTTP、端点、策略(如密钥认证)和管道(只是应用于某些端点的一系列策略)的地方。默认生成的配置文件在`/ip`为网关创建了一个 API 端点，并将这些请求代理给<https://httpbin.org/ip>——您稍后会想要更改它，但我们现在将它保留下来。我们需要做的是向“api-basic”管道添加一个策略(这里唯一的一个)。

在`gateway.config.yml`文件中找到您的“pipelines”块，使它看起来像这样:

```
pipelines:
 - name: api-basic
 apiEndpoints:
 - api
 policies:
 - key-auth: ## This line is new!!
 - proxy:
 - action:
 serviceEndpoint: httpbin
 changeOrigin: true
```

请注意，我们在“代理”策略之前添加了“密钥授权”策略。每个管道中的策略都是按顺序排序的，所以一定要按照您希望的顺序来执行。在我们的例子中，如果认证失败，我们应该*而不是*代理 API 请求。

信不信由你，这是您需要对我们的演示网关进行的唯一的*更改，以启用密钥认证！您可以通过启动网关并向`/ip`(或任何其他端点)发出一个简单的`GET`请求来测试这一点:*

```
~/widget-factory$ npm start
```

您可以使用 cURL 或者类似于 [Postman](https://www.getpostman.com/) 的工具(我强烈推荐用于 API 开发)来测试 HTTP 请求。下面是使用 cURL 可能得到的响应:

```
~$ curl -D - "http://localhost:8080/ip"
 HTTP/1.1 401 Unauthorized
 X-Powered-By: Express
 Content-Type: text/html; charset=utf-8
 Content-Length: 9
 ETag: W/"9-PatfYBLj4Um1qTm5zrukoLhNyPU"
 Date: Sun, 16 Jul 2017 19:37:52 GMT
 Connection: keep-aliveForbidden
```

请注意，我们的响应代码是`401`，因为我们发送的是*而不是*API 密钥。稍后我们将更多地讨论这些状态代码，但是现在让我们创建一个 API 键。

# 正在生成密钥凭据

我们创建 API 键的第一步是在系统中创建一个“用户”。打开另一个终端窗口，导航到您的网关项目目录。现在，我们可以使用与生成网关相同的`eg`命令来创建凭证:

```
~/widget-factory$ eg users create
 ? Enter username [required]: jordan
 ? Enter firstname [required]: Jordan
 ? Enter lastname [required]: Kasper
 ? Enter email: jordan@widgets-r-us.com
 ? Enter redirectUri:
 ✔ Created jordan
```

我在这里把`redirectUri`留为空白，因为我们不在密钥认证中使用它，但是你可能需要其他方案。现在我们有了一个用户，可以为该用户创建一个“应用程序”，然后创建“凭据”，或者我们可以只为用户自己创建“凭据”。我现在做第二个选择:

```
~/widget-factory$ eg credentials create -c jordan -t key-auth
 ✔ Created
 {
 "isActive": true,
 "createdAt": "Sun Jul 16 2017 15:48:48 GMT-0400 (EDT)",
 "updatedAt": "Sun Jul 16 2017 15:48:48 GMT-0400 (EDT)",
 "keyId": "0J6961Lhn8JgxYybTXFdRg",
 "keySecret": "23BfI6QVqgdxP3ty8F4Jx3",
 "scopes": null,
 "consumerId": "jordan"
 }
```

就是这样！注意，上面的输出显示出了`"keyId"`和`"keySecret"`。这两部分一起为系统创建了最终的 API 密匙。

# 发送经过身份验证的请求

现在我们有了一些凭证，我们可以向我们的 API 发送更多的请求(通过我们的网关)。

```
~$ curl -H "Authorization: apiKey 0J6961Lhn8JgxYybTXFdRg:23BfI6QVqgdxP3ty8F4Jx3" -I "http://localhost:8080/ip"
 HTTP/1.1 200 OK
 x-powered-by: Flask
 connection: close
 server: meinheld/0.6.1
 date: Sun, 16 Jul 2017 19:54:49 GMT
 content-type: text/html; charset=utf-8
 content-length: 12793
 access-control-allow-origin: *
 access-control-allow-credentials: true
 x-processed-time: 0.00561594963074
 via: 1.1 vegur
```

上面有两点需要注意:首先，我们得到了一个`200`响应！太好了，这意味着我们的认证检查通过了！那么我们是怎么做到的呢？我们发送了包含密钥的`Authorization`报头。记住，我们的密钥由两部分组成。所以你可以看到上面的`Authorization`头值实际上是由冒号(“:”)分隔的两条信息。此外，我们用我们的头方案“apiKey”作为该值的前缀。

```
Authorization: apiKey 0J6961Lhn8JgxYybTXFdRg:23BfI6QVqgdxP3ty8F4Jx3header name : scheme keyId : keySecret
```

# 状态代码

正如您在上面看到的，我们得到了一个`200`响应状态代码，意味着成功！那个状态代码实际上来自我们的 API 服务(在这个例子中是 httpbin.org)*而不是来自我们的网关*。例如，如果您请求一个在该服务上不存在的资源，您应该收到一个`404`。当密钥没有通过认证或者完全丢失时，网关将发送回一个`401`。但是如果用户通过了身份验证，但没有被授权使用给定的资源，它也可以发送回一个`403`。如果您使用“范围”,这可能会发生。

# 什么是范围？

在这篇博文中，我们不会深入讨论作用域，但是作用域是在 Express Gateway 中指定授权的主要实体。范围只是添加到网关配置中的预定义字符串(在 API 端点上，然后在该端点的管道中的策略上)。API 端点通过指定范围来保护。要获得受范围保护的 API 端点的授权，使用者必须拥有包含 API 端点上列出的范围的凭据。换句话说，端点上的范围必须与用户(或应用程序)的密钥凭证上的范围相匹配。

# 附加配置

您可以将更多选项添加到您的网关“key-auth”策略中，以进一步保护它或简单地定制它。例如，默认情况下，网关将接受头和查询字符串中的键。您可以使用`disableQueryParam`选项轻松禁用此功能:

```
pipelines:
 - name: api-basic
 apiEndpoints:
 - api
 policies:
 - key-auth:
 - disableQueryParam: true ## Disable API keys in the query string
 - proxy:
 - action:
 serviceEndpoint: httpbin
 changeOrigin: true
```

您还可以更改用于身份验证的标头(尽管这将违反当前标准)或使用的方案:

```
pipelines:
 - name: api-basic
 apiEndpoints:
 - api
 policies:
 - key-auth:
 - apiKeyHeader: "X-My-Auth-Header"
 - apiKeyHeaderScheme: "key-pair"
 - proxy:
 - action:
 serviceEndpoint: httpbin
 changeOrigin: true
```

如果您使用上面的配置，您需要修改在所有经过身份验证的 API 请求中发送的报头，如下所示:

```
~$ curl -H "X-My-Auth-Header: key-pair 0J6961Lhn8JgxYybTXFdRg:23BfI6QVqgdxP3ty8F4Jx3" -I "http://localhost:8080/ip"
```

# 管理密钥凭据

我们这篇文章的最后一个主题是关于管理你生成的那些密钥。您可能会发现需要取消用户访问权限的时候。这可以通过在命令行中停用他们的凭据来轻松实现:

```
~$ eg credentials deactivate -t key-auth 0J6961Lhn8JgxYybTXFdRg
 ✔ Deactivated 0J6961Lhn8JgxYybTXFdRg
```

执行此操作后，给定的`keyId`将不再在网关中被认证。这是*而不是*一个永久的动作，并且凭证可以很容易地用伴随的`activate`子命令重新激活:

```
~$ eg credentials activate -t key-auth 0J6961Lhn8JgxYybTXFdRg
 ✔ Activated 0J6961Lhn8JgxYybTXFdRg
```

# 包扎

API 网关可以为您做的最大的事情之一是为您的各种微服务集中认证。Express Gateway 使这一过程变得非常简单。看一下[文档](http://www.express-gateway.io/documentation)试试看！

查看**[**Express Gateway**](http://www.express-gateway.io)**成为这个完全基于 Express.js 构建的新开源 API 网关的贡献者或维护者****