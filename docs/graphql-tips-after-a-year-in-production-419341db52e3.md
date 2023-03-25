# GraphQL:生产一年后的提示

> 原文：<https://medium.com/hackernoon/graphql-tips-after-a-year-in-production-419341db52e3>

当我一年多以前写 [Meatier](https://github.com/mattkrick/meatier) 的时候，我很早就采用了 [GraphQL](https://hackernoon.com/tagged/graphql) 来替换我的 REST 端点、ORM 和我的必要的客户端数据请求。不久之后，当我受雇从头开始开发一个[开源实时应用](https://github.com/ParabolInc/action/)时，我抓住了在服务器上使用 GraphQL 的机会，并将其作为我自己的客户端缓存的基础。一年后，我了解到为生产构建一个 GraphQL 应用程序与你在 GitHub 上看到的那些演示应用程序有很大不同。去想想。在我犯了所有的错误之后，这里是我得到的教训。

GraphQL 是灵活的。刚开始时，你经常怀疑自己的做法是否“正确”。GraphQL 应该和 mongoose 这样的 ORM 对话，还是直接进入数据库？您是为密码、段落和标题创建自定义标量库，还是使用外部验证？你喜欢更多更小的变异(例如 **updateName，updateTitle** )还是更大更强大的变异(例如 **updateUserDoc** )？你应该用它来认证吗？(是)作为外部 API 的中介怎么样？(是的！)它能做订阅了吗？(有点)。让我们开始吃吧。

# 1.文件夹结构

哈欠节。但是说真的，保持 GraphQL 模式的模块化至关重要。我为每个 DB 实体保留了一个独立的文件夹( **Post** 、 **Comment** 等)。)内部是每个操作的离散文件( **PostQuery，PostMutation，PostSchema** )。用文字解释文件夹结构很烂。[点击这里查看](https://github.com/ParabolInc/action/tree/master/src/server/graphql)。现在开始有趣的事情…

# 2.使用 Node.js 连接到数据库

在生产中，您使用驱动程序连接到数据库。所有的演示都在文件的顶部导入它们的 DB 驱动程序。这是废话，主要原因是:它不懒！当您启动服务器时，您的 GraphQL 端点将解析您的 GraphQL 模式文件，这反过来将解析您的 db 驱动程序并启动该连接。通过急切地实例化 DB 连接，您增加了启动服务器所需的时间。更重要的是，您的连接现在是有状态的，因此任何使用您的模式的 webpack 魔术(考虑为服务器端呈现创建一个 webpack 包)都需要管理该连接，并在必要时终止它。想象一下，构建一个通用的应用程序，同时构建客户端和服务器包&，然后可能启动服务器。就像叫最后一个出去的人关门，却看不到也听不到任何人。意大利面代码变得不可避免。为了避免头痛，我只在必要时建立连接:

```
// getDBDriver.js
let driver;
export default function getDBDriver() {
  if (!driver) driver = startDriver();
  return driver;
};
```

现在，我不导入驱动程序，而是导入我的包装器&在 **resolve** 方法中调用它:

```
// in userSchema.js
resolve(source, {id}) {
  const db = getDBDriver();
  return db.get(id);
}
```

现在我的进口没有副作用。耶关闭！

# 3.结构化解析函数

在每个 GraphQL 演示中， ***resolve*** 函数除了解析什么也不做。一定很棒。在生产中，攻击者调用您的 GraphQL 端点，试图查询不属于他们的文档。即使有人拥有该文档，他们也可能试图传入不应该允许的值(例如 **userCredits = $1000000** )。那么，如何在保持代码可读性的同时保护自己呢？我的答案是我称之为验证/确认/解决模式的东西。为了描述它，让我们假设我们有一个应用程序，它的表单允许一个人更改他们的团队名称:

```
async resolve(*source*, {*updatedTeam*}, {*authToken*}) {
  const db = getDBDriver();

  // AUTH
  requireUserOnTeam(*authToken*, *updatedTeam*.id);

  // VALIDATION
  const validate = makeTeamSchema();
  const {errors, data: {id, name}} = validate(*updatedTeam*);
  handleSchemaErrors(errors);

  // RESOLUTION
  return await db.table('Team').get(id).update({name});
}
```

在 5 行代码中，我建立了身份验证和授权。我已经验证并规范化了参数，并返回了结果。我们来分解一下。

## 作家（author 的简写）

在一个完整的企业 SaaS 中，我发现每一个突变只需要大约 5 个唯一身份验证检查中的 1 个。(他们签到了吗？他们在编辑属于他们或他们团队的东西吗？他们有活动的 websocket 连接吗？).这些是廉价的检查，以确保用户可以做他们想做的事情。理想情况下，它们是同步的。为了使它们同步，我有时会作弊&在主键中加入额外的信息。比如一个用户有一个待办事项，而那个待办事项不能改变用户，我就把 id **设为 user123::xYz8yUo** 。当用户试图更新它时，我们可以将待办事项的 **id** 与他们的 authToken/cookie 中的 id 进行比较。当你有一个实时应用程序，并且以亚秒的间隔编辑同一个文档时，这种事情就变得至关重要。我不在乎你是否能黑掉选举，你仍然不能改变数据库中的主键。

## 确认

现在我们知道了*用户*是好的，我们需要看看*数据*是否是好的。当然，您可能已经在客户端验证了这一点，但是安全 web 应用程序的第一条规则是永远不要信任客户端。为此，我使用了一个函数来验证和规范化(例如 **String.trim()** )数据。有很多流行的库，比如臃肿的 [Joi](https://github.com/hapijs/joi) 和 async [Yup](https://github.com/jquense/yup) ，但是我推出了自己的 [100 LOC 解决方案](https://github.com/ParabolInc/action/blob/ef26d6151addd6c9a34f263f115bb30cc82fc4e1/src/universal/validation/legitify.js)，它直接插入到 [redux-form](http://redux-form.com/6.2.1/) 中，因此客户端和服务器可以使用相同的验证模式。

为什么没有 GraphQL 自定义标量？我以为那是应该走的路，但我错了。不幸的是，他们只是调情有用。他们冗长而缺乏力量。例如，如果我想验证一个新用户的电子邮件地址，我需要一个正则表达式，但是我还需要确保它不存在于我的数据库中。如果我需要在 **resolve** 函数中验证这一点，我也可以在那里使用正则表达式(并返回一个客户端错误消息，比 GraphQL 给我的糟糕结果要漂亮得多)。

# 4.编写您的模式

有些人喜欢使用 GraphQL 简写来编写大文本 blob 模式。我不这样做的原因和我不把我所有的 JavaScript 写在文本文件中一样。这很难写，我喜欢 IDE 给我的彩色错误曲线。当然，在未来，编辑可能会发现 GraphQL 位的代码&在运行中丢弃它们，但那一天不是今天。

至于描述，有 2 件重要的事情需要包括:

*   星号表示数据库中有索引的字段(这样那些不能直接访问数据库的人仍然可以编写高效的查询)
*   描述如何生成任何 id 的描述符。这可能是:**短 ID，UUID，团队 Id::用户 id，**等。只是一些让你知道期待什么和你可能从中提取的任何非随机信息(对多对多关系特别有用)

# 5.写你的突变

古老的问题:你愿意和一只马大小的鸭子战斗，还是和 100 只鸭子大小的马战斗？

![](img/2ec82e7315f1137c4bc7807c06c75c47.png)

Honestly, I’d take the horses. I’m from the country. I’ve had to run from angry ducks.

它也适用于 GraphQL。您应该编写 1 个变体来处理 100 种不同形式的数据，还是为文档的每种不同编辑方式编写 100 个不同的变体？虽然正确的答案总是“视情况而定”，但我喜欢默认更大、更强大的突变，因为它让你迭代得更快。如果你的表单得到了一个新的字段，你把它添加到前端，你把它添加到你的 GraphQL 模式，你的验证模式，你就可以开始了。当您开始在 **resolve** 函数中看到多个巨大的条件块和越来越多的参数时，您就知道是时候把它拆开了。

# 结束语

就是这样！您的 GraphQL 中级课程已经完成。如果你想深入了解 GraphQL 中的 auth 和错误处理，你可以随时查看我的 [GraphQL 域 Auth 指南](/front-end-developers/graphql-field-guide-to-auth-ead84f657ab#.5nubbma4n)。我是不是满口胡言？请在评论中告诉我。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)