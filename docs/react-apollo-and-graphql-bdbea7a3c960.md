# 反应，阿波罗和 GraphQL

> 原文：<https://medium.com/hackernoon/react-apollo-and-graphql-bdbea7a3c960>

![](img/6c70170bfc65e1996860551c38631c59.png)

在开发一个与 REST API 交互的网站时，一个常见的路径是使用网络管理库和 Redux 来管理状态。这意味着在与 API 通信时，要为不同的阶段编写规约、动作和状态管理。有了好的设计，这可以使用 HOCs 或一些其他结构来避免重复代码。

Apollo 栈旨在简化使用 API 的应用程序的开发。

# GraphQL

对于栈的后端部分，它使用 [GraphQL](http://codigodelsur.us5.list-manage1.com/track/click?u=f2dbabff93db99724b6ef4824&id=813d31d7c8&e=d5e9da9b45) 而不是 REST。 [GraphQL](https://hackernoon.com/tagged/graphql) 是脸书在 2012 年创建的一种查询语言，用于描述客户端-服务器应用程序的数据模型的功能和要求。它解决了 REST 的几个缺点，提供了一种简单的方法来定义数据结构和查询，从而使 API 的开发和使用变得简单而灵活。[有些人甚至称之为 REST 2.0。](http://codigodelsur.us5.list-manage2.com/track/click?u=f2dbabff93db99724b6ef4824&id=681ec882e6&e=d5e9da9b45)

围绕 GraphQL 有一个完整的生态系统。一些值得注意的工具示例有:

*   GraphCool 提供了一种像后端即服务一样使用 GraphQL 的方法。
*   [插件](http://codigodelsur.us5.list-manage.com/track/click?u=f2dbabff93db99724b6ef4824&id=897d0b8b73&e=d5e9da9b45)的存在是为了使用 ESLint 验证查询，避免输入错误，并在后台发生变化时警告开发者。
*   一些人指出 [GraphiQL](http://codigodelsur.us5.list-manage.com/track/click?u=f2dbabff93db99724b6ef4824&id=05accc2942&e=d5e9da9b45) (一个探索和测试 GraphiQL 后端的交互式工具)是 graphi QL 的杀手级应用。

**Apollo**
[Apollo](http://codigodelsur.us5.list-manage1.com/track/click?u=f2dbabff93db99724b6ef4824&id=24d8b47206&e=d5e9da9b45)是一个针对 [React](https://hackernoon.com/tagged/react) ，React Native，Angular，Swift，Java 的 GraphQL 客户端。使用它时，您可以选择配置缓存、突变、乐观 UI、订阅、分页、服务器端呈现、预取等等。

它可以被增量地采用，这意味着它可以为小组件实现，与非 apollo 组件共存。Apollo 在幕后使用 Redux，因此它可以插入现有的 Redux 应用程序，所有 Redux 开发工具都可以处理 Apollo 的查询。

所有与 API 交互相关的代码都由 Apollo 管理，允许开发人员定义组件需要什么数据。

[除了官方文档](http://codigodelsur.us5.list-manage.com/track/click?u=f2dbabff93db99724b6ef4824&id=2dbf34c54e&e=d5e9da9b45)，以下是一些有用的资源:

*   [全栈 React + GraphQL 教程](http://codigodelsur.us5.list-manage2.com/track/click?u=f2dbabff93db99724b6ef4824&id=50a18e89fe&e=d5e9da9b45):实现全 Apollo 栈的优秀 6 部分教程。
*   [我该如何制图？](http://codigodelsur.us5.list-manage1.com/track/click?u=f2dbabff93db99724b6ef4824&id=43452d2270&e=d5e9da9b45):休息时要知道的三件事。
*   [GraphQL 解释了](http://codigodelsur.us5.list-manage.com/track/click?u=f2dbabff93db99724b6ef4824&id=c36c298b92&e=d5e9da9b45):graph QL 如何把一个查询变成一个响应。
*   [用 Apollo 简化 React 组件，用 HoCs 重组](http://codigodelsur.us5.list-manage1.com/track/click?u=f2dbabff93db99724b6ef4824&id=2f17ac45f1&e=d5e9da9b45):保持组件的纯净