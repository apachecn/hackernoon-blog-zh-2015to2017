# 尝试建立我自己的无服务器网站的五大经验教训

> 原文：<https://medium.com/hackernoon/top-5-lessons-learned-from-trying-to-build-my-own-serverless-website-d65a168c5e6d>

一个多月前，我开始做一个梦——也就是自我强加的错觉；)

## 在 AWS 上构建一个移动响应的无服务器 web 应用程序，它与社交媒体集成，在事件发生时实时显示事件——为什么不在我在的时候加入 React 呢？

([如果你对“无服务器”是什么意思感到好奇，看看这篇便利的时髦文章](https://read.acloud.guru/serverless-the-future-of-software-architecture-d4473ffed864#.p7zuwnphe)

是的，我已经开发了其他应用程序，所以我“理智地意识到”我正在开发一个通常需要三个人花三个月时间才能完成的东西——至少——这对我自己和我的《犯罪》搭档 T2·普拉斯来说是个不小的任务。但早在 6 月份，我所在的团队赢得了曼哈顿 Angelhack 黑客马拉松的大奖。我们被邀请加入黑客计划。看到你的梦想成真，成为百万富翁，他们说！

(添加挥手动作)

(Angelhack 没有这么说，也没有发表任何过于乐观的废话，但如果我假装他们说了，我的故事会更好。)

# 这些是我打算使用的技术

*   AWS Cognito 用于身份授权和认证
*   针对静态内容托管的 AWS S3、CloudFront 和 Route 53
*   AWS API Gateway，Lambda，DynamoDB，ElasticSearch，用于支持我的 CQRS 和 REST 风格的数据架构
*   然后自举，反应什么的放到前面。我不是前端开发者。我是一名建筑师。我是来让事情变得复杂的。(“别担心，我是工程师，我可以把这个过程搞复杂。”)和实时消息传递将通过 MQTT 和/或 WebSockets 实现。

# 我现在在哪里？

我已经完成了第二和第三项，第一项也快完成了。我还在研究的最后一个。看看这里。【https://gogobanana.net/ 

# 迄今为止在无服务器方面学到的最重要的经验？

## AWS 路线 53

如果你注册了一些你不关心的域名，比如在一次黑客马拉松中，你在凌晨 2 点决定你的产品应该叫做 jumping spider . info——不是说我——但是后来你意识到你喝了太多的怪物能量饮料。你在内心深处认为拥有一个域名不需要任何成本，对吗？

这是一个警告。默认情况下，当您在 Amazon Route 53 上购买域名时，Amazon Route 53 会为您创建一个 DNS 托管区域。这就是为你的域名提供快速免费 SSL 证书的原因。但是 DNS 的管理费用大概是每月 50 美分。我已经注册了 9 个域名，上个月我在 DNS 托管区域使用了大约 5.30 美元的信用，这些区域基本上只有两个默认记录。(我通过删除我不使用的托管区域修复了这个问题)

## AWS Cognito

这不是一个容易设置的现成服务。我找到的最简单的助手指南是开发者百页 SDK 文档和[僵尸实验室。](https://github.com/awslabs/aws-lambda-zombie-workshop)(不，真的，这是一个很棒的实验室，涵盖了许多其他无服务器主题，看看吧。)

## API 网关、Lambda 和 DynamoDb

我的目标是从 EventBrite 中提取大约 1000 条记录，并将它们存储在我的 DynamoDb 中，然后这些记录将流向 ElasticSearch。(这个 DynamoDb streams 特性非常棒，Python 模板很容易在 ElasticSearch 上根据您自己的类型和索引方案进行调整)

然而，这是我对 Lambda 和 DynamoDb 的主要挑战。将我的 Lambda 放在 128mb 的 RAM 中，将 DynamoDb 放在较低的写容量中，会很快削弱我的进程，导致超时。我首先尝试将 node npm 包 async 合并到我的进程中，以便一次只放一个 PUT。

然而，即使这样仍然会导致超过 3 秒的超时。我还尝试提高 Lambda 的超时时间。最后，我不得不一次强制一个 PUT 操作，在我的 Lambda 上将 RAM 设置为 512mb，超时 60 秒，在 DynamoDb 上将写容量设置为 25。我现在最大的问题是 DynamoDb 不再是持久保存我的 JSON 文档数据的廉价而简单的方法。请注意，还是很便宜，大概每月 25 美元或更少。但是，当其他人开始使用我的数据库时，我开始焦虑地猜测我将来可能需要扩展多少。

DynamoDb 也有一个写批处理项特性，允许一次插入多达 25 个文档。(不超过 25)

[更新:在这里发布了一个解决方案博客条目](/@CodingJoe/dealing-with-dynamodb-write-capacity-limits-and-lambda-timeouts-f4e08d9f4b4f#.kog03oe4c)

## 弹性搜索

这并不奇怪，但是 ElasticSearch 在我的集群上为两个 t2.small 节点每月花费了我大约 26 美元。我可能会将它降低到仅一个节点的不安全水平，但是我不愿意在开始对它应用任何有用的东西之前破坏我的数据。它的速度也快得惊人。我在查询中使用了 geohash 过滤器，并使用高斯函数对邻近度和最近度进行了自定义函数评分。

## 谷歌地图 API V3

这可能是也可能不是谷歌地图的问题，但我想我会把它标记出来以防万一。我刚从英国度假回来。(我知道，在为期 13 周的黑客活动中预订假期是个好主意。但我在 4 月份买了机票，并在 6 月份赢得了黑客马拉松，我不相信我能像超人一样环游世界并让时光倒流。只要你相信，你可以做任何事！)

当我在英国的时候，我只能在伦敦的几个小时里使用我的应用程序。我不知道发生了什么。大部分时间地图都画不出来。我想可能是反应或其他原因造成的，但在去除这些问题仍然发生。当然，我必须说服我的妻子让我飞回英国，只是为了再次测试这个错误，但我想我在英国无法让谷歌地图加载到我的手机上，因为某种地区性的超时。(不过在英国的时候，它在我的笔记本电脑上运行得很好。)

## 试试我的网站

不过，使用这款应用还是很棒的。这对于旅游来说非常方便。我们从乘坐伦敦地铁(又名地铁)到伦敦桥站开始。然后我们打算步行去伦敦塔。

在路上使用我的应用程序时，我发现了“纪念碑”——献给在 1666 年大火中丧生的人(如果我能正确阅读我的罗马数字的话)。)他们正在分发贴有贴纸的儿童练习册，这对我的孩子来说是一个巨大的胜利。否则，我不会在纪念碑旁边停下来，而是在去伦敦塔的路上一直呆在河边。

![](img/3ecd09448dbf693d4e7175f711487120.png)

The Monument, erected in 1677 in memory of the Great Fire of London

看看我正在进行的无服务器工作，[https://gogobanana.net](https://gogobanana.net)

[请花些时间填写这份调查，并与我分享您的反馈。我本来应该做的事情，而不是去度假。)](https://docs.google.com/forms/d/e/1FAIpQLSeA4hqUzAByNpd-yGvkiLmLlm_RPj1W9owU3h4ZyFPBT17hUg/viewform)

# 如果你觉得这篇文章有用，请关注我的 Medium 和 heart 这个故事。非常感谢！:)

我还会在我的 https://twitter.com/@CodingJoe 推特[和 https://instagram.com/codingjoe 的 Instagram】上发布我遇到的问题和解决方案](https://twitter.com/@CodingJoe)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)