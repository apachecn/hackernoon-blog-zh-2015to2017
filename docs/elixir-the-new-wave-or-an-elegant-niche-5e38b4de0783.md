# 灵丹妙药:新浪潮还是一个优雅的利基？

> 原文：<https://medium.com/hackernoon/elixir-the-new-wave-or-an-elegant-niche-5e38b4de0783>

![](img/f4d92f8b4110030d97d6009c530c28a8.png)

就像一个运气不佳的人偶然遇到一个老式的教堂复兴帐篷，我准备好了接受洗礼。

简洁、清晰的语言语法。生产力。一个有魅力的外国领导人。他甚至开赛车——多么奇特！

我在最脆弱的时候——2005 年刚从一个失败的 J2EE 项目中走出来——遇到了 Ruby on Rails，选择生产力而不是性能是我乐于遵守的一个原则。

在过去的 12 年里，鲁比没有让我失望。我还没有找到比 Ruby 更能激励我创作的语言。权衡呢？性能、线程、并发等？我找到了绕过它们的方法(或者把自己孤立起来，不去解决需要它们的问题)。

简而言之，**我和露比结婚很幸福。那么，为什么我突然对一门新的语言产生了怀疑？**

# 进入灵丹妙药:无折衷的语言

去年，在与@ [的一名员工交谈时，我问他们未来几年最兴奋的是什么。他们用一个字回答:](http://thoughtbot.com)[仙丹](http://elixir-lang.org/)。这个故事引人入胜。

仙丹是一种似乎缺乏权衡的语言。它的 Ruby 风格的语法看起来很舒服。它可以并发地做一些事情— [对于像我这样的 Ruby 开发人员来说，这是令人难以置信的事情—](https://dockyard.com/blog/2016/08/09/phoenix-channels-vs-rails-action-cable)—[为构建 web 应用程序开辟了一条不同的途径。所有这些新时代漂亮的代码语法和并发性都建立在 Erlang VM 之上，它为爱立信、T-Mobile 和北电等电信提供商提供了“九个九”的可用性(*即一年 32 毫秒的停机时间*)。](https://robots.thoughtbot.com/how-we-replaced-react-with-phoenix)

反对长生不老药？真的很难。这就像失去了一个求爱，并把你的灵魂交给了这样的女孩:

> 我:你看上那家伙什么了？是的，他看起来像 JCrew 模特，唱歌像约翰·传奇，和他在一起很有趣。但是，拜托——那样的人在高中毕业后是没有前途的。
> 
> 女孩:你知道他要去哈佛，对吧？

缺乏与长生不老药的权衡让我不舒服。人类习惯于做出权衡。Ruby on Rails 代表了一种明显的权衡。长生不老药的权衡是什么，超越它的年轻？

# 我在车站错过了长生不老药公共汽车吗？

作为一名开发人员，我已经习惯了。无论好坏，我没有在不同的语言、键盘布局、操作系统和代码编辑器之间转换。但是，长生不老药引起了我的注意。我已经开始用它开发应用程序了。我很享受。

我飘忽的眼神正在变成行动。十年来第一次，我在想:是否有一辆公共汽车正快速驶出车站，而我不想错过它？

**为了回答这个问题，我在下面放了一个观点/对位(** [**像洋葱**](http://www.theonion.com/multiblogpost/my-computer-totally-hates-me-vs-god-do-i-hate-that-11538) **，但不像搞笑)。**有很多与 Ruby 和 Ruby on Rails 的比较，因为:

1.  我从 1.0 之前的 Rails 开始，在动力上看到了很多相似之处。
2.  Ruby 的腾飞是因为 Rails，Phoenix 是 Elixir 上的一个类似 Rails 的框架。
3.  Rails 是在我眼前发展起来的游戏规则改变者。

最后澄清一下:这个*不是*关于仙丹是否比红宝石好的讨论。我正在争论 Elixir 是否代表了类似 Ruby on Rails 所引发的 web 应用开发的新浪潮。新的浪潮是一个更大的挑战。

# 是的，Elixir 引领了一股新的网络应用浪潮。

## 灵丹妙药让小团队可以解决难题。

Ruby on Rails 让专注于用户体验的小团队更有能力。这些小团队可以在创纪录的时间内开发出漂亮的 CRUD 应用程序(想想 Basecamp、AirBnB、Dribbble、GoodReads 和 ZenDesk 的早期版本)。

2017 年，我 90%的时间都花在 Rails 提供的精彩 CRUD 片段之外。Elixir 和 Phoenix(占主导地位的 Elixir web 框架)以优雅的方式解决了当今的难题:

1.  **在每个请求中，用更少的时间跨更多的服务工作**

单个 web 请求可以从几个来源收集数据:关系数据库、外部 API、内部微服务、NoSQL 数据库。快速呈现输出不仅仅是基于语言的原始速度(虽然 Elixir 比 Ruby 快得多，但 Go 和 Java 有 Elixir beat ),还取决于开发人员如何轻松地同时执行工作。

> 用 Erlang 或 Elixir 与其他语言做并发有点像用 Git 与 Subversion 做分支。在 Subversion 中，做起来非常复杂——而我从来没有做过。在 Git 中，这要容易得多，我一直都在这么做。

摘自 [*灵丹妙药——网络的下一个大语种*](http://www.creativedeletion.com/2015/04/19/elixir_next_language.html)

例如，假设我想:

1.  从 GitHub API 获取一个开放问题列表。
2.  获取我内部存储在数据库中的 Git repo 提交者列表。

这些调用可以彼此独立地执行。为什么要等一个完成后再做另一个呢？

```
github_issues_task = Task.async(fn -> fetch_issues(repo) end)
users_task         = Task.async(fn -> users_in_db(repo) end)github_issues      = Task.await(github_issues_task)
users              = Task.await(users_task)
```

[仙丹本质上是异步非阻塞的](http://www.akitaonrails.com/2015/12/01/the-obligatory-why-elixir-personal-take#coroutines)。当然也有其他语言提供并发性，但是 Elixir *让并发变得容易。*

**2。为每个用户提供快速、实时、双向的更新**

Elixir 和 Phoenix 可以扩展到在一台服务器上支持数百万的 websocket 连接。ActionCable 为 Rails 5 添加了 websocket 支持，[只是不在同一个联盟](https://blog.ably.io/rails-5-actioncable-the-good-and-bad-parts-1b56c3b31404#.laxwn4gml)。有了 Elixir/Phoenix，大多数应用程序可以合理地为每个用户提供一个 websocket 连接。这使得构建快速响应的单页应用程序体验对每个应用程序都是可行的，无论其大小如何。

**3。更少的移动部件**

> 当我们编写 Rails 应用程序时，我们的 Rails 应用程序依赖于许多其他部分。

[中的纳丹龙*为什么和凤凰仙丹一样？*](https://github.com/bignerdranch/why_elixir#simplicity)

一个生产 Rails 应用程序需要几个依赖项:

1.  像 Puma 或 Unicorn 这样的应用服务器。Rails 应用程序不能处理多个请求。
2.  像 Sidekiq 这样的后台作业框架，因为在 web 请求中长时间运行的工作会阻塞。这还添加了另一个依赖项 Redis 来存储作业信息。
3.  另一个服务(Monit，God)监视进程在失败时重新启动。
4.  用于运行计划任务的 Cron。

实现这些部分经常需要来自开发人员和 DevOps 团队的输入——有更多的协调和摩擦。elixir——其中的进程像 Ruby 中的对象一样轻量级——允许您执行上述所有操作，而无需向设置和监控添加额外的服务。

**综合起来……**

能够在每次请求时完成更多工作、与每个用户高效交流、较少需要担心的独立系统的网络应用是下一波应用。

## 长生不老药平易近人

一键到一键，仙丹与红宝石惊人地相似。这是我在 Ruby 中过滤一个`Array`:

```
[1,6,10].select { |number| number > 5}
=> [6,10]
```

…药剂中的`List`也是如此:

```
Enum.filter([1,6,10], fn(number) -> number > 5 end)
=> [6,10]
```

看起来一样，不是吗？当然，Elixir 是一种函数式语言，而 Ruby 不是，但是逐行来看，它和 Ruby 一样平易近人。

## 像 Ruby 一样，Elixir 将把开发者从多种语言中拉出来

Elixir 需要将开发人员从 Ruby(它目前的基础)中吸引出来，以超越利基市场。会的。

像 Pinterest 这样的聪明公司正在从 Java 迁移过来，并取得了巨大的成功。 [Elixir 在它的一个卖点——web sockets——上轻松胜过 NodeJS](https://hashrocket.com/blog/posts/websocket-shootout) ,并且拥有一个[更加复杂的并发模型](http://www.akitaonrails.com/2015/12/03/the-obligatory-flame-war-phoenix-vs-node-js)。

简而言之，许多反对长生不老药的论点可以归结为:

1.  说服你的老板让你用 Elixir 开发应用程序太难了
2.  这个社区太小了
3.  可用的库数量太少

听起来熟悉吗？这些都是反对每一种语言和框架(包括 Rails)的理由。

# 不，灵药只是一种小众语言。

要超越小众，你需要比 [**好 10 倍**](https://bothsidesofthetable.com/your-product-needs-to-be-10x-better-than-the-competition-to-win-here-s-why-6168bab60de7?gi=a681a885f241) 。这在今天很难做到:从 Closure 到 Elm 再到 Rust to Go，对于一门新语言来说，要成为一种标准，有太多专门的选择。

## 是的，CRUD 已经完成了，但是您可以用现有的语言和框架接近 Elixir 所提供的东西。

当您可以在需要时组装并发和实时通信的片段时，为什么要学习新的语言和框架呢？拥抱我们今天拥有的组合服务的灵活性—不需要一个整体:

*   很少有 web 端点*需要*并发。对于那些高容量的，关键的，不死药是一个很好的选择。但是，您不需要为几个特殊的端点迁移整个应用程序。
*   需要实时 websockets？巧妙地使用类似于[推杆](https://pusher.com/)或[的服务。](https://www.ably.io/)
*   有依赖是可以的。Puma、Sidekiq、Redis:这些是广泛使用的、维护良好的、微调过的服务。

## 现有的语言将修复它们的弱点

随着 Rails 越来越受欢迎，其他语言也迅速开发了自己的 Rails 风格的框架。JVM 之上的脚本语言也是如此。Ruby 也知道它有问题，并且正在修复它。

惰性可能会得到回报:原地不动，看着灵丹妙药成长，然后看着你最喜欢的语言挑出最好的部分。

## Elixir 的卖点没有传达给非开发者

Rails 在生产率方面有一个明显的“10 倍于 T1”的优势:用它构建应用程序比像 J2EE 这样的框架快 10 倍。

生产力对*每个人*都很重要，不仅仅是开发人员。这意味着对 Rails 的推动不仅仅来自开发人员，还来自经理、主管和客户。

非开发人员不太清楚 Elixir 和 Phoenix 的卖点。要成为一个新的浪潮，需要从多个方面容易地销售仙丹，而不仅仅是开发者福音传播者。

# 告诉我吧！

对于读到这里的人来说，可悲的是，我不能告诉你长生不老药是否代表了一种新潮流。我刚刚给你的生活增加了一个决定。对此我很抱歉。

然而，**我会密切关注网络插座**。如果开发人员想出一种方法，以令人兴奋的方式在每个应用程序中利用这些，那么 Elixir 将具有巨大的优势。

最后，[在我的日常工作](https://scoutapp.com)中，我和我的同事们即将开始一项长生不老药实验。我会在 Medium 上分享这段旅程中的好与坏，所以请跟我来听故事。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)