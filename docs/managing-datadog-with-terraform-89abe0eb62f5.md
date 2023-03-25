# 使用 Terraform 管理 Datadog

> 原文：<https://medium.com/hackernoon/managing-datadog-with-terraform-89abe0eb62f5>

![](img/a05f8aa780fca3fd81018c6ce88d452f.png)

手动管理 Datadog 是一个极其繁琐且容易出错的过程。

针对 Datadog 的 API 编写一个自动化工具会有回报，但这是一个巨大的代价。

谢天谢地，有更好的方法。一个更好的方法。那就是[用 Terraform 管理 Datadog](https://hackernoon.com/tagged/managing-datadog) ，免费给你[自动化](https://hackernoon.com/tagged/automation)(以及更多)。

# Terraform 如何帮助您管理 Datadog

*   在几秒钟内对所有 Datadog 组件进行可靠的全面更改。无论您有一台、十台还是一百台显示器，运行 Terraform 所需的时间大约是十秒钟。
*   在 GitHub pull 请求中跟踪和检查对 Datadog 设置的更改，就像进行代码更改一样。使用您喜欢的文本编辑工具编辑您的整个 DD 设置。
*   模板化您的警报消息，并在监视器和时间板上重复使用查询。例如，为您的监视器提供一个一致的页脚(下面显示了如何在代码中实现),其中包括指向您的 Datadog 仪表板和 Kibana 日志的链接，您的待命人员可以立即着手解决问题。
*   在几秒钟内为您的所有显示器和时间板的新环境设置一个新的 Datadog 帐户。例如，您开始使用 Datadog，并为 dev 创建一个单独的帐户，然后为 prod 创建一个单独的帐户，并需要对其进行调配。

# 你如何使用 Terraform？

你用 [HCL](https://www.terraform.io/docs/configuration/syntax.html) (哈希公司的配置语言)编写你的配置。您描述了您想要的组件，例如“我想要一个名为 disk_usage 的 DD monitor，用于度量 system.disk.in_use，在 85%及以上时发出警报”。然后，TF 计算并运行您的 DD 帐户上需要的任何更改，以达到您描述的期望状态。

虽然 HCL 是一种声明性语言，但它是动态的，提供了像变量这样的特性；[计数](https://www.terraform.io/docs/configuration/resources.html#count)；条件；列表和地图等类型；[内置函数](https://www.terraform.io/docs/configuration/interpolation.html#built-in-functions)比如 format——格式化字符串，join——用分隔符连接列表项，element——访问列表项；和模块。因此，除了配置语言之外，你不需要模板语言来弥补后者的不足。例如，当使用由 YAML 配置的工具时，您经常需要使用像 Jinja2 这样的模板语言及其条件和循环来生成工作 YAML。你只需要盐酸。

您可以使用易于使用的 CLI 运行 Terraform。到目前为止，您运行了几个 shell 命令:plan 和 apply。为 TF 做计划，找出你想要的和你在 DD 中已经得到的之间的差异，并计划需要的改变，以达到你想要的状态。并申请在 DD 上运行这些更改并到达那里。

# 使用 TF 管理 DD 的示例

下面的示例展示了如何创建监视器和时间板，使用变量来获得一致的警报消息、查询和阈值。您可以跟随这个[示例 repo](https://github.com/travisjeffery/datadog-terraform-example) 。克隆它并使用试用的 Datadog 帐户运行(如果您没有同名的监视器/时间板，您自己的帐户也可以)。

以下脚本/要点中的内容:

*   该脚本定义了两个监视器，磁盘使用率和 CPU 使用率。它们共享一个页脚用于页面责任和松弛时间提醒。用于监视器的查询也在时间板图中使用，因此您可以直观地确认监视器是否正确，并对照警报阈值检查每个指标的历史记录。
*   然后，该脚本定义了一个时间板，它有两个图表，分别显示磁盘使用情况和 CPU 使用情况。
*   该脚本还定义了变量，以便在一个地方定义常量值，而不是在它们发生变化时将它们分散到各处进行挖掘。

Terraform script managing Datadog.

你可以阅读 Terraform 的数据狗提供商[文档](https://www.terraform.io/docs/providers/datadog/index.html)来了解 API 的细节。

# 用 Terraform！愈早愈好

Terraform 是一种支付复利的工具。你从小额投资开始，比如 Datadog，然后是 Kubernetes 和 AWS。虽然它们可以单独管理，但是通过将它们连接在一起，您可以在一个模块中组成服务的基础结构。现在，您可以轻松启动每个环境的所有基础架构—开发、试运行、生产(甚至每个 IaaS — AWS 和 GCP)。

Terraform 是有史以来最好的操作工具之一。如果你知道它就在那里，却不去利用它，你就是一个受虐狂。即使有了 Datadog，这也是一大福音。

–

请向 [@travisjeffery](https://twitter.com/travisjeffery) 问好。

击中👏如果你觉得这有用，欢迎分享。

感谢阅读。