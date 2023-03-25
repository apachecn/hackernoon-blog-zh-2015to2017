# 更好的实体框架

> 原文：<https://medium.com/hackernoon/better-entity-framework-77ed80bd3717>

我读过的所有关于博客帖子的东西都说我需要一个大图来开始。所以，现在开始…

![](img/f9b9014ede5508e43ad3fb76f524ecf8.png)

This should be the fate of the repository pattern.

首先，我必须说，有人愿意看我的第一篇博客，我感到很谦卑。我接近最后一个条目的时候，我认为几个星期内会有几十个人阅读它，如果是这样的话。在我写这篇文章的时候，它已经有超过 2000 次阅读，几个 Twitter 分享，包括一个微软 MVP，一个实体框架团队的工程师，还有一个同事突然问我是不是真的是我。要点:检查态度，提炼信息。

所以，说重点。我对上一篇文章的主要印象是，人们强烈渴望好的实体框架资源。这些问题是众所周知的，许多人都感到沮丧，人们想知道该怎么办。这就是这篇文章的内容。

## **0)步代**

黄铜钉时间；这是我将要使用的软件包。

(TLDR； [GitHub repo](https://github.com/hoagsie/BetterEntityFramework) 如果你想自己玩。)

**微软。EntityFrameworkCore . SqlServer**引入 EF 核心运行时以及使用 SQL Server 所需组件的主包。

**微软。EntityFrameworkCore . InMemory**提供了 in memory 支持，允许我们像模仿馅饼一样简单。但是，应该注意的是，它不强制关系约束。因为到目前为止它最流行的场景是测试，这不是问题，因为测试的全部目的是验证事情。即便如此，快速(尽管不稳定)的内存数据库的能力还是存在的。想象力是唯一真正的限制。

**微软。EntityFrameworkCore . SQL server . design****微软。EntityFrameworkCore.Tools** 这些是对现有数据库进行逆向工程所必需的，这是我的首选方法。我更喜欢使用 SQL Server，尽管它很丑。能够定义您希望您的数据库是什么(re:让数据库是一个数据库)并让运行时吸收它可以处理的所有细节是确保您从代码优先实现中获得最大收益的快速方法。新版 EF 出来了？再次导入。但是，这就像在商店买一套衣服。肯定能行。它甚至会很好看，但它需要一些剪裁，袖扣是分开的。在实际的产品中，记住偏旁是你的朋友。

**系统。反应式**提供最独特的功能是。净反应延伸。这些可以以各种不同的方式使用，但是将用于演示 ETL 功能。

Z . entity framework . plus . EF coreEF 仍然缺少在数据存储中发布命令的声明性动作。这并不坏，因为不可知论者需要一定的权衡。然而，EF 是可扩展的，这是像这样的产品存在的原因。这个产品让我们拥有更新和删除功能，而根本不需要与实体交互。Z 库是可靠的，但是我确实对他们的商业批量插入产品的定价有问题。我在这里展示的内容将让您了解他们的批量插入产品所做的大部分事情，尽管可能没有那么完美。毕竟，这是一个免费的博客。

您可以用下面的代码行安装它们:

> “微软。EntityFrameworkCore.SqlServer "，"微软。EntityFrameworkCore.InMemory”，“微软。EntityFrameworkCore . SQL server . design "，"微软。EntityFrameworkCore.Tools "，"系统。Reactive "，" Z . entity framework . plus . efcore " | foreach { Install-Package $ _ }

不幸的是。Net 核心包意味着所有的包将膨胀到 70 个，包括所有的依赖包。为了匹配我将使用的数据库，运行脚本 [EfStore.create.sql](https://github.com/hoagsie/BetterEntityFramework/blob/master/EfStore.create.sql) 。这个条目的重点不是讨论模式方面，而是实体框架如何存在于应用程序中，以及我们如何与它交互而不会成为一个蹩脚的噩梦。唉，我非常讨厌两个表数据库“示例”。

我用来导入 EfStore 数据库的命令如下:

> scaffold-db context " Server =(local)；Database = EfStoreTrusted _ Connection = True 微软。SqlServer -OutputDir StoreData

由于 partial*是*我们的朋友，所以让 partial 将上下文的一部分作为{ context _ name } . customizations . cs。因为我们这样做，我们应该注意上下文中预先生成的 OnConfiguring 方法。你可以删除这个。

[](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/StoreData/EfStoreContext.Customizations.cs) [## hoagsie/BetterEntityFramework

### BetterEntityFramework——一些关于如何更干净地使用实体框架的例子

github.com](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/StoreData/EfStoreContext.Customizations.cs) 

## 1)隔离

我完全同意我们不应该在代码中添加 DbContext 引用的观点。为什么有这个必要？然而，我仍然袖手旁观我的声明，存储库模式是一个谎言的宝座。它走得太远，削弱了实体框架的潜力。

主要联系点可以采取多种形式。在我的例子中，因为我只使用 SQL Server 存储，所以我将其命名为 **DataService** 。在处理许多提供者的应用程序中，它可能成为一个工厂或某种查找工具，知道如何向调用者公开 DbContext。

[](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/DataService.cs) [## hoagsie/BetterEntityFramework

### BetterEntityFramework——一些关于如何更干净地使用实体框架的例子

github.com](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/DataService.cs) 

正如可以包装 DbContext 一样，也可以包装 DbContextOptionsBuilder。我称之为**数据选项构建器**。是的，我做了一些隐式的操作符重载，让裸露的 DbContextOptionsBuilder 适合 DataService 的方法，而不需要做显式的造型，也不会让它看起来很奇怪，但是它很适合概念演示的目的。在生产应用程序中，您应该将它放在服务调用的工厂中，或者如果您想要反转依赖关系，可以将它传入。

[](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/DataOptionsBuilder.cs) [## hoagsie/BetterEntityFramework

### BetterEntityFramework——一些关于如何更干净地使用实体框架的例子

github.com](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/DataOptionsBuilder.cs) 

在这些之后，您就有了应用程序和实体框架之间所需的所有分层。你可以完全去掉它，只要合同保持不变，你就不需要对你的应用程序代码做任何修改。这也说明了为什么我在之前的博客中表现出愤怒。

你不需要这些千篇一律的东西像雷区一样点缀你的应用程序，来暴露你想暴露的每一个小方面。为你自己节省一些工作，并利用这个框架。当然，有些人不喜欢把自己过多地束缚在一个特定的框架上。Protip:当你在一个. net 控制台应用程序中写下“hello，world”时，你已经输了五次了。相反，如果您希望能够从一个框架复制/粘贴到另一个框架(几乎不做任何修改),那么您没有理由不这样做。无论如何，这样的事情需要惊人的工作量，即使你只使用原语和数组编码。诸如此类的事情也让我对 Visual Studio WCF 代理生成感到愤怒。滚动一个手动代理工厂很容易，让你更忠实，更一致，更不用说比六个文件 Visual Studio plops 到你的解决方案轻得多。我跑题了。

## 2)延伸

你可能会觉得上面说的很棒。你有实体框架，它不会像你那粗鲁的朋友一样乱扔你的代码，当他来访时，他从来不收拾干净。然而，它对我们所需要的东西毫无用处，也不符合我在上一篇文章中提出的主张。为此，我们需要两样东西来满足我提出的标准。

这其中的第一部分， **DbContextExtensions** ，在 ClearCache 方法中交付了一个生活质量组件，并展示了破坏“EF 不能”声明的马，BulkInsert。 **ClearCache** 方法非常简单。一个是选择器，这样你可以选择去掉什么。另一个清除一切。

[](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/Extensions/DbContextExtensions.cs) [## hoagsie/BetterEntityFramework

### BetterEntityFramework——一些关于如何更干净地使用实体框架的例子

github.com](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/Extensions/DbContextExtensions.cs) 

看着代码，你可能会说“但是 hoagsie，兄弟，你说 IEnumerable 不好。”不，我没有。我说要和它断绝关系。我们不想带着一堆可能有数千或数百万个项目的物品到处走。我们仍然可以延迟枚举，直到我们真正想用它做些什么，这就是 IEnumerable 的目的。此外，这强化了我所提倡的一个重要的隐含范式:IQueryable 用于获取数据并在执行过程中处理数据，而 IEnumerable 用于管理我们已经拥有的数据，并且可以以简单的方式使用这些数据。

"但是豪格西，兄弟，回购不就是这样吗？"不，他们没有。正如我在上一篇文章中所阐述的，存储库对您提供给应用程序的数据施加了限制，并且抵制更改。此外，虽然今天您需要选择一些产品，但在另一个模块中对它们进行一些处理，如果初级开发人员需要在四个月后重构该模块以跳过前 30 个，无论出于什么原因，IQueryable 都可以做到这一点。除非您返回并更改调用存储库的部分，否则存储库不能。

正如我所支持的, **BulkInsert** 方法与 SqlBulkCopy 一起工作。这不应该是一个惊喜。可能令人惊讶的是我为它设置的专用 SqlConnection。在我的测试中，批量复制对象一直关闭底层连接，导致问题。因此，我们可以创建另一个连接。它仍然是流数据，所以你仍然可以通过它铲吨的数据，你不会创建几个连接。你有两个，仅此而已。

BulkInsert 的精华就是[反应式延伸](https://msdn.microsoft.com/en-us/library/hh242985%28v=vs.103%29.aspx)给我们的。它们提供了一种“只需几行”的方式来创建对流经该方法的对象的强大的、富有表现力的转换。你必须注意你在转变中做了什么，否则你仍然会把你的表现扔进垃圾桶。尽管如此，我还是见过这种如入无人之境的啃唱片。将使用此功能的应用程序放在[网络](https://hackernoon.com/tagged/network)上靠近数据库的地方，以减少延迟，并观察它的尖叫。

我还提供了一个快速而肮脏的 **BulkHelper** 来提供一种挂钩到 SqlBulkCopy 对象提供的更新通知的方法。

[](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/Helpers/BulkHelper.cs) [## hoagsie/BetterEntityFramework

### BetterEntityFramework——一些关于如何更干净地使用实体框架的例子

github.com](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/Helpers/BulkHelper.cs) 

最后，将实现联系在一起的部分是 **QueryableDataReader** 。这使得批量复制对象的 IDataReader 重载可以理解我们的 IQueryables，并可以随后处理我们抛出的任何内容。

[](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/DataReaders/QueryableDataReader.cs) [## hoagsie/BetterEntityFramework

### BetterEntityFramework——一些关于如何更干净地使用实体框架的例子

github.com](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/DataReaders/QueryableDataReader.cs) 

最后但同样重要的是，我们有我们的 **IQueryableExtensions** 。这些利用 Z lib 优雅地包装

> (表情<func bool="">>)哪里。更新(表情<func tentity="">>)</func></func>

和

> Where(表情<func bool="">>)。删除()</func>

包装的特点，而不背叛它来自其他地方。

[](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/Extensions/IQueryableExtensions.cs) [## hoagsie/BetterEntityFramework

### BetterEntityFramework——一些关于如何更干净地使用实体框架的例子

github.com](https://github.com/hoagsie/BetterEntityFramework/blob/master/BetterEntityFramework/Extensions/IQueryableExtensions.cs) 

有了这些扩展，代码仍然可以几乎完全不知道实体框架和 Z lib。让我们看看 Program.cs 中的 using 块。

> 使用系统。Linq
> 使用系统。反应性；
> 使用 BetterEntityFramework。扩展；
> 使用 BetterEntityFramework。StoreData
> 使用微软。EntityFrameworkCore
> 使用数据= BetterEntityFramework。数据服务；
> 使用 IsolationLevel = System。Data . IsolationLevel

*只有*个理由“使用微软。EntityFrameworkCore”这是因为我调用了构建器选项来展示我们如何与它们交互。将它放在工厂或默认的提供者实现中会让代码的应用程序方面 100%不知道发生了什么。请注意，这不同于不假装实体框架不是依赖关系。确实是。仓库也是如此。但是，我们不需要考虑它，可以让其他组件为我们管理这些问题。这就是它的意义所在。

我没有创建一个“工作”演示，而是选择了一个类似例子的食谱，这样你就可以挑选和调整组件来做你想做的任何事情，而不需要刮出我的场景代码。

## 长寿和繁荣

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)