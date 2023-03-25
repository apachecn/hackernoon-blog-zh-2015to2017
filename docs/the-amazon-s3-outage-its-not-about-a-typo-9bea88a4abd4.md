# 亚马逊 S3 服务中断:这不是一个打字错误

> 原文：<https://medium.com/hackernoon/the-amazon-s3-outage-its-not-about-a-typo-9bea88a4abd4>

![](img/e7c16b385210126fc8382a471bea3664.png)

It happens.

本周最大的科技新闻当然是亚马逊 S3 的宕机，亚马逊的部分云宕机了 3 个小时。公司和消费者越来越依赖于 S3，所以这次停电影响广泛。

S3 的停电本身并没有困扰我——这只是一个小小的不便。然而，我意识到，对于一些公司来说，这是一个影响[收入的事件](http://www.npr.org/sections/thetwo-way/2017/03/03/518322734/amazon-and-the-150-million-typo)。尽管如此，我们都应该把它归入“[倒霉事](https://www.youtube.com/watch?v=edmqTODMZC4)”类别，然后继续前进。考虑到互联网的相对脆弱性，我真的很惊讶这种情况没有经常发生。

我对媒体将这一事件称为“扳倒 S3 的一根胖手指”的方式感到困扰，也对科技和信息安全推特(infosec)如何以同样的幸灾乐祸和紧张的同理心进行报道感到困扰，因为我们以前都做过类似的事情。(提醒我在博客中记录我销毁三个没有备份的 Exchange server 数据库的经历。)

如果故事是真的，一个错别字搞垮了亚马逊的云主干，那么这个故事就是错的；一个打字错误并没有让 S3 下台。这是管理链上的一连串失败。这是一系列失败的控制，甚至允许这种情况首先存在。

如果一个命令可以关闭公司面向客户的基础设施的如此重要的一部分，这是一个基本的设计问题。除了其他安全原则之外，我还想到了职责分离:影响收入的关键变更是由两个或更多人做出的。

良好的变更控制流程将记录所请求的变更，包括将要使用的命令、命令失败的风险、回滚程序和备份计划。“什么会出错？”评估的一部分还包括识别 S3 基础设施上发生重大可用性事件的可能性。

如果所有这些都失败了，风险评估应该识别可用性事件并量化影响。即使没有调用特定的命令或相关功能，关键业务流程本身也应该符合变更控制、回滚计划、系统架构审查和风险的适当管理批准的需要。

当我们想到信息安全时，我们喜欢想到酷而性感的工作，如[汽车黑客](https://www.wired.com/2015/07/hackers-remotely-kill-jeep-highway/)、[笔测试员](https://mitnicksecurity.com)、[让 ATM 机吐钱的人](https://www.youtube.com/watch?v=qwMuMSPW3bU)——有趣的东西。幕后，*我们*直播。我们是一支由信息安全治理、合规性和风险专业人士组成的队伍，为正义而战。

对在那个致命命令上点击<enter>的人:没关系。你会熬过去的。这里还有其他因素在起作用；尤其是你的打字错误。</enter>

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)