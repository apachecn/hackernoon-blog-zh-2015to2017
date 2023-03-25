# Node.js 每周更新—2017 年 10 月 27 日

> 原文：<https://medium.com/hackernoon/node-js-weekly-update-27-october-2017-a8117ecf7a5d>

**下面你可以找到**[**rising stack**](https://risingstack.com/)**最重要的 Node.js 更新的集合，本周项目&教程:**

## [节点 v8.8.0(当前版本)，10 月 24 日](https://nodejs.org/en/blog/release/v8.8.0/)

显著变化:

**加密:**

*   揭露 ECDH 阶级

**http2:**

*   默认情况下，http2 现在是公开的，不需要标志
*   添加了一个新的环境变量 NODE_NO_HTTP2，以允许需要 userland http2
*   增加了对通用`Duplex`流的支持

**模块:**

*   ESM 生命周期中添加了解析和实例化加载程序管道挂钩

**zlib:**

*   CVE-2017–14919-在 zlib v1.2.9 中，进行了一项更改，该更改导致在 windowBits 设置为 8 的情况下初始化 raw deflate 流时引发错误。在某些版本中，这将导致节点崩溃，您无法从中恢复，而在某些版本中，它会引发异常。Node.js 现在会优雅地将 windowBits 设置为 9，复制传统行为以避免 DOS 向量。

10 月 26 日发布了一个[节点 v8.8.1 修复程序](https://nodejs.org/en/blog/release/v8.8.0/),用于修复空句柄超时问题。

## [Rich Trott (@trott)如果破坏 Node.js](https://mobile.twitter.com/trott/status/915624306750537728) 没有 V8 提交可以登陆

> *Keynote by*[*@ fhinkel*](https://twitter.com/fhinkel?ref_src=twsrc%5Etfw)*告诉大家，V8 现在的政策是，如果破坏 Node.js，任何 V8 提交都不能落地。🎉*
> 
> *—有钱的 Trott(@ Trott)*[*2017 年 10 月 4 日*](https://twitter.com/trott/status/915624306750537728?ref_src=twsrc%5Etfw)

## [测试 Node.js API](https://code.tutsplus.com/tutorials/testing-a-nodejs-api--cms-29689)

刚刚用 Node.js 搭建了一个 API？如果你真的想在开发上有所进步，你必须开始测试你的 API。

在本教程中，您将学习如何编写 Node.js API 测试。潜进去！

## [谷歌 APIs Node.js 客户端](https://github.com/google/google-api-nodejs-client)

Google 支持 Node.js 客户端库来评估 Google APIs。Github 上的这个库仍处于维护模式，因为他们仍在处理一些错误，并添加新功能以满足或访问 Google APIs 的要求。代码打开！

## [V8 版本 6.3](https://v8project.blogspot.hu/2017/10/v8-release-63.html)

本周，V8 版本 6.3 已经公布，该版本处于测试阶段，直到几周后与 Chrome 63 Stable 协调发布。V8 v6.3 充满了各种面向开发人员的好东西，例如改进的速度、内存消耗和调试，以及新的 ECMAScript 语言特性。

## [微软为计算密集型应用 Node.js 添加多线程](https://www.infoworld.com/article/3234744/node-js/microsoft-adds-multithreading-to-nodejs-for-compute-heavy-apps.html)

微软的 beta 版 Napa.js 运行时为 Node.js 服务器端 JavaScript 平台提供多线程支持，以提供 JavaScript 的灵活性和类似 C++的快速性能。

> *通过将多线程引入 Node.js，Napa.js 运行时可以更轻松地处理计算密集型任务。*

## [带 AWS Lambda、API 网关和 DynamoDB 的无服务器节点](https://node.university/blog/1176932/aws-serverless)

> Lambda 和 AWS API Gateway 提供了一种快速高效的方式来构建 RESTful APIs。开发人员可以用 JavaScript 或任何其他主要编程语言设置 lambda(即函数)，并通过 HTTP 请求触发它。

怎么做？在本教程中检查一下。您将创建一个 lambda CRUD 微服务，它将数据保存在数据库中。

## [在 Node.js 上，Go 和并发](https://edneypitta.com/on-node-go-concurrency/)

Node.js 的创建者 Ryan Dahl 在谈到 Node 的并发模型时说道:

> *[……]我认为 Node 并不是构建海量服务器 web 的最佳系统。我会用 Go 来表示。老实说，这就是我离开 Node 的原因。人们意识到:哦，实际上，这并不是最好的服务器端系统。*

这对 Node.js 社区意味着什么？这是节点时代的终结吗？

## [我们的初学者 Node.js 教程现在也有俄语版本了](https://blog.risingstack.com/node-js-tutorial-russian-translation/)

我们很高兴地宣布，我们最全面的 Node.js 教程系列现在有两种语言版本:[英语](https://blog.risingstack.com/tag/node-hero-getting-started-with-node-js/)和[俄语](https://blog.risingstack.com/node-js-tutorial-russian-translation/)。

> *通过消除语言障碍，我们希望许多新开发人员能够了解 Node.js 提供的奇妙可能性。*

看看吧！

## [node . js 中基于令牌的认证，使用护照、JWT 和 bcrypt](https://jonathas.com/token-based-authentication-in-nodejs-with-passport-jwt-and-bcrypt/)

当您开发一个 API 时，大多数时候您需要它的部分或全部端点来要求认证。如何使用 Node.js 做到这一点？

passport.js 与 JWT 和 bcrypt 的组合是实现它的最佳方式之一。是时候无国籍了！

# Node.js 每周更新前情提要

在之前的 [Node.js 每周更新](https://community.risingstack.com/node-js-weekly-update-october-20/)中我们收集了 Node.js 类似 DOS 安全漏洞的最新消息，查看了如何用 OpenID Connect 搭建站点，如何文档化你的 Node.js API，介绍了 StdLib 源代码，见证了一场 Javascript vs. Java 的史诗级战役。[错过了就点击](https://community.risingstack.com/node-js-weekly-update-october-13/)！

*我们还帮助您每天更新 Node.js。查看我们的* [*Node.js 新闻*](https://news.risingstack.com/) *页面及其* [*推特供稿*](https://twitter.com/NodeJS_Daily) *！*

*原载于 2017 年 10 月 27 日*[*community.risingstack.com*](https://community.risingstack.com/node-js-weekly-update-october-27/)*。*