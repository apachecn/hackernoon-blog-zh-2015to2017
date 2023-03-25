# 记录慢速 Ecto 查询:元编程历险记

> 原文：<https://medium.com/hackernoon/logging-slow-ecto-queries-adventures-in-metaprogramming-110f3472be33>

![](img/852db88c18f39e1e6519aadcf73a8062.png)

Travis Warren via [Flickr](https://www.flickr.com/photos/travis_warren123/4229031035/in/photolist-7rGTAc-9rCpAB-9rFiHY-9rFhCm-9rCrc8-6nifBe-gVq68-3CZHAC-6nnYzL-9rCnMD-9rCpqe-6nnCyq-5Fasi3-6niERM-9rFneS-9rCoSK-9rCoqk-4ATf5R-9rCqhi-4jEvec-9rFjaC-9rCkJt-bPnq62-9ngRXP-bPnnkt-bAsH5o-BFYkr-bqCcAa-bPniWK-dtJDV4-5An3R-5YjXCa-apogJM-8u5H53-jAqs1-bPneFk-apqZQ1-bAsJS9-4wbPVG-bPn7dH-2pvH9P-bAsKhC-bPnkHH-apqZGh-bPnpG6-apogav-apogT8-apqZEU-bPn4Zi-yiuZN)

缓慢的数据库查询是我的一大烦恼。无论是缺少索引、昂贵的连接还是 N+1，数据库性能灾难都会以多种方式出现。

这就是为什么追踪缓慢查询的来源如此重要。虽然 Postgres 可以很容易地记录缓慢的查询，但是没有办法追踪到发出查询的代码行。这是个问题。

我们可以用 Elixir 开发者工具箱中最像 James Halliday 的工具来解决这个问题:元编程。到本文结束时，您将在一项有意义的工作中运用您的元编程能力:识别 Phoenix 应用程序中缓慢的 Ecto 查询的来源。

# 目标:记录慢速 Ecto 查询的来源

对于每个超过 500 毫秒的 Ecto 查询，我们希望记录查询执行时间、原始 SQL 和对源的回溯:

# 从哪里开始？

添加[一个额外的 Ecto logger](https://alexgaribay.com/2016/02/27/using-elixometer-with-phoenix/) 似乎是一个清晰的起点，对吗？我们甚至不需要元编程！这使我们能够访问原始查询及其执行时间。这里我记录了所有耗时超过 500 毫秒的查询:

示例输出:

```
[info] Slow!!!!: SELECT g0."id", g0."install_id", g0."email", g0."raw_payload", g0."deleted_at", g0."inserted_at", g0."updated_at" FROM "github_integrations" AS g0
```

现在，我们只需要回溯就行了。

**但是，有一个问题**:在`trace/1`中不能追溯到生成查询的应用程序代码。如果您试图通过`Ecto.LogTrace.trace/1`内部的`Process.info/2`获取回溯，它不会返回到您的定制应用程序代码。相反，您将看到这样的回溯— *注意，这里没有对控制器*的引用，例如:

> PSA:不想搞这个？我们正在构建 **Elixir 应用程序监控**在我的日常工作中，它会为你完成大部分工作。 [**提前预约**](https://apm.scoutapp.com/elixir) 。

# 现在怎么办？

让我们覆盖公共的 Ecto 查询函数。*这并不理想*:我们需要确保覆盖所有的函数。如果函数签名或名称在以后的版本中改变了，我们将无法捕获一些缓慢的查询和/或可能在编译时中断。

元编程的危险就在于此。

计划是:

1.  让我们的应用程序的`repo.ex`扩展我们在将要创建的`SlowLogger.Ecto`模块中定义的宏。
2.  `SlowLogger.Ecto`将动态创建一个新的模块`YourApp.Repo.SlowLogger`，它包装了 Ecto 查询函数。我们就能确定他们的执行时间并追踪他们的行踪。
3.  别名`YourApp.Repo.SlowLogger`为`Repo`，这样我们的查询将被默认跟踪。

## 回购. ex

这是我们更新的回购模块:

## 斯洛格。埃克托

当这个模块被`repo.ex`使用时，一个新的模块`YourApp.Repo.SlowLogger`被动态创建。多么 meta！这个模块包装了像`query`、`all`和`update`这样的 Ecto 查询函数。我们就能确定他们的执行时间并追踪他们的行踪。

*这是实现*的肉。[这里是模块源码](https://gist.github.com/itsderek23/f83a3f24d9a417c87da4993e8ce9db48)。让我们走一遍。

我们如何覆盖 Ecto 函数？

技术上来说，我们不能。只有通过`[defoverridable](https://hexdocs.pm/elixir/Kernel.html#defoverridable/1)`关键字引用的 Elixir 函数才能被直接覆盖。运行查询的 Ecto 函数——`all`、`query`、`update`等——没有被列为可重写的。

我们可以解决这个问题。我们将动态创建一个新模块。这个模块包含在我们的`Repo`模块中，我们将定义函数来包装对像`unquote(__MODULE__).query(a)`一样需要它的模块的调用。

`**Module.__using__/1**`

当在`repo.ex`内部调用`[use](http://www.zohaib.me/use-in-elixir-explained/)`宏时，调用该函数。通常这样做是为了在调用模块内部注入函数(`YourApp.Repo`)，在这种情况下，代码会创建一个完整的子模块。

**`[**quote**](http://elixir-lang.org/getting-started/meta/quote-and-unquote.html)`**宏****

**我们已经定义了希望在双重嵌套的`quote`块中跟踪慢速查询的 Ecto 函数。外部的`quote`块通过`use`放在调用模块中。内部引用块包含分配给一个`content`变量的函数定义。`quote`块生成一段独立的代码:它不会立即执行。**

**`**__trace/1**`**

**这将进行实际的跟踪:我们记录 start_time，调用函数，并记录 end_time。**

**`**Module.create/3**`**

**`Module.create(slow_logger_repo_module, contents, __ENV__)`**

**这将从内部的`quote`块中获取内容，并创建我们的新模块来跟踪我们的回购查询。我们将使用这个模块来运行 Ecto 查询。**

## **web.ex**

**我们将在`web.ex`中为动态创建的模块`YourApp.Repo.SlowLogger`起别名，这样我们对`Repo`的调用就通过我们的跟踪来路由:**

## **嘣！痕迹！**

**我们现在记录对缓慢的 Ecto 函数调用的跟踪。**

# **我可以同时拥有原始 SQL 和回溯吗？**

**此时，我们知道如何:**

1.  **记录慢速查询的原始 SQL**
2.  **记录对慢速 Ecto 函数调用的回溯**

**我们没有把它们结合在一起:既看到慢速查询的原始 SQL，又看到其源代码位置的相关回溯给出了完整的画面。**

## **处理/放置和处理/获取**

**我们的结婚戒指将成为长生不老药的过程字典。将日志条目存储在流程字典中，并从我们的跟踪逻辑中获取它。**

**在我们的 Ecto logger 中，我们将把日志条目放在进程字典中:**

**…并获取`__trace/1`中的日志条目:**

**`log_entry = Process.get(:ecto_log_entry)`**

**嘣！我们什么都有了:**

# **TL；速度三角形定位法(dead reckoning)**

**以下是记录慢速 Ecto 查询的说明:**

1.  **将`web.ex`中的回购别名更改为`YourApp.Repo.SlowLogger`。**
2.  **将`Ecto`记录器添加到`config.exs`:**

```
config :yourapp, YourApp.Repo,  
  loggers: [{Ecto.LogEntry, :log, []}, {Ecto.LogTrace, :trace, []}]
```

**3.将`[SlowLogger.Ecto](https://gist.github.com/itsderek23/f83a3f24d9a417c87da4993e8ce9db48)`放到你的`lib/`文件夹中。**

**4.将`[Ecto.LogTrace](https://gist.github.com/itsderek23/03c29bd3077b6e17a43f5233221eb8b3)`放到你的`lib/`文件夹中。**

**5.将下面一行添加到您的`repo.ex`文件中:`use SlowLogger.Ecto.`**

> **PSA:咻…太多的步骤？我们正在构建 **Elixir 应用程序监控**，它将在我的日常工作中为你完成大部分工作。 [**报名提前接入**](https://apm.scoutapp.com/elixir) 。**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**