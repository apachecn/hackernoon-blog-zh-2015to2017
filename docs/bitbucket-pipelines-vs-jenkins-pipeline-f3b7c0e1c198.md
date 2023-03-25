# 比特桶管道 vs 詹金斯管道

> 原文：<https://medium.com/hackernoon/bitbucket-pipelines-vs-jenkins-pipeline-f3b7c0e1c198>

![](img/7af0289047cac649421123d776d31b5d.png)

在我工作的地方，我们是非常典型的亚洲人。JIRA，HipChat，Confluence，当然还有 Bitbucket 都从我们这里得到了很多使用。Atlassian 正在慢慢消灭竹子，我们还没有 CI/CD 设置。因为我们还没有准备好离开亚特兰蒂斯号，这意味着我们正在使用 Bitbucket，这样我们就有了 [Bitbucket 管道](https://bitbucket.org/product/features/pipelines)和 [Jenkins 管道](https://jenkins.io/doc/book/pipeline/)作为我们的主要竞争者。

Bitbucket Pipelines 是一个内置在 Bitbucket 中的 CI/CD 解决方案，非常类似于 [GitLab CI/CD](https://about.gitlab.com/features/gitlab-ci-cd/) 。

Jenkins 是 CI/CD 领域的长期统治者。它有时会得到一些负面的意见，但 Jenkins Pipeline 和 BlueOcean 已经创造了奇迹，使该平台现代化，并使其成为当今 CI/CD 工具广阔世界中的真正竞争者。

两者都采用了 Travis 推广的*管道作为代码*的思路。

# 费用

## 比特桶

[Bitbucket Pipelines 定价](https://bitbucket.org/product/pricing?tab=host-in-the-cloud)基于用户数量和您想要的构建分钟数。例如，我们的团队在 Bitbucket 的标准层。这给了我们每月 500 分钟的通话时间，每个用户 2 美元。[每月 10 美元可以购买](https://bitbucket.org/product/features/pipelines#pricing)额外的时间，为您赢得额外的 1000 分钟。

我的团队大约有 10 个人，我们每月支付 20 美元，共 500 分钟。我们的构建在 Bitbucket 上运行 3 到 5 分钟，平均每月 150 个构建。如果我们在所有 10 个用户中平均一下，每个用户每月只有 15 次构建。我们很可能需要再购买 1000 分钟，这样我们每个月就要花费 30 美元。这使我们每月大约有 450 个构建，或者每个用户每月 45 个构建。这对我们来说看起来好多了。

对于 10 名全职开发人员来说，你每个月至少要花 30 美元。

## 詹金斯

我们是 AWS 商店，所以我将关注 AWS 成本。如果我们看一下官方的 [AWS Jenkins 文档](https://aws.amazon.com/getting-started/projects/setup-jenkins-build-server/)，AWS 估计每月花费 89 美元来托管一个由 40GB EBS 存储支持的 m4.large EC2 实例。

Jenkins 的推荐架构是一个主节点和至少一个代理节点。不鼓励在主服务器上运行构建已经有一段时间了。如果我们再增加一个 m4.large EC2 实例，我们每月增加 74 美元。这使我们每月花费 163 美元。

成本可以通过调整你的实例类型来调整。你可以在一个 t2.medium 上运行 master，这样你的花费可以降到每月 123 美元。如果在非工作时间没有任何开发，也可以在下午 6:00 到早上 7:00 关闭主服务器和代理。如果您运行两个 m4.large 实例，每月将节省 81 美元，那么您每月将节省 82 美元。如果您运行一个 m4.large 和一个 t2.medium，您将每月节省 59 美元，每月 72 美元。

Jenkins 没有构建时间限制，因为您自己托管基础架构。对于一个小团队和一个基本的 Jenkins 设置，你的月薪在 72 美元到 163 美元之间。

从长远来看，这些成本都不是问题。两者都非常便宜。

# 管理

## 比特桶

Bitbucket 几乎不需要任何管理工作。您只需在项目设置中单击一个复选框来启用该功能。如果有东西坏了，亚特拉斯会修好它。也就是说，如果 Pipelines 出现故障(在过去几周内，我已经见过几次了)，你就只能等待 Atlassian 来纠正这个问题。

## 詹金斯

另一方面，詹金斯需要一些管理。有人需要足够的知识来安装它，配置必要的插件，并配置代理。有了 Docker，很多事情都变得非常简单。您可以运行[Jenkins ci/blue ocean](https://hub.docker.com/r/jenkinsci/blueocean/)Docker 映像，并在几分钟内拥有一个正在运行的 Jenkins 实例。它甚至可以与开箱即用的 Bitbucket 一起工作。

虽然设置可能相当简单，但这仍然是一项必须有人付费才能完成的工作。他们至少需要熟悉 AWS、Docker 和设置 Linux 环境。

如果您没有专门的运营团队，那么您的开发人员中至少要有一名能够解决和纠正任何可能出现的问题。如果一个构建代理死了，你团队中的某个人需要修复它。如果构建神秘地失败了，需要有人去调查。如果您需要扩展您的构建代理，需要有人知道如何做这件事。

许多这样的管理任务都很简单。扩展可能很简单，就像为代理提供一个 AMI，创建一个新的 EC2 实例，并将其连接到 Jenkins。 [Jenkins EC2 插件](https://wiki.jenkins.io/display/JENKINS/Amazon+EC2+Plugin)可以根据需要自动完成整个过程。当需求增加时，它能够根据您提供的 AMI 启动新的 EC2 实例，然后在需求下降时自动终止该实例。

在管理方面，Bitbucket 管道是明显的赢家。如果一个年薪 9 万美元的开发人员在 Jenkins 上花了 10%的时间，那么一个月就有 750 美元花在了 CI/CD 上，而没有花在新特性上。这一成本可能会被增加的灵活性抵消，成本甚至可能不会这么高，但当有一个无管理选项可供选择时，很难证明这是合理的。

如果您的组织已经有专门的运营团队或运营人员，那么这个人-小时成本可能更容易证明是合理的。

# 灵活性

## 比特桶

Bitbucket 在允许你做的事情上有相当大的限制。Atlassian 已经为你提供了一套非常坚实的核心功能，但是如果你觉得有必要偏离这条道路，那么你将会有一段艰难的时间。我发现 Bitbucket 管道非常适合那些只需要一个基本的构建-测试-部署-忘记管道的小型项目。如果你需要定制报告，比如静态分析趋势，测试结果，等等，那么 Bitbucket 就没什么用了。

## 詹金斯

Jenkins 在它所能做的事情上非常灵活。有超过 1000 个可以使用的插件,无论你的特定用例是什么，很有可能有人已经解决了它。如果您的特定用例还没有解决，您可以构建自己的插件或[共享库](https://jenkins.io/doc/book/pipeline/shared-libraries/)来解决它。Bitbucket Pieplines 没有办法让你像这样提供自己的功能。

如果您想使用 Jenkins Groovy DSL，那么 Jenkinsfile 构建脚本可以非常简单，或者如果您想走[脚本化管道](https://jenkins.io/doc/book/pipeline/syntax/#scripted-pipeline)路线，那么它可以非常复杂。有了脚本化管道，您可以使用大部分 Groovy 语言来构建您认为合适的管道。

詹金斯是更灵活的选择。真的没有限制，你可以用詹金斯管道完成。

# 创建

## 比特桶

所有构建都使用您选择的一个或多个 Docker 映像运行。您可以为构件的每个步骤使用不同的图像。每个步骤都在一个新的容器中运行。这意味着您需要管理您想要在步骤之间保留的任何工件。例如，您可能不想在每一步都编译代码。

Bitbucket 对此不提供任何控制。您不能直接在虚拟机或专用硬件上运行您的构建。这样做的主要后果是 Windows 构建变得困难。事实上， [Bitbucket 管道现在甚至不支持 Windows 构建](https://confluence.atlassian.com/bitbucket/limitations-of-bitbucket-pipelines-827106051.html)。

## 詹金斯

詹金斯遵循[主/代理](https://en.wikipedia.org/wiki/Master/slave_(technology))架构。有一个 Jenkins Master 负责协调一个到多个代理的构建。主人*也可以*做代理，但不推荐。

*代理*实际上只是一个安装在协调构建的机器上的软件。这可以是位于机架中的物理服务器、云中配置的虚拟机或 Kubernetes 上运行的临时容器。

一种常见的方法是供应仅包含 Docker 和 Git 的虚拟机，并在 Docker 容器中运行所有构建。

Bitbucket 和 Jenkins 都支持在 Docker 中运行的构建。这是一件好事，因为它减轻了 CI/CD 带来的许多麻烦。Bitbucket 把 Docker 强加给你，而 Jenkins 让你选择。如果您需要构建 Windows 应用程序，那么 Bitbucket 管道现在甚至都不是一个选项。

# 通知

## 比特桶

Bitbucket 给团队中的每一个人或者所有关注失败构建的人发邮件。有一个 HipChat 集成，您可以安装它来通知 HipChat 频道构建状态。没有简单的方法来通知 Slack、团队、IRC 等。如果您想要像*这样的特殊通知规则，只给失败的特性分支上的罪犯和失败的主分支上的每个人发电子邮件*，您就不走运了。

## 詹金斯

詹金斯通知是非常可定制的。存在 Slack、团队、HipChat、IRC、电子邮件和几乎任何你能想象到的东西的插件。何时发送通知的规则是非常可定制的。通知本身可以根据您的需要进行定制。如果你想发送 Chuck Norris 所有成功构建的图片，你可以这样做。

Jenkins 允许您挂钩构建的状态，这样您就可以根据构建是失败、通过、不稳定还是在之前失败后通过来调整您的通知。

通知是 Bitbucket 目前真正缺乏的一个领域。目前，詹金斯是这里的赢家。

# 结论

Bitbucket 管道是一种非常精致但有限的体验。它是一个很好的工具，可以让一个小团队快速进入 CI/CD 世界，但是如果你需要更高级的功能，你将很快达到平台的极限。

詹金斯是一个更复杂的平台。设置需要实际安装 Jenkins 软件，连接 Git 存储库托管提供程序以向 Jenkins 发送 Webhooks，并且在开始构建之前提供构建代理。安装完成后，你需要有人来维护平台，并确保它保持正常运行。这应该不是很多工作，但如果你是一个小团队，那么你可能无法抽出一个人花一些时间在詹金斯。这一努力带来了最灵活的 CI/CD 平台。你将很难遇到詹金斯无法满足的需求。

喜欢你读的吗？点击“鼓掌”按钮，关注[黑客月](https://medium.com/u/4a8a924edf41?source=post_page-----f3b7c0e1c198--------------------------------)和[杰瑞德准备好](https://medium.com/u/bc152e4dcd63?source=post_page-----f3b7c0e1c198--------------------------------)获取更多精彩内容！