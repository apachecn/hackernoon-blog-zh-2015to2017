# 更简单的拉式请求工作流

> 原文：<https://medium.com/hackernoon/a-lighter-pull-request-workflow-972301e30c5>

开发团队需要优化速度。代码需要快速发送以验证想法，并且需要在反馈到来时立即投入使用。完美的代码解决了错误的问题或者发布得太晚，对公司来说是没有用的。

但是速度需要与质量相平衡，确保质量(以及共享知识和协调)的最有效工具之一是[代码审查](http://goodmath.scientopia.org/2011/07/06/things-everyone-should-do-code-review/)。

可悲的是，大多数现有的代码审查解决方案都过度牺牲了速度。但在过去几年尝试了各种工作流程后，我有了一些感觉非常好的东西。通过在 [GitHub](https://hackernoon.com/tagged/github) 和 Reviewable 之上添加一些脚本，可以平衡代码审查和速度。

# 拉取请求非常多

拉式请求对于大型开源项目来说是一个很好的主意，在这些项目中，提交者要么接受要么拒绝整个概念，速度没有质量重要。不幸的是，GitHub 的主导地位也使它们成为商业软件开发的默认工作流程，尽管它会减慢速度。拉取请求太重了。

*   创建分支、保持分支同步以及分配分支以供审查的步骤太多
*   权重阻止频繁的提交，并鼓励长寿的分支
*   通知和评论界面又吵又笨重

# 北极星

在谷歌，我喜欢的事情之一是我们如何在 master 中工作，在 [one repo](http://cacm.acm.org/magazines/2016/7/204032-why-google-stores-billions-of-lines-of-code-in-a-single-repository/fulltext) 中，几乎没有分支，非常频繁的提交，以及由功能标志保护的新功能。Aaron 在 Chromium 的背景下做了很好的工作。频繁提交到 master(有时称为[基于主干的开发](https://www.thoughtworks.com/insights/blog/enabling-trunk-based-development-deployment-pipelines))有很多好处，也有一些缺点。

*   避免容易出错的大型合并、嘈杂的合并提交和繁重的代码审查
*   阻止开发人员走上任何不好的道路
*   鼓励团队内部和团队之间更紧密的合作
*   更有利于重构和共享所有权
*   通常允许团队更快地移动

然而，Google 依靠定制的工具来完成这项工作，那么人们该怎么做呢？

# 很接近，但是没有雪茄

在 Foursquare，我们使用了脸书的代码审查工具 [Phabricator](https://www.phacility.com/) 。Phab 表面上提供了 Google 风格的敏捷工作流的所有好处——审查针对 master 编写的代码，并在准备好的时候提交给 master。但实际上，这并不令人满意。

*   如果你使用 GitHub 或其他工具做其他事情，会感觉有点混乱
*   配置繁琐，性能下降
*   很难将 arc.sh 或提交挂钩应用到您的工作流中
*   很难确保代码真正得到审查
*   许多现有的 SaaS CI 和代码质量工具只插入拉式请求

# 中间立场

鉴于这一切，我决定在[卷轴](https://scroll.com)为我们的新工程团队找到一个中间地带。我们的解决方案使用一个 shell 脚本、一个 Heroku 上的小型 go 服务器和 [Reviewable](https://reviewable.io) 来做一些事情。

1.  只有在审查代码的时候才分支，而不是在之前。(这有助于确保，在其他事情中，您通过之前使用 rebase 的 pull 与 master 非常接近。)
2.  自动生成和分配拉请求，因此它是一个单一的命令行。(分配评审是确保它发生并快速发生的关键，这与简单的单一命令一起为较小的代码评审创建了一个积极的反馈循环，并提高了团队的整体速度。)
3.  确保轮到编码人员和评审人员时，他们会立即得到通知。(同样，保持高速度以保持飞轮转动。)

# 尝试一下

*   步骤 1 和 2 被 [**shell 脚本**](https://gist.github.com/kushal/656331109dcce1849fd7a18d52ae8ee5) 覆盖。它假设您[生成一个具有 repo 权限的 GitHub 个人令牌](https://github.com/settings/tokens)并将其放入~/.ghtoken 中。通过这种设置，开发人员在 master 中编写代码，当他们准备好进行代码审查时，他们就会运行。/review.sh [reviewer]，脚本与 master 同步，创建分支，提交任何未提交的代码，推它，生成 pull 请求，并分配它。
*   没有严格要求，但我是代码评审的 [Reviewable](https://reviewable.io) 的忠实粉丝，它填补了 GitHub 代码评审工作流程中的所有漏洞。([米哈伊](https://twitter.com/mihai)在注意到[蟑螂](https://github.com/cockroachdb/cockroach)使用它后把这个放到了我的雷达上。)
*   可悲的是，Reviewable 缺乏 Slack 集成，GitHub 的通知也不充分。所以步骤 3 被简单的 [**go 程序**](https://github.com/kushal/slackgitreviews) 所覆盖。它使用两个环境变量进行配置:SLACKURL 是您使用[https://api.slack.com/custom-integrations](https://api.slack.com/custom-integrations)上的按钮创建的 webhook URL，USERMAP 是一个；githublogin、slacklogin 对的分隔列表，例如 johnny24、johnny 鲍勃，懒汉鲍勃。创建 Heroku 实例后，在 Github 中将它的 URL 作为 webhook 添加，用于发布评论和请求事件。当一个拉取请求被创建或检查时，这个脚本会得到通知，并向 Slack 中的正确人员发送一条直接消息。

# 到目前为止，一切顺利

我们刚刚从这个代码工作流开始，但是到目前为止，一切都很好。我们可以审查代码，从围绕拉请求构建的所有工具中受益，并且仍然以几乎与直接提交给 master 相同的敏捷性前进。它并不完美，听听其他人在做什么或者这个解决方案如何为你工作会很有趣。在下面回复你的想法，在 [HN](https://news.ycombinator.com/item?id=13015919) 上，或者在推特上回复 [@krave](https://twitter.com/krave) 。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！