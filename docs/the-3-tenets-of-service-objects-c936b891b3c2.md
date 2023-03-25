# Ruby on Rails 中服务对象的三个原则

> 原文：<https://medium.com/hackernoon/the-3-tenets-of-service-objects-c936b891b3c2>

![](img/aa320538f8a141ceb2ad215a623a8cf9.png)

服务对象正在成为精简控制器和模型的工具带中的一个主要部分。欢迎来到脂肪服务的世界[文件夹]！对那些可能不知道服务对象是什么的人进行快速复习…

服务对象是一个 PORO(普通的老式 Ruby 对象)，意在将业务对象分解成可管理的类和方法。在不深入讨论可靠的 OOP 原则以及它们如何应用于 Ruby 的情况下，一个服务对象应该有一个单一的职责，易于测试，以及其他一些我将进一步深入讨论的方面。

这可能背离了过去的 Rails 设计原则。以前，您可能会直接向您的“胖”模型添加另一种方法，或者最近向 mixin 或模型关注点添加逻辑。虽然这可能有助于代码分离，但我发现它并没有使服务对象可以执行的活动变得简单、有组织和容易发现。此外，当它们被“混合”到模型类中时，模型仍然会变大，并将所有东西都绑定到模型类中。

注意:这篇文章是对我即将出版的书[在 Rails 6](https://buildasaasappinrails.com) 中构建一个 SaaS 应用的服务对象章节的评论。这本书指导你从卑微的开始，通过部署一个应用到生产。这本书现在已经开始预售了，你可以马上拿到免费的一章！

另外，我的新项目[的测试版已经发布了。如果你失去了对拉取请求的跟踪，让旧的请求留在身边，或者只是喜欢一个通过多个服务(Github、Gitlab 和 Bitbucket)聚集这些请求的仪表板，](https://pullmanager.com)[查看一下](https://pullmanager.com)。

# 所以…一个服务对象？

在深入研究服务对象之前，最好先讨论一下服务对象的名称。事实上，我最近在 Reddit、Twitter 等网站上越来越多地看到这种情况。

这里有几个选择。其中最大的一个是使用名称“ **Operations** ”，这个名称取自 TrailBlazer 框架(位于 Rails 框架之上)。

我见过的另一种方法是称它们为对象，并从类名、文件夹等中完全删除服务。

就我个人而言，我不介意服务对象的名称，我觉得它描述了类/对象的目的是执行一个动作或服务的事实。然而，操作紧随其后，因为它以类似的方式描述服务对象。尽管如此，不打破当前服务对象的一些最佳实践是坚持“服务”的一个足够好的理由。

# 原则 1:尽最大努力只拥有一个公共方法。

就我个人而言，我觉得这与课程的 SRP 密切相关。如果类/对象只负责一件事，那么应该只有一个公共方法使对象提供单一的服务操作。一个公共方法约束最常用的方法是`.call`。

现在，虽然这是服务对象最佳实践的最一致的方面之一，但是服务对象实例化的方法可以分为两大类。

首先:

```
#services/thing_service.rb
class ThingService

  def initialize(param1)
    @param1 = param1
  end def call
    private_method
  end private
  attr_reader :param1...
end### and calling by ThingService.new(param1).call
```

或者二:

```
#services/thing_service.rb
class ThingService
    def self.call(param1)
    new(param1).call
  end

  def call
    private_method
  end private def initialize(param1)
    @param1 = param1
  end attr_reader :param1...
end### and calling by ThingService.call(param1)
```

就个人而言，我更倾向于第一种方式，因为它符合经验和大多数其他 Ruby 开发人员创建 POROs 的经验。然而，第二种方法在调用时会更简洁，可读性更强。

这里的主要思想是，您正在努力将您的对象限制为一个公共方法，该方法将在整个应用程序(主要是控制器)中被调用。

一个或最少的公共方法的额外好处是，您将更容易重构服务对象，并且通常不需要在整个应用程序中更改太多的服务对象调用。

# 原则 2:管理依赖关系可能很难。私下做，最好。

在最简单的服务对象之外，几乎不可能不包含另一个依赖的服务对象。那么包含它们的最佳方式是什么呢？

可能想到的一种方法是实例化对象并将它们作为参数包含进来。这种方法虽然可行，但很可能会导致重构问题和在许多地方更改方法签名。

另一种方法是在服务对象的公共方法或主私有方法(如果有的话)中实例化依赖服务。虽然这更好，但是它开始分解一个方法的 SRP。它使该方法直接负责并了解依赖关系。

根据我的经验，这里最好的方法是创建一个私有方法，该方法将返回注入依赖的一个记忆版本。这里有一个例子:

```
#services/thing_service.rb
class ThingService

  def initialize(param1)
    @param1 = param1
  end def call
    result = other_service.call
    private_method(result)
  end private
  attr_reader :param1 def other_service
    @other_service ||= OtherService.new(param1)
  end...
end
```

虽然与其他依赖注入方法相比，这并不是一个巨大的变化，但这可以使您在重构时更容易地更改公共方法，因为它不再直接负责另一个服务的实例化。

# 原则 3 返回有意义的东西。一个物体。

虽然您的直觉是返回一个布尔值作为服务对象的结果，但这可能不是您的最佳选择。是的，Rails 中的默认习语是运行 ActiveRecord 更新/创建的条件关闭，从长远来看，一个丰富的响应对象将会带来好处。

这么想吧。一个服务对象不仅仅有两种结果。1.成功。2.失败。3.未处理的异常。仅仅在布尔响应中处理这样的结果会对您的服务造成损害。

当更深入地研究这些结果时，就更有理由使用响应对象了。让我们以*成功*为例。首先，一个结果对象可以包含一个`success?`谓词方法来处理控制器响应的条件分支，等等。此外，因为它是您自己的响应对象，所以您可以随意命名成功/失败方法。如果`signed_up?` 更有意义，那就像在你的对象中定义它一样简单。

服务对象也可能有一组甚至多组数据作为响应。假设您正在创建一个信用卡处理服务。您可能需要记录来自处理器的多个不同的令牌，或者要向用户显示的其他数据集。如果没有某种响应对象，这可能会很麻烦。

现在让我们看看服务对象失败和异常的后两种结果。乍一看，似乎很简单。只需将任何一个结果设为 falsey，并强制某种错误/失败浏览器响应。然而，这可能无法向最终用户或开发人员提供完整的意图或上下文。假设由于表单中缺少数据而触发了一个异常。该异常可以被挽救，通过结果对象设置或传递消息，并用于在浏览器中正确地通知最终用户。现在，有了来自响应对象的一点点信息，您就有了一个更有意义的请求/响应周期。比你仅仅从一个`true`或者`false`那里得到的还要多。

那么，如何使用响应对象呢？最简单的用法通常是使用 Ruby 库中的`Struct`或`OpenStruct`。

```
#services/thing_service.rb
class ThingService

  def initialize(param1)
    @param1 = param1
  end def call
    private_method
  end private
  attr_reader :param1 def private_method
    things = do_something(param1)
  rescue SomeSpecificException => exception
    OpenStruct(success?: false, things: nil, error: exception.message)
  else
    OpenStruct(success?: true, things: thingserror: nil)
  end
end
```

虽然这是一个非常模糊的例子，但您应该能够明白这里的意思。除了结构之外，您甚至可以创建自己的响应类/对象，或者使用一些漂亮的宝石来很好地包装响应/结果对象功能。如果我走那条路线，我的一个 go to gems 是 [Github::DS gem](https://github.com/github/github-ds/) ，它有一个 Github::Result 对象。

# 还有什么？

虽然这些是我认为服务对象的三个主要必须做的部分，但是还可以有其他方法来使它们变得更好。

其中之一是尝试将类方法限制到绝对最小，并允许服务对象作为实例化对象工作。这看起来很简单，甚至可能消耗更多的内存，但是在允许服务对象作为实例工作时会有更大的灵活性。这可能会让你以后不再头疼。

另一个技巧可能是用非常功能化的方法来考虑服务对象。在函数式编程中，你把方法看做更多的输入/输出方法。参数进去，值出来。在“服务对象领域”中，这应该解释为，您应该将简单的参数作为响应对象的一部分传入，并将简单的值作为响应对象的一部分传出。它不是纯功能性的，但是它应该有助于降低您的服务对象开销。

你有什么一直遵循的服务对象的信条吗？请分享！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)