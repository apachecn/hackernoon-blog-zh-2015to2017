# 停止花费工程努力来解决你没有的问题

> 原文：<https://medium.com/hackernoon/stop-spending-engineering-effort-solving-problems-you-dont-have-8d18584f4d2a>

这个帖子来自一个令人沮丧的地方。我提前道歉。

如果你正在编写一个 [web 应用](https://hackernoon.com/tagged/web-app)——一个伟大的 web 应用，一个奇妙的 web 应用，有史以来最好的 web 应用——你不需要容器编制器。你需要一个 PaaS。你不需要 5 种不同的方式来推出你的应用程序的新版本。您不需要能够配置和调整您的数据库。*不管理自己的数据库*！你不需要 etcd。对于一切神圣的事物:不要任命执政官。你没有这些问题。这些不是你的问题。不要给自己买这些问题。

这里有一个快速的备忘单可以帮助你避免这些事情:

# 如何打包和编排应用程序更新

看啊。你在写一个网络应用。把你的数据放在别人维护的服务中。如果你用的是亚马逊，RDS 似乎不错。或者，如果你使用 Cloud Foundry(在 BlueMix 或 PCF 或 GCP 或其他地方)，绑定一个数据库服务(mysql 也可以)。

现在，你剩下的是一个简单的，无状态的 web 应用程序。让平台来处理打包和升级应用程序。你的应用没有状态*，是一个简单的网络应用*，所以这很容易。

请不要创建自定义的首次展示策略或(你在开玩笑吗？)一个 ReplicationController，不要建立一个“docker compose file”或者一个里面有 redis 和 mysql 的“pod”，或者一个“operator”来处理升级。不要用厨师或者木偶，除非你在点菜或者在儿童节目中表演(你为什么要读这个？).推送简单的 web 应用。使用一个平台使这变得容易。这应该很容易。

# 如何管理和升级生产数据库

*不管理生产数据库*。使用由其他人提供、扩展和维护的服务。使用别人备份的服务。当这对你来说成为一个真正的[问题](https://hackernoon.com/tagged/problem)时，担心改变这一点(提示:当这是你时，你将*知道*)。同样，任何好的平台都会让这变得容易。

# 如何进行服务发现和管理仲裁

你想让我心脏病发作吗？不要这样做。你在写一个网络应用。你是一家小型企业或大型企业，你正在为神圣的一切编写 web 应用程序，不要在服务发现上投入时间。不要运行 etcd。不要运行动物园管理员。不要跑领事。如果你需要管理一个分布式微服务系统，Consul 是很棒的。你不需要这样做。不要这样。把你该死的网络应用推到一个该死的平台上，好吗？好的。

# 后期脚本:如果您正在编写一个复杂的基于微服务的有状态分布式应用程序，它实际上需要仲裁和服务发现，该怎么办

请编写一个简单的 web 应用程序。过会儿谢谢我。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！