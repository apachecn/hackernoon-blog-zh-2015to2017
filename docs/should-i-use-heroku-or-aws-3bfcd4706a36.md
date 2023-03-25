# 应该用 Heroku 还是 AWS？

> 原文：<https://medium.com/hackernoon/should-i-use-heroku-or-aws-3bfcd4706a36>

## 那么托管自己的机器呢？

每个运行 web 项目的人，无论是纯粹的 API 还是成熟的 web 应用程序，都将不得不托管他们的服务。在(仔细地)选择了你的软件栈之后(Ruby on Rails vs .[node . js](https://hackernoon.com/tagged/nodejs)vs . Django vs . etc。)你需要把你制作精美的应用程序公之于众。目前有多种选择，虽然这个概述并不完整，但我希望它能让您对一些选择有一个初步的了解。

我将简要介绍一下 [Heroku](https://www.heroku.com/) ，它提供平台即服务，亚马逊的 [AWS](http://aws.amazon.com/) ，提供基础设施即服务，并托管您自己的机器。这个列表既不完整，也不代表一个比另一个具体的建议，考虑它是基于我自己的经验的个人意见。

## IaaS 与 PaaS

在任何人跳出来说，这是比较苹果和橘子之前，是的，你是对的。基础设施即服务(Infrastructure as a Service)，简称 IaaS，是一种云计算，其中提供商(在本例中为 [AWS](http://aws.amazon.com/) )通过互联网提供计算资源(虚拟化)。就当是在某个地方租了一台机器，不用管电线，电，硬件等等。

平台即服务，例如 heroku 提供的 PaaS，也可以为您处理基础设施运营。因此，您不需要担心构建基础设施的不同组件(负载平衡器、机器等)。)但是通常可以通过简单的 git 推送来部署整个应用程序。

## 亚马逊 AWS vs. Heroku

因此，如果你愿意将部分服务器基础设施外包给另一家公司，请继续阅读。如果没有，跳到最后，如果你运行你自己的服务器，检查我的建议。

## 自动警报系统

如前所述， [AWS](http://aws.amazon.com/) 要求您设置负载平衡器，在 EC2 实例上安装正确的软件栈，配置数据库，等等。您还应该实现和配置您的部署过程，例如通过使用类似 [capistrano](https://github.com/capistrano/capistrano) 的东西。

为了维护实例上的软件，我强烈建议不要在实例上手动安装软件，这可能会导致不同机器上的不同配置，等等。我的建议是使用适当的供应工具链，像汉堡 [dynport](http://www.dynport.de/) 的伟大团队中的[木偶](http://puppetlabs.com/puppet/puppet-open-source)、[主厨](https://www.chef.io/chef/)或[乌尔克纳尔](http://urknall.dynport.de/)。Urknall 是一个基于 Go 的“聪明开发者的自以为是的供应工具”。此外(也是 AWS cloud architects 推荐的)，你应该认真看看 [AWS Cloudformation](http://aws.amazon.com/cloudformation/) 。Cloudformation 允许您编写模板来描述您在 AWS 的基础设施。因此，使用 Cloudformation，您可以用代码提供基础设施的适当文档，并确保您可以使用与最初完全相同的配置来启动它。

运行 AWS 的成本比在 heroku 上要低，因为你“只”为基础设施付费。根据实例类型的不同，t2.micro 实例的价格最低为每小时 0.014 美元，生产就绪 t2.medium 实例的价格最低为每小时 0.056 美元。

## 赫罗库

Heroku 消除了安装软件、维护软件、监控软件所需更新、设置部署等所有痛苦。听起来像魔术？第一次用的时候感觉有点像。难怪很多教程都建议使用 heroku。我还在我的 web 应用程序开发讲座中推荐它。它只是工作，并允许您快速地让您的 web 应用程序在线，让世界看到并使用它。此外，他们还提供了一个免费层，这使得测试您的应用程序变得非常棒。

需要更高的性能？需要更大的 Postgres 数据库？想要将 Redis 添加到您的堆栈中吗？大多数软件需要你设置一个新的实例，供应它，配置它，等等。只需点击或通过命令行，就可以在 heroku 上添加 on AWS。扩展你的性能只是添加更多的所谓的 dynos。

那为什么大家都不使用 heroku 呢？一如既往，有多种原因。一个是成本。当您获得所有平台和软件即服务时，您也需要为此付费。因此，您应该计算基础设施增长时的额外成本，并将其与设置 AWS 基础设施的成本进行比较。

另外，有些人可能会对将自己的代码交给 heroku，并在相同的情况下与他人共享感到不舒服(与 heroku 的 dyno 隔离隔离)。

## 经营自己的？

并非每个应用都适合在云提供商处运行。有时可能会有法律问题或要求，迫使您运行自己的数据中心、托管自己的服务器等。乍一看，它可能更便宜，但是您必须考虑额外的机器管理工作。那个用了两年的硬盘在周日早上停止工作了？您负责启动并运行系统，希望能够找到一个替换磁盘，然后集成到 RAID 中，等等。有了 AWS 或 heroku，这样的事情就完全隐藏起来了，你可以在同一个周日早上和家人一起享用早餐。

## 那又怎样？

使用 heroku:

*   对于你想向世界展示的小型个人项目
*   作为一个自由职业者，展示你的作品作为工作应用程序(大多数时候免费的 dyno 计划就足够了)
*   如果你是一家代理公司，需要专注于交付一个(网络)应用程序，并向客户承诺全面负责运营，不需要专家参与
*   如果您需要快速入门，并且不希望有管理系统的开销，也不需要这样做的充分自由
*   如果你是一个学生/学习一个新的框架，只是想玩一玩

使用 AWS:

*   如果你是一个机构，你可以负担得起有一个 devops 的专家
*   对于具有 SLA 的生产负载
*   如果您需要对基础架构进行额外控制，如软件版本、特定补丁等。
*   如果你能在 devops 上花些时间

亚马逊 AWS 和 Heroku 都签署了美国/欧盟数据隐私安全港协议。在这里你可以找到亚马逊的隐私声明，这里是 T2 的隐私声明。

使用托管服务器或您自己的基础架构:

*   如果你能在开发和管理上花费大量时间
*   如果法律要求你这样做
*   如果您需要从硬件中获得最后一点性能
*   如果你想在周日早上坐在服务器机房里，扯着头发

*注:本帖于 2015 年 2 月 3 日编辑，以反映读者的评论* [*丹·彼得森*](https://twitter.com/dpiddee) *和* [*克里斯蒂安·威耶*](https://twitter.com/chrisweyer) *。感谢您的参与！*

延伸阅读:

[](https://stackoverflow.com/questions/9802259/why-do-people-use-heroku-when-aws-is-present-whats-distinguishing-about-heroku) [## AWS 在场的情况下，为什么还有人用 Heroku？Heroku 和 AWS 有什么区别？

### 我是一名初学 RoR 的程序员，打算用 Heroku 部署我的应用程序。我的其他顾问朋友说…

stackoverflow.com](https://stackoverflow.com/questions/9802259/why-do-people-use-heroku-when-aws-is-present-whats-distinguishing-about-heroku)