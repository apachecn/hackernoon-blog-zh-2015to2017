# GraphQL 和 DDD:缺失的一环

> 原文：<https://medium.com/hackernoon/graphql-and-ddd-the-missing-link-4e992a26b711>

当大多数人谈论 GraphQL 时，他们会提到它在性能、可预测的响应格式、类型系统和版本控制方面的优势。所有这些都是很棒的特性，但是我想强调 GraphQL 的另一个亮点:领域建模。

领域模型捕获特定业务领域的概念和过程(例如:航空旅行、社交网络、银行)。大多数业务领域都有复杂的规则、细微差别和需求，即使它们初看起来很简单。例如，让我们考虑一下银行业:跟踪账户余额和借贷日志有什么难的？首先，这个账户包含哪种货币？该帐户属于哪个管辖区，适用什么税法？这个账户会产生利息吗？这是一个共享帐户吗？随着领域模型有机地成长以解决新问题，它们变得越来越支离破碎、不精确，甚至自相矛盾。

在[领域驱动设计](https://en.wikipedia.org/wiki/Domain-driven_design)中，作者 Eric Evans 提出了一种控制这种日益增长的复杂性的方法:围绕用于描述问题及其解决方案的语言的极端纪律。选择名词和动词来描述一系列相关的过程、问题和解决方案，这些过程、问题和解决方案是精确和一致的，形成了一种“[无处不在的语言](http://martinfowler.com/bliki/UbiquitousLanguage.html)，开发人员、用户和领域专家都可以用它来表达相同的意思。Evans 走得更远:实现应该是无处不在的语言的一种表达，理想情况下采取一种自我记录的形式，并且对于非技术领域的专家来说是合理的。

面向对象编程语言擅长表达 DDD 的核心概念:实体以及它们之间的关系，但是这种无处不在的语言往往在 API 上分崩离析，在 API 上它可以说是更加重要。考虑 REST 架构风格。通过应用 REST 的统一接口约束(特别是它最固执己见的需求:[超媒体作为应用程序状态的引擎](http://roy.gbiv.com/untangled/2008/rest-apis-must-be-hypertext-driven)，您可能会无意中降低流畅表达您的领域模型的能力。其他 API 技术，如 SOAP XML-RPC，保持了域模型的完整性，但代价是相当复杂和有限的可移植性。

我相信 GraphQL 占据了一个最佳位置——它简洁地表达了域模型的关系图，而不会泄漏实现细节，同时保持足够简单，可以在不同类型和版本的客户端之间共享。正如在其他模式驱动的 API 技术中一样，GraphQL 模式可以被看作是这种无处不在的语言的一种表达。例如，考虑这样一个需求:“组有一个管理员集合”。在代码中，这可能看起来像

```
var groupId = 1234;
var admins = getGroupById(groupId).getAdmins();
```

在 GraphQL 查询中:

```
{
  group(id: 1234) {
    admins { id }
  }
}
```

请注意，由于 GraphQL 类型系统，语言表达式“Groups have a collection of admins”在这两种情况下都是清晰而完整的。相比之下，REST API 可能有:

```
HTTP GET [http://api.com/group/1234](http://api.com/group/1234)...{
  “id”: 1234,
  “admins”: {
    “href”: “http://api.com/group/1234/admins”
  }
}
```

但是我们失去了一些东西。group/admins href 指定的资源的类型/形状是什么？REST API 的语言描述已经偏离了我们想要表达的意思。现在，它只表达了“组有一个相关的资源:admins”，并暗示您可以自己解决剩下的问题(希望存在好的文档)。我相信这种*客户端驱动的数据获取*用例并不符合 REST 作为一种架构风格的优势。

领域驱动设计强调伟大的无处不在的语言不是免费的。工程师和领域专家需要紧密合作来发展这种无处不在的语言，以适应新的和不断变化的需求。为了使用 GraphQL API 完成这项工作，可以考虑让非技术人员审查对模式提出的更改(理想情况下，他们可以在[graph QL](https://github.com/graphql/graphiql)中摆弄这些更改)。他们应该能够给出关于类型、关系或字段是否与他们对领域模型的解释兼容的有用反馈。预计会有很多关于如何命名的争论。如果你尝试一下，请给我写信，我很想听听你学到了什么！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！