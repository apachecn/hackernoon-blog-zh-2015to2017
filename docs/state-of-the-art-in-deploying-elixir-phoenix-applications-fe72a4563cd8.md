# 部署 Elixir / Phoenix 应用程序的最新技术

> 原文：<https://medium.com/hackernoon/state-of-the-art-in-deploying-elixir-phoenix-applications-fe72a4563cd8>

这是基于在生产环境中运行 3 到 4 个 Phoenix 应用程序超过一年的固执己见的指南。我们最大的应用每天面对大约 14K 付费用户。

**我可能是错的**，在[未来](https://hackernoon.com/tagged/future)，我们可能会在 Kontomatik 做不同的事情。然而，据我所知，截至 2017 年 3 月，这是部署基于 Phoenix Framework 的应用程序的最佳方式。

# 使用 **asdf** 来管理您笔记本电脑上的 Erlang 和 Elixir 版本

假设从一开始就从事多个 Elixir 项目。即使你现在只有一个生产应用程序，你也会想尝试不同版本的应用程序和其他开源项目。底线是，您不可避免地需要在笔记本电脑上轻松管理 Erlang 和 Elixir 版本。

使用 [asdf](https://github.com/asdf-vm/asdf) 版本管理器及其。工具版本文件，为您的每个项目固定特定的 Erlang 和 Elixir 版本。

显然，这些版本必须与您在生产环境中运行的版本相匹配。

根据我的经验，asdf 版本管理器工作得非常好。缺少其他工具(kiex 等)(例如只支持酏剂)。

# 使用 distillery 来打包发布

Erlang/OTP 版本是打包项目的标准方式，使它们可以在 Erlang VM 上运行。

[Distillery](https://github.com/bitwalker/distillery) 是 Elixir 社区为构建版本提供的最佳工具。

了解 Distillery 不涉及服务器或部署是很重要的。最终结果是包含该版本的本地目录(或 gzipped 文件)。

在内部，发布构建是一个复杂的怪兽。大多数时候，Distillery 会完全抽象掉这种复杂性。偶尔，你需要亲自动手，更详细地了解它的某些方面。

Elixir 建立在 31 岁的 Erlang/OTP 生态系统之上，部署是遗留问题的一个方面。

不要试图以“Rails 方式”在服务器上编译 rsync Elixir 源代码。在 Erlang 生态系统中，这是一个死胡同和错误的方法。

# 不要在服务器上安装 Erlang 将它嵌入到发行版中

配置 Distillery 以在发行版中嵌入 Erlang:

```
# rel/config.exs
environment :prod do
  set include_erts: true
  # ...
end
```

不要在服务器上安装 Erlang。

不幸的是，Erlang VM 并不完全向后兼容。为旧虚拟机编译的代码可能会在新虚拟机上崩溃。这真的很烦人，在 JVM 和 CLR 世界里闻所未闻。

实际上，这意味着如果不停机，就无法升级服务器 Erlang 安装。基础设施工作必须与新应用程序版本(为新 Erlang 编译的)的部署同步。这种双向部署耦合感觉像石器时代，在现代技术环境中是不可接受的。

将 Erlang 嵌入到版本中完全解决了这个问题，但是引入了一个新的问题:不同的程序员有不同的、不兼容的 Erlang 版本(Ubuntu LTS vs Arch 滚动版本 vs OS X 等等)。

人们不能简单地将 OS X Erlang 二进制文件(及其依赖项)部署到生产 Ubuntu LTS 服务器上。

我们将在下一章解决这个问题。

# 使用 docker 为生产环境进行本地构建

我不是说要建立码头工人的形象。Docker 只需要具有生产兼容的构建环境，而不考虑您的底层开发机器。

您希望在运行的 docker 容器中使用 Distillery 构建您的 Erlang/OTP 版本。

让您的 docker 文件 100%接近您在生产中使用的文件——与您的。工具版本等。请注意，Erlang 本身的构建会有依赖项的硬编码特定版本(如 Arch 上的“libncursesw.so.6”和 Ubuntu LTS 上的“libncursesw.so.5 ”),这取决于构建它的平台。这就是为什么你需要 docker，即使你团队中的每个人都使用 Linux。

顺便提一下，Docker 也可以用来快速设置开发环境。虽然我不太喜欢这个。它有一些用途，但通常情况下，您不会希望直接在笔记本电脑上开发，而不需要复杂的层，从而获得直接的控制和简单性。此外，像在 IntelliJ 中运行的上下文测试这样的东西不能在容器中使用。更喜欢本地设置。

PS 为什么不用 edeliver？该工具假设在程序员和生产之间有一个部署服务器。这没有什么错，除了对大多数项目来说这是一种过度的杀戮。对于中小型项目，为了速度和简单性，我倾向于避免这种情况，减少移动部件，减少要管理的服务器。也就是说，真正的部署管道非常适合大型团队和项目。

# 不要使用热代码升级

Erlang 允许更新正在运行的应用程序，以获得极大的正常运行时间。

缺点是您需要在应用程序中迁移数据结构。部署不再是显而易见的事情(在持续部署的世界中也应该如此)。突然之间，必须仔细考虑部署——否则，您将面临难以调试的问题。

我们不想那样。既费时又有风险。

**简单重启。**

为了实现更长的正常运行时间，重点关注快速重启，如果有必要，尝试经典的蓝/绿部署或类似模式。

# 使用 ansible(或类似的)来自动化部署

在 Ruby 世界中，Capistrano 提供了几个经过验证的部署流程。您将希望使用您最喜欢的配置工具创建类似的东西。如果你一无所知，选择 [ansible](https://www.ansible.com/) 。

设计部署流程的一些技巧:

*   生产服务器不应该访问您的源代码库
*   作为第一步，将中央存储库克隆/拉至您笔记本电脑上的另一个目录(不是您的项目根目录)；构建将在此目录中单独发生
*   一切都必须从**中央**主(生产)分支——而不是本地程序员分支)构建和部署；这是为了保证没有私有提交被部署到生产中
*   不要压缩版本；相反，rsync 发布目录；这将允许 rsync 只发送实际的更改(快得多)
*   在服务器上使用每个开发人员的发布缓存(更快，避免权限问题)
*   通过在服务器上准备好一切，然后只将符号链接切换到新版本(并强制重启)，使部署接近原子；这在 Rails 世界中被称为“当前”符号链接
*   要运行数据库迁移，请参见:[https://github . com/bit walker/distillery/blob/master/docs/Running % 20 migrations . MD](https://github.com/bitwalker/distillery/blob/master/docs/Running%20Migrations.md)

# 嘿，这太复杂了！

可靠的生产部署从来都不容易，而 Elixir 在此基础上增加了一些意外的复杂性。这可能是去年我们在 [Kontomatik](http://kontomatik.com/) 爱上的**美妙而多产的生态系统**中最糟糕的一面。

好的一面是，仙丹/凤凰应用程序:

*   在内存和 CPU 方面具有非常低的资源使用率；当您在可编程云中托管时，这一点尤为重要(那里的东西很快就会变贵)
*   应用重启速度非常快，最大限度地减少了停机时间
*   生态系统正在迅速成熟，在未来的几个月和几年中，事情将部分简化

遵循建议的最佳实践，以最大限度地减少部署痛苦，并**快速回到仙丹黑客乐趣！**:)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！