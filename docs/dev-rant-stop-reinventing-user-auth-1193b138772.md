# 警告:停止改造用户认证

> 原文：<https://medium.com/hackernoon/dev-rant-stop-reinventing-user-auth-1193b138772>

在随意翻阅[书架](http://bookshelfjs.org/)公开问题(无耻之徒:我最喜欢的 node.js [ORM](https://en.wikipedia.org/wiki/Object-relational_mapping) ，部分是因为它的[丰富的插件生态系统](https://www.npmjs.com/search?q=bookshelf-*))时，我偶然发现了一个不愿意透露姓名的问题。问题很简单…

问:我们如何用 bookshelf 建立一个用户注册/认证模型？

![](img/c8aa38eaa4b694e6c935d380eed65b3d.png)

现在，我可以用一个连接到规范的“bookshelf.Model.extend”的直接 knex 模式来帮助这个家伙，但是后来我想……我会给这个可怜的家伙帮倒忙。为什么他和其他许多像他一样的人首先试图重新解决这个问题？

# 新闻组回答

超出范围。关门了。

# 简短回答

如果你一定要问这个问题，你不应该问。:-p

# 中等答案

说真的。观看此视频: [Youtube:如何不存储密码！](https://www.youtube.com/watch?v=8ZtInClXe1Q)

# 长回答

## 1.使用功能全面的服务和成熟的第三方身份提供商

对于绝大多数 B2C 应用来说，你和你的用户会更乐意使用一个成熟的身份提供商(Openid、GitHub、脸书、LinkedIn、Gmail 等)。对于 B2B，他们需要单点登录。您可以有多个标识提供者。这个选择很大程度上是一个商业决策。

在技术方面，第三方提供商如 [auth0](https://auth0.com/) (行业领导者，但还有大约一打其他的)提供这种服务。像 Firebase 这样的其他服务甚至做得更多。

基本上，没有真正的理由自己去做。以上所有功能都包含了用于用户管理的漂亮的 web 控制台和大量其他不值得你自己构建的功能。

## 2.自己与第三方身份提供商合作

Passport-JS 是标准，支持所有主要的提供商。或者，您可以选择持久化一个简单的用户/会话表。没有乱搞安全密码存储，等等。

## 3.自己做

仅当:

*   教育项目/为 lulz

运筹学

*   你知道你在做什么吗
*   有令人信服的理由不做(1)和(2)，例如内部要求、高度管制的行业等。