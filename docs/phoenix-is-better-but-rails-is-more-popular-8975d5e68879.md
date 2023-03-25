# Phoenix 更好，但 Rails 更受欢迎

> 原文：<https://medium.com/hackernoon/phoenix-is-better-but-rails-is-more-popular-8975d5e68879>

我靠为 [Ruby on Rails](http://rubyonrails.org) 和 [Phoenix](http://www.phoenixframework.org) 提供咨询、培训和撰写[书籍](http://amzn.to/2qgoHKH)为生(提示:如果你想雇佣我，请发邮件至 sw@wintermeyer-consulting.de)。我有 50%的时间是作为消防员被雇佣来修复一个现有的项目。在另外 50%的时间里，我受雇帮助在绿地上创建一个 web 应用程序。在这种情况下，许多客户会问他们的新项目是应该使用 Rail 还是 Phoenix。我生活在两个世界，并想分享一些光到这个不平凡的决定。

# Phoenix 不仅仅是用仙丹编写的更快的 Rails

大多数开发人员认为 Phoenix 是 Rails 在函数式编程语言中的重新实现。别搞错了！它不是。它的创作者 Chris McCord 最初确实回收了一些 Rails 的想法，但只是他喜欢的那些。对于许多技术问题，Phoenix 社区提出了不同的解决方案。很多次都有更好的。

# 光束是怎么回事？

Ruby 是一种面向对象的编程语言。这是一种解释语言，由松本幸宏创造，他在发明这种语言时考虑到了效率和乐趣。另一方面，Elixir 是一种函数式编程语言(由 [José Valim](https://medium.com/u/8a14cd1c1c04?source=post_page-----8975d5e68879--------------------------------) 创建)，运行在 [Erlang](https://en.wikipedia.org/wiki/Erlang_(programming_language)) 虚拟机(BEAM)上。它被编译成 BEAM 的字节码。BEAM 作为一个单独的操作系统进程运行，但是它本身就是一个世界。

BEAM 在服务器上全天候运行。Ruby (on Rails)进程只有在 web 服务器启动它来响应请求时才会运行。因此，如果你想每小时做一件事，你必须使用 cronjob 在 Rails 系统上触发它。在凤凰系统中，这将由光束中的一个小小的药剂程序来处理。

从 Phoenix 的角度来看，将消息从服务器推送到客户端的 Rails 方法 action cable([https://medium . com/ruby inside/action-cable-hello-world-with-Rails-5-1-EFC 475 b 0208 b](/rubyinside/action-cable-hello-world-with-rails-5-1-efc475b0208b))非常慢，容易出错，而且扩展性不好。它需要一个 redis 服务器来解决这个问题。在 Phoenix 项目中，这一切都由光束来完成。

如果你正在运行一个非常大的 web 应用程序，BEAM 有一个非常有趣的锦囊妙计:它可以非常容易地在许多服务器上扩展。对于 Phoenix 项目来说，扩展并不是一个令人头疼的问题。谈到规模和速度:我有没有提到 WhatsApp 服务器也运行在 BEAM 上？

# 函数式与面向对象式

有时候灵药看起来有点像红宝石。我认为这是误导，因为 Ruby 是面向对象的，而 Elixir 是函数式的。完全不同的世界！如果你是一个经验丰富的 Ruby 开发人员，你需要一段时间来感受长生不老药。

但是当我多年前爱上鲁比时，我也爱上了长生不老药。我工作中最糟糕的部分就是在这两个世界之间切换。它们中的每一个本身都完全有意义，而且非常美丽。但是你不想编写 Ruby 和 Elixir 代码。挑一个坚持下去！

# ActiveRecord 与 Ecto

[ActiveRecord](http://guides.rubyonrails.org/active_record_basics.html) 是 Rails 世界中处理数据库的首选工具。在凤凰城，Ecto 是首选工具。两者都提供验证，但在其他方面有很大不同。ActiveRecord 感觉像一个 Ruby 对象。如果你了解 Ruby，用起来感觉刚刚好。你必须很少考虑它，你根本不需要理解 SQL 来使用它。另一方面，使用 Ecto 需要对 SQL 有基本的了解。埃克托的边缘有点粗糙。

在我看来 ActiveRecord 是 Rails 的秘制酱。Rails 因为 ActiveRecord 而有趣。我看不出埃克托和菲尼克斯之间有什么密切关系。埃克托是伟大的，但菲尼克斯没有埃克托依然伟大。Rails 不会没有 ActiveRecord。

# 宝石与包装

gem 是一个第三方软件，可以很容易地集成到您的 Rails 项目中。大多数 Rails 开发人员都有一套他们喜欢在所有项目中使用的 gem(例如 [acts_as_list](https://github.com/swanandp/acts_as_list) 或[device](https://github.com/plataformatec/devise))。宝石就像厨师的盐和胡椒。看看 https://www.ruby-toolbox.com 的[就能感受到宝石生态系统的巨大规模。](https://www.ruby-toolbox.com)

Gems 是集成功能的快捷方式。gems 的主要问题是软件升级。假设您有一个 Rails 4.2 应用程序，并希望升级到 Rails 5.1。那么你必须确保你正在使用的 gems 仍然可以在 Rails 5.1 上工作。你使用的宝石越多，你遇到问题的几率就越高。通常维护者对他/她的创作失去兴趣，或者不涉及您的具体情况。我见过无数的客户项目都有这样的问题。一切都停滞不前，因为 2 年前，一颗宝石被包括在内，但它不再被维护。

如今，我更喜欢自己编写一个功能，而不是使用 gem。但这是一条狭窄的路线。

在仙丹大陆，这些第三方软件被称为软件包。包装生态系统远没有宝石生态系统那么大。看看 https://elixir.libhunt.com/categories 的[和](https://elixir.libhunt.com/categories)就知道了。因为 Phoenix 和 Ecto 工作包比 gems 更经得起未来的考验。但是你得在几年后再问我这个问题。

# 速度

这个问题可以很快很轻松地回答:凤凰号很快。必须比 Rails 快。

但是说实话:这种速度是很少需要的。因此，我与 Phoenix 建立了商业网络 [https://www.vutuv.de](https://www.vutuv.de) ，但大多数项目不需要这种极限速度。大多数情况下，只需再添加一台服务器或添加一些缓存就足够了。

# 发展速度

用 Phoenix 和 Rails 编写一个 web 应用程序花费的时间差不多。它们中的任何一个都没有开发速度优势。

当然，当你第一次从铁路换到凤凰城时，你会经历减速。就像你会反过来一样。

# 稳定性

我花了很长时间才相信/理解它，但横梁超级稳定。Erlang 已经有 20 多年的历史了，Erlang 的开发者竭尽全力确保它保持稳定。

Ruby 不是不稳定的。但是我在从一个 Ruby 版本升级到另一个版本时遇到了问题(参见 gem 问题)。我从未遇到过长生不老药的升级问题。

# 零停机和热部署

对于大多数 web 应用程序来说，软件升级期间的短暂停机根本不是问题。大多数公司在这个问题上投入了太多的钱，而 1-2 分钟的停机时间根本不会对他们造成伤害。但有时您确实需要零停机部署。

对于 Rails 来说，零宕机可能是一个相当大的挑战。这很容易。为此，光束具有内置功能。您可以热替换代码。对我来说，这是梦想成真。

全面披露:当升级涉及到数据库迁移时，零停机对双方来说都变得更加复杂。

# 大卫对克里斯

大多数开源软件项目都由或多或少的大型核心团队运营。Ruby on Rails 和 Phoenix Framework 也是如此。但在这些核心团队中，并不是每个人都是平等的。创始人总是有超能力。他们一直在定义方向，他们对公众的可见性很重要。

DHH(大卫·海涅梅尔·汉森)创造了 Rails，从那以后人们就一直在谈论它。他是一个天生的表演家。在我看来，他在过去的 2-3 年里变得有点懒惰。他的铁路主题演讲曾经是令人大开眼界的经历。在他最近的两次主题演讲中，我没有感觉到这一点。

大卫有创造知名度的天赋。他不仅仅是一名软件工程师，还是一名赛车手和一名 T2 摄影师。但是他为一个软件项目或一本书创造知名度的能力([他也写了几本非软件的书](http://amzn.to/2rT05sq))是很难与之相比的。

大卫有傲慢的光环。但我明白。Rails 社区比 Phoenix 社区大得多。大卫不可能像克里斯在精英俱乐部那样和每个人聊天。

并不是说金钱是成功的一个很好的指标，但是大卫因为他的公司 [Basecamp](http://basecamp.com) 而成为了一个两位数的百万富翁，该公司运行着一个 Ruby on Rails 堆栈。

克里斯·麦考德创造了凤凰城，从那以后人们也开始谈论它。Chris 来自 Rails 社区，但无法解决 Rails 的几个问题。所以他用长生不老药发明了一种更好的轮子。你可以感觉到他目前在 Phoenix 的参与程度超过了 David 在 Rails 的参与程度。在会议上，克里斯对新手更友好。他比大卫更脚踏实地。

克里斯在[船坞](https://dockyard.com)工作。

顺便说一句:布莱恩·卡达雷拉(首席执行官)在船坞建立了一个令人印象深刻的凤凰团队。

# 能见度

Rails 比 Phoenix 更容易看到。有更多关于 Rails 的书(无耻之徒:我的新 [Rails 5.1 书](http://amzn.to/2qZh5iJ))，更多的截屏(这一切都是从著名的“15 分钟博客”截屏开始的)和更多的培训。

我最喜欢的指标？[彼得·库珀](https://medium.com/u/179d3d289c60?source=post_page-----8975d5e68879--------------------------------)会写一封关于特定话题的新闻信吗？他对红宝石([http://rubyweekly.com](http://rubyweekly.com))有效，但对仙丹无效？).

但是你正在读这篇文章。所以你听说过这两个项目。它讲述了 Rails 和 Phoenix 的可见性。

# 这两种框架的主要问题是

我所有的客户都很难找到好的开发人员。凤凰城和铁路。凤凰城可能更难一点。

这是一个大问题。找不到好的开发者，一个完美的框架有什么用？！使用 PHP 的顶级 PHP 开发人员可能比使用 Rails 的普通 Ruby 开发人员获得更好的结果。

# Rails 和 Phoenix 哪个更适合你？

在我看来，Phoenix 提供了稍微好一点的技术，但我也认为这不是一个成功项目的关键。重要的是开发人员的数量以及他们对框架的了解程度。因此，当你开始在一个绿色的领域，你必须问自己“我会更容易找到好的轨道或好的凤凰人吗？”。大多数情况下，找到 Rails 人员会更容易。

如果您已经深入到一个现有的 Rails 项目中，那么在迁移到 Phoenix 之前要格外小心。另一边的草总是更绿。我见过非常成功的转变，但很多时候这些转变是由摇滚明星做出的。

如果你有一个喜欢学习新东西的有才华的 Rails 开发团队，那么我的建议是从 Phoenix 开始一个副业项目。在你决定改变你的生计项目之前，先看看它是如何运作的。

在极少数情况下，速度和/或零停机时间是您项目的绊脚石，那么您必须选择 Phoenix。

**重要:没有一个是不好的选择！**

# 无耻的插头

如需 Rails 或凤凰咨询或培训，请发邮件至 sw@wintermeyer-consulting.de。请关注我在 https://twitter.com/wintermeyer[的推特消息](https://twitter.com/wintermeyer)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！