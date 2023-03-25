# 为您的应用程序构建 API 的最快方式

> 原文：<https://medium.com/hackernoon/the-fastest-way-to-build-api-for-your-app-53e36e01b0ed>

![](img/43471e19fde2404d56c5dab9153ce68e.png)

DA-14 的开发团队为不同的领域设计和构建了 API，比如工程和建筑、[旅游&旅行](https://goo.gl/rrN9yp)等等。多年来，我们已经学到了一些有用的经验，并定义了一些轻松快速地创建 API 的最佳实践。

大多数公司都遇到同样的问题，[从头开始构建自己的 RESTful API](https://da-14.com/services/api-development)。例如，应用 Express 非常耗时，因为它需要使用参数、安全性、端点等逐步设计和设置每个单独的模型。在这方面，LoopBack 很快获得了牵引力，减轻了 API 开发过程的痛苦并减少了工程时间。

## API 开发的回送优势:

*   构建于 Express.js 之上
*   基于模型的
*   用 Yeoman 快速搭建动态 RESTful APIs
*   开箱即用的多数据库兼容性，如 PostgreSQL、MySQL、MongoDB 等。
*   通过 Passport.js 将用户认证与 oAuth 和社交登录集成在一起
*   狂妄的顺从
*   易于部署和扩展

## 安装环回:

*   使用 npm 安装环回:

```
$ npm install -g strongloop
```

(注意–g 代表“全局”,其用法并不重要)

*   继续构建新的应用程序。运行 slc loopback 启动并选择您的项目名称
*   这将为您的新 API 自动生成所有核心文件/代码。对于测试，使用新目录和运行节点。在[http://localhost:3000/Explorer](http://localhost:3000/explorer)的 API Explorer 允许使用 API 端点和测试操作。

进一步的步骤取决于应用程序类型、选择的数据库和其他因素。例如，我们想分享我们在旅游行业的一个市场解决方案的 API 开发。

*   继续点击 ORM:

```
npm install loopback-datasource-juggler --save
```

*   链接到 PostreSQL 的连接器:

```
npm install loopback-datasource-postgresql --save
```

*   连接到首选数据库:

```
{ "db": { "name": "db", "connector": "postgresql", "database": "dbname", "username": "dbuser", "password": "dbpassword" }}
```

## 为什么环回是最好的选择？

*   **高适应性**和**开源**框架——庞大的 Node.js 模块库，可以一起使用，也可以单独使用；
*   **精细结构安全** —回环允许为应用程序设置访问数据、定义角色和定制访问控制列表(ACL)。访问控制模块开箱即可工作；而且 LoopBack 通过 Passport.js 提供对社交登录的支持；
*   **部署和可扩展性** —为了简化部署流程并抓住服务器空间的机会，所有节点应用程序都可以扩展到多个主机或单个主机上的多个进程。这种垂直/水平扩展或简单的集群可确保零应用停机时间，开箱即用，无需编码；
*   **Swagger 2.0 合规性**确保交互式文档、可读性和客户端 SDK 生成。Swagger 拥有最大的 API 工具链堆栈，同时它是 REST API 的一个简单而强大的表示；
*   **API 版本**促进了未来的更新并简化了进一步的支持。你可能不会马上升级你的应用程序，但这是设计你的 API 时首要考虑的问题。版本控制可以防止现有应用程序在发生任何变化时崩溃，例如重命名或删除参数可能会导致许多或所有客户端系统停机的风险；
*   **兼容数十种 SQL 和 NoSQL 数据源**，包括 MongoDB、SQL Server、MySQL、Oracle 等。API 平台还有助于连接各种消息传递、服务和大数据后端，无需编写连接、逻辑或额外代码；
*   LoopBack 支持通过**内置 Arc 工具模块**收集和查看性能分析。Arc 提供了用于监控、配置和构建节点应用程序的工具。此外，它允许开发人员找到瓶颈并跟踪执行路径；
*   **日志记录**对于审计和调试应用程序至关重要，例如在生产阶段，日志轮换可以方便日志文件的处理和存储，并节省服务器空间。我们主要使用 **Winston** 的大量功能和易用性。这个日志库使开发人员能够:使用各种传输或创建自定义传输；流/查询日志；执行分析并处理异常；使用预定义的错误级别或创建自定义的错误级别；
*   LoopBack 允许您控制在有限的时间内可以发出的 API 请求的数量。这更好地被称为**速率限制**。

与 Express.js 和类似的框架相比，使用 LoopBack 可以编写更少的代码。除此之外，它消除了为每个端点编写处理程序的需要，支持方便搜索和分页的过滤器，将代码存储在单独的模型 JS 文件中，等等。其中最大的优势是 API 代码的快速和简单的生成、设计和测试。这种方法允许开发人员主要关注用户体验和业务逻辑，从而构建安全、文档完善、高性能的 API。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)