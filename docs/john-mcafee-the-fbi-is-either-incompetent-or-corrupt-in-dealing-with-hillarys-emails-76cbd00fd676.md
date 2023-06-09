# 约翰·麦卡菲:联邦调查局在处理希拉里的电子邮件方面，要么无能，要么腐败。事实是这样的。

> 原文：<https://medium.com/hackernoon/john-mcafee-the-fbi-is-either-incompetent-or-corrupt-in-dealing-with-hillarys-emails-76cbd00fd676>

![](img/1cf75f4dbeae005aca23029c6116ea29.png)

作者约翰·麦卡菲

我终于抽出时间观看了国会议员吉姆·乔丹对联邦调查局局长詹姆斯·科米的审讯，审讯发生在 10 月 19 日。(【https://youtu.be/jrKmxt2BRK0】T2)。这是一个令人大开眼界的顿悟，它一劳永逸地让我明白，联邦调查局的网络技术专家要么是这个星球上最无能的，要么是联邦调查局像伯利兹的内阁部长一样腐败，他们保持着世界上的腐败记录——顺便说一下，我和他们有过不少经验。

我兴致不高地看着审讯，直到议员问科米，联邦调查局是否知道保罗·科米塔在 Reddit 上询问如何从服务器的电子邮件数据库中删除一个[电子邮件](https://hackernoon.com/tagged/email)地址。顺便说一下，康贝塔先生是最终删除了 3.3 万封希拉里·克林顿电子邮件的技术专家。

有两点非常突出:

1.  科米不确定联邦调查局是否知道康贝塔在 Reddit 上的帖子。他知道希拉里的电子邮件地址已经被删除，但他相信这些邮件仍然完好无损。
2.  康贝塔作证说，希拉里害怕自己的电子邮件地址被公之于众。所以他从邮件里删除了。

现在，任何有能力的软件工程师都可以考虑上述两个条件，并得出与我相同的结论:克林顿或她的工作人员中的某个人想出了一个聪明的主意，从法律上(某种程度上)剥离希拉里电子邮件的所有标题，使剩余的文本几乎无用。它们没有“发件人”或“收件人”字段，没有日期戳、时间戳，也没有关于谁可能或可能没有被复制到电子邮件中的信息——而且，这似乎可以通过合法的方式完成，只需声明“我们出于隐私原因删除了希拉里的电子邮件地址”。整个标题也必然消失的事实是该过程的一个“意外”产物。哎哟..有人只能说“我的错”。我承认，很聪明。但由于情有可原的情况，它没有按照计划的方式进行，原因我将在后面解释。因此，最终采取了用 Bleachbit 清除邮件的激进措施。但首先，我是如何得出上述结论的。

让我们先来看看科姆贝塔的证词，她说克林顿害怕她的电子邮件地址会被泄露给公众。

据我所知，也是大多数人的常识——无论是联邦调查局还是联邦政府的任何其他调查机构，都从未公开过任何调查目标的电子邮件地址，无论是否提出指控。希拉里·克林顿足够聪明去竞选总统，所以这个信息的瑰宝必须为她所熟知。至于少数可能通过调查获得信息的国会议员，他们都已经有了她的电子邮件地址，而且她已经给大多数人发了多次电子邮件。

显然，康贝塔的证词要么是公然虚假的，要么是希拉里对司法部内显而易见的政策和程序完全不知情，她应该立即因不合格而退出总统竞选。我不相信希拉里不知道她的电子邮件地址绝对不会公开的事实。我怀疑会有人相信这一点。我们不得不相信，康贝塔的证词是明显不真实的。

现在让我们检查一下这件事的技术方面。首先，Combetta 在信息科学方面毫不逊色。你会期望一个最有才能和能力的人来管理一个包含绝密国家文件的服务器，而 Combetta 没有让你失望。所以我对他的才华没有意见。这就是我对这个故事的疑问。

在班加西众议院特别委员会与国务院就制作她的所有电子邮件达成协议后的一天，康贝塔在 Reddit 上发布了以下内容:

![](img/660344e81abb314bf7ad700bf50c7bd6.png)

奇怪的是，任何一个计算机科学一年级学生都可以正确回答这个问题，即使是在兄弟会聚会上喝醉了。答案很简单:“没门儿”。至少在没有数万小时的人工劳动(33，000 封电子邮件)或一个你必须自己编写的软件的情况下是不行的——这将需要比允许的电子邮件传票更多的时间来编码和测试。

以下是 Reddit 的一些预期回应:

![](img/f8b6c9cddb4bdd9bb296d65fa1225a19.png)![](img/b7a9178490b7f4f638e9d69117fcd9c6.png)

由于明显的法律原因，电子邮件系统被设计成很难修改发件人、收件人、日期和时间。然而，剥离整个头部是微不足道的。出于各种原因，人们经常只想要电子邮件的文本。

难以置信的是，com betta——一个对国务卿的个人服务器负有技术责任的人——不会完全熟悉关于电子邮件服务器如何工作的信息科学 101。这简直不可思议。

那他为什么在 Reddit 上发布这个问题呢？毒品太多了？不太可能。即使我嗑了迷幻药、冰毒和 K，喝了半瓶苏格兰威士忌来缓解冰毒，我相信我仍然能记住这个简单的事实。

至少对我来说，不可避免的结论是，这篇文章是精心制作的，旨在提供一份记录，表明康贝塔只是试图让希拉里免于因其电子邮件地址被公开而面临的潜在尴尬。

如果 Combetta 确实试图修改邮件头来更改电子邮件地址，那么结果肯定是邮件头被彻底破坏。电子邮件提供商创建了防止这种修改的安全措施。一个世界级的黑客可以做到这一点，需要大量的工作和时间，而这是康贝塔所没有的。

同样，康贝塔对这一切了如指掌。如果没有，那么克林顿雇了一个白痴，这并没有很好地推荐她。

至于为什么这个过程没有达到目的？归咎于维基解密。即使没有标题，被编辑的电子邮件在电子邮件数据库中仍然是有序的。编写一个取证程序来交叉引用已知的电子邮件发件人、收件人、日期和时间以及编辑的电子邮件并不需要太多时间。我怀疑 95%或更多的丢失数据可以恢复。

联邦调查局想过这个吗？这不就是问题所在吗？

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)