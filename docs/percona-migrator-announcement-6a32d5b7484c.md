# Percona 迁移程序公告

> 原文：<https://medium.com/hackernoon/percona-migrator-announcement-6a32d5b7484c>

今天，我们很高兴向大家展示 [Percona Migrator](https://github.com/redbooth/percona_migrator) ，一个在 Rails 中运行在线 MySQL 模式迁移的 ActiveRecord [适配器](https://hackernoon.com/tagged/adapter)。自从我们在 2014 年 12 月实施以来，它经历了几个阶段，但它一直是我们在 [Redbooth](https://redbooth.com/) 的工具的重要组成部分。

# 动机

这要追溯到 2013 年初，当时我们开始使用 [Soundcloud 的 LHM](https://github.com/soundcloud/lhm) 来克服 MySQL 5.5 的限制，即模式更改会在更改期间锁定表。这给高度并发和大型的表带来了非常糟糕的体验，这在当时已经存在。

不幸的是，一年后，一个带有单记录表的 bug 加上缺乏对外键约束的支持，使得我们创建了自己的 fork，却忘记了维护。

2014 年下半年，LHM 对我们的生产数据库来说是不够的，生产数据库的规模和负荷一直在增长。对注释和任务(经历最高并发级别的表)运行迁移会导致死锁，除非我们在周末运行它们。即使在 2014 年，也没有人会想到会有零停机时间的迁移和部署。

同样重要的是，LHM 迫使我们放弃 Rails 迁移 DSL，我们认为这是一种使用的乐趣。我们最终得到了不太容易理解的迁移和硬编码的节流值。

所以，我们决定开发一种工具，可以解决这些问题，让我们周末再次放松。

# Percona Migrator

它最初是作为我们的 Rails 3.2 应用程序中的一个库开始的，该库解析了 LHM 的 DSL 迁移，并返回了 Percona 的 [pt-online-schema-change](https://www.percona.com/doc/percona-toolkit/2.2/pt-online-schema-change.html) 命令。为了运行迁移，开发人员必须执行一个 rake 任务，该任务将返回一个带有参数的`pt-online-schema-change`命令，复制并粘贴该命令，然后执行该命令。然后，我们有几个其他的 rake 任务来标记迁移是向上还是向下。

虽然这个过程有点乏味，但它让我们能够毫无问题地将模式更改应用到我们的[数据库](https://hackernoon.com/tagged/database)中，这让我们的周末又回来了。

# 在线模式迁移工具

像大多数在线模式迁移工具一样，比如 Github 的 gh-ost、脸书的 OSC 或 Soundcloud 的 LHM 会改变表而不会通过新表锁定它们。

使用新的模式创建表的副本，并且将行按块复制到表中，而不会对数据库的负载造成太大影响。完成后，此表将替换旧表。

`pt-online-schema-change`和 LHM 的主要区别在于他们工作的堆栈级别。LHM 是一个 Ruby 宝石，它让你用自己的 DSL 编写 Rails 迁移，`pt-online-schema-change`是一个命令行工具，充当数据库客户端，向数据库发送语句。

`pt-online-schema-change`通过自动检查服务器负载，将数据以动态调整的块复制到新表中，这一事实使我们免于过多增加数据库服务器负载，虽然迁移时间稍长，但更安全。

# 用于迁移的 ActiveRecord 适配器

一年后，厌倦了复制粘贴`pt-online-schema-change`命令，我们决定最终将它转移到一个 ruby gem 上，它会自动执行这些命令并为我们更新迁移状态，但仍然是 rake 任务。

然后我们决定开源它，开始写文档，发现了一些缺陷，进一步推迟了发布。我们看到了将它实现为 ActiveRecord 适配器的机会。

起初这听起来很疯狂，但我们开始为 ActiveRecord 3.2 开发适配器，我们的应用程序仍在这个 Rails 版本上运行。

它取代了您在`config/databases.yml`中配置的适配器，并通过 Percona 的`pt-online-schema-change`执行 MySQL 语句。这提供了 Percona Toolkit 的最佳功能，而无需改变编写 Rails 迁移的方式。

如果你以前是`pt-online-schema-change`用户，我们会为你提供保障。您可以通过环境变量向它传递参数。如果你像我们一样来自 LHM，你也不必重写你的迁移；Percona 迁移器将 LHM 的 DSL 翻译成 Rails 的 DSL。

# 把❤️放到你的数据库里

开始使用它非常简单。将此添加到您的 gem 文件中:

[https://gist.github.com/712aaa37606dd1594a9e2f36c7ac6e33](https://gist.github.com/712aaa37606dd1594a9e2f36c7ac6e33)

并运行`bundle install`。没别的了。

对于像这样的简单迁移:

[https://gist.github.com/1f60c16668dbc39fcca2ccd83e4c2361](https://gist.github.com/1f60c16668dbc39fcca2ccd83e4c2361)

运行`bundle exec rake db:migrate:up`时的输出将变为:

![](img/046dbb6d807c17bc6f87325b141e1314.png)

因此，您的迁移将受益于`pt-online-schema-change`的功能，例如:

*   当服务器负载过高或副本延迟过大时，自动迁移中止
*   动态调整块大小，因此数据复制查询不会花费太长时间

查看[文档](https://www.percona.com/doc/percona-toolkit/2.1/pt-online-schema-change.html#options)中的完整列表

感谢 [Adrian Serafin 的](https://github.com/aserafin)贡献，我们最近获得了[对 Rails 4.2 的支持](https://github.com/redbooth/percona_migrator/pull/39)，我们计划很快获得对 Rails 4.1 的支持！

你喜欢它，但你的应用程序在 Rails 5 上？不要犹豫，打开一个 PR，挖到 ActiveRecord！您将在[自述文件](https://github.com/redbooth/percona_migrator/blob/master/README.md)中找到更多详细信息。

移民快乐！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)