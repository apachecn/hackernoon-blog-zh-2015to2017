# MongoDB 的问题

> 原文：<https://medium.com/hackernoon/the-problem-with-mongodb-d255e897b4b>

![](img/96d1b321a6f9122c384575614e98394e.png)

当构建一个需要存储数据的 web 应用程序(或任何应用程序)时，要做的最大决定之一是使用什么数据库。在初创企业的世界里，MVP 和敏捷开发占据了主导地位，NoSQL 数据库在[中增长，越来越受欢迎](http://www.techrepublic.com/article/nosql-databases-eat-into-the-relational-database-market/)主要是因为它们的[灵活的](http://www.ibmbigdatahub.com/blog/rise-nosql-databases)特性和易用性。其中，MongoDB 是迄今为止最受欢迎的。[mongose](http://mongoosejs.com/)是 Nodejs 中 MongoDB 的一款神奇的 ODM，它也越来越受欢迎，从 2015 年到 2016 年，npm 下载数量几乎翻了一番[。](https://npm-stat.com/charts.html?package=mongoose&from=2012-01-01&to=2016-12-31)

对于使用 Nodejs/javascript 的 web 应用程序，MongoDB 特别好，因为数据是使用 JSON 对象存储的，使得读写数据变得流畅自然。然而，尽管有这些巨大的优势，MongoDB 仍然缺少关系数据库中最有用的特性之一，即…关系。

![](img/744b6c0da81bbc887a922b1d2fef66c5.png)

Not how it works…

对于几乎任何存储数据的应用程序来说，不同的数据实体之间自然会有关系。用户有角色，购物车有商品，书籍有类别……你明白了。这些关系通常采取三种不同的形式之一:一对一、一对多和多对多。虽然 MongoDB 不是关系数据库，但实际上有两种推荐的方法来表示实体之间的关系。让我们看看每一个，看看它们是如何产生的。

*注意:如果您觉得自己已经熟悉这个话题，或者只是想要一个解决方案，请随意向下滚动至脾气暴躁的猫……(或者只需查看*[*rest-hapi*](https://github.com/JKHeadley/rest-hapi)*)*

## 方法 1: [嵌入文件](https://docs.mongodb.com/v3.0/tutorial/model-embedded-one-to-many-relationships-between-documents/)

在 MongoDB 中关联数据(表示为[文档](https://docs.mongodb.com/v3.0/reference/glossary/#term-document))最自然的方法是将它们嵌入到另一个中。这种方法的优点是只需一次查询就可以检索所有相关数据。这是非常高效的，一开始看起来不错。有时这可能是一种合适的方法，但是依赖嵌入式关系可能会付出很高的代价。考虑一个用户和角色的例子，其中用户可以属于一个角色，而角色可以有多个用户。在这种情况下，我们可以选择将用户文档嵌入到角色文档中，反之亦然。如果我们决定用户是最重要的实体，角色应该嵌入其中，那么我们最终会得到如下所示的数据:

```
Users:
[
   {
      _id: 1234,
      email: 'bob@admin.com',
      role: {
         name: 'Admin',
         description: 'A user with awesome powers.'
      }
   },
   {
      _id: 4321,
      email: 'bill@admin.com',
      role: {
         name: 'Admin',
         description: 'A user with awesome powers.'
      }
   }
]
```

当我们想改变一个角色的细节时，他的方法就出现了问题。如果我们更新第一个用户的角色描述，第二个用户的角色描述保持不变。这是一个问题，如果我们想保持一致，我们几乎总是这样做。对于只有两个用户的情况来说，这似乎不是一个大问题，但是如果有数百个用户呢？查找和更新每个嵌入的角色听起来并不有趣。

意识到这一点，我们可以选择将用户嵌入角色中。这可能会产生如下数据:

```
Roles:
[
   {
      _id: 5678,
      name: 'Admin',
      description: 'A user with awesome powers.',
      users: [
         {
            email: 'bob@admin.com'
         },
         {
            email: 'bill@admin.com'
         }
      ]
   }
]
```

问题解决了，对吧？当然，如果我们的数据库中没有其他实体与用户有关系…这是不太可能的。如果我们有另一个实体“团队”，它可以与多个用户相关联，那么我们又回到了起点。更新用户的电子邮件必须反映在用户所在的任何角色或团队中。随着实体和关系数量的增长，一致性问题会变得更快。这通常会导致代码混乱不堪。我们开始时的效率也消失了。所以也许有更好的方法？让我们看看下一种方法。

## 方法二:[文件参考](https://docs.mongodb.com/v3.0/tutorial/model-referenced-one-to-many-relationships-between-documents/)

我们可以给每个文档一个 id 并存储相关的文档 id，而不是整个文档，而不是将文档嵌入到另一个文档中。让我们来看看上一个例子中的数据，它是这样表示的:

```
Users:
[
   {
      _id: 1234,
      email: 'bob@admin.com',
      role: 5678
   },
   {
      _id: 4321,
      email: 'bill@admin.com',
      role: 5678
   }
]Roles:
[
   {
      _id: 5678,
      name: 'Admin',
      description: 'A user with awesome powers.',
      users: [
         1234,
         4321
      ]
   }
]
```

现在，无论何时更新文档，都不需要其他操作，因为引用 id 不会改变。一致性问题解决！然而，有一个权衡。在方法 1 中，只需要一个查询就可以获取与用户(或角色)相关的所有数据。现在，为了获得角色及其相关用户的数据，我们必须首先执行一个角色查询，然后执行另一个查询，根据 id 查找用户。这可能看起来没那么糟糕，但也可能很快失控。让我们引入团队实体，再次查看我们的数据:

```
Users:
[
   {
      _id: 1234,
      email: 'bob@admin.com',
      role: 5678
   },
   {
      _id: 4321,
      email: 'bill@admin.com',
      role: 5678
   }
]Roles:
[
   {
      _id: 5678,
      name: 'Admin',
      description: 'A user with awesome powers.',
      users: [
         1234,
         4321
      ]
   }
]Teams:
[
   {
      _id: 1357,
      name: 'Managers',
      description: 'They manage things.',
      users: [
         1234
      ]
   },
   {
      _id: 9753,
      name: 'Editors',
      description: 'They edit things.',
      users: [
         4321
      ]
   }
]
```

现在，如果我们想要检索一个角色中包含的所有数据，我们必须执行与前面相同的两个查询，以及第三个更复杂的查询来填充每个用户的团队数据。作为开发人员，您现在不得不为每种需要检索相关数据的情况编写复杂的查询处理程序。

如果您试图通过 web api 检索这些数据，这将变得更加困难。api 必须接受某种“嵌入”查询参数，每个端点都需要支持逻辑来填充嵌套数据。除此之外，还需要更多的逻辑来支持基于引用对象的过滤查询。这个问题通常会导致许多过于特定的端点，这些端点带有针对不同实体结构的不同“类型”查询的定制逻辑。这种方法也可能很快导致难以扩展、难以维护的代码。

![](img/7c3193fb519a398574ca5695cd0b5727.png)

In this case…yes

## 那现在怎么办？

那么我们应该采取哪种方法呢？理想情况下，您会希望混合使用针对您的整体模型结构优化的嵌入和引用关系。问题是这不可能从一开始就做到，因为模型结构在开发过程中必然会改变(即敏捷开发)，通常从一开始就试图“优化”事情可能是一种[被误导的方法](http://seanhess.github.io/2011/12/15/optimization_is_like_firing_clay.html)。

作为解决这些问题的尝试，我构建了一个名为 [rest-hapi](https://github.com/JKHeadley/rest-hapi) 的工具。在我之前的[帖子](https://hackernoon.com/the-problem-with-apis-331f08f7a39c#.f1uch63vr)中，我讨论了 rest-hapi 如何通过将端点逻辑封装到[模型配置](https://resthapi.com/docs/creating-endpoints.html)中来简化 api 的开发。从那时起，rest-hapi 已经升级为一个 [npm 模块](https://www.npmjs.com/package/rest-hapi) / [hapi 插件](https://hapijs.com/plugins)，也可以用作 mongoose ODM 的[包装器](https://resthapi.com/docs/mongoose-wrapper-methods.html)。

使用 rest-hapi，[实体关系](https://resthapi.com/docs/associations.html)被定义为模型配置的一部分，并且提供了支持添加、移除、填充和查询关系数据的包装器方法。它甚至支持一个等价形式的[连接表](https://resthapi.com/docs/associations.html#linking-models)，用于需要特定关系实例数据的多对多关系。虽然这可能不是一个优化的解决方案，但我相信它确实支持一个优化的开发环境。rest-hapi 允许开发人员专注于以敏捷的方式开发应用程序，并快速启动和运行。它结合了 MongoDB 著名的灵活性和关系结构的强大功能。

![](img/a0392afab08e46ee3ba75a678f683676.png)

Fast and Flexible

我最近的努力是开发一个样板用户系统(名为 [appy](https://github.com/JKHeadley/appy) ),它提供通用的用户服务，同时利用 rest-hapi 的能力提供一个全功能的应用引导程序。我刚刚完成了第一个版本，到目前为止，我觉得它体现了 rest-hapi 的有用性。appy 中实现的[权限系统](https://github.com/JKHeadley/appy-backend/wiki/Authorization)甚至利用连接表支持来允许启用/禁用特定的权限关系。

我希望你能花些时间看看 [rest-hapi](https://github.com/JKHeadley/rest-hapi) 和 [appy](https://github.com/JKHeadley/appy) 。我觉得他们可以为开发者社区做出重大贡献。如果您有任何反馈，请随时在下面留下评论或在 [GitHub](https://github.com/JKHeadley/rest-hapi/issues) 中提出问题。如果你想联系我，你可以通过推特[、脸书](https://twitter.com/JKHeadley)[、领英](https://www.facebook.com/justinkheadley)或电子邮件[headley.justin@gmail.com](mailto:headley.justin@gmail.com)联系我。我希望你喜欢这篇文章，感谢阅读！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)