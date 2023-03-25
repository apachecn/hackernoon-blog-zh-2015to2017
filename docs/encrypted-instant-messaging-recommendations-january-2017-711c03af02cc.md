# 加密即时消息推荐 2017 年 1 月

> 原文：<https://medium.com/hackernoon/encrypted-instant-messaging-recommendations-january-2017-711c03af02cc>

**TL；dr:加密你所有的在线交流，没有理由不这样做。使用一个 XMPP+Omemo 客户端**[**(Android 上的**](https://play.google.com/store/apps/details?id=eu.siacs.conversations) **、iOS 上的**[**ChatSecure**](https://itunes.apple.com/de/app/chatsecure-verschlusselter/id464200063)**和 Windows/Mac/Linux 上的**[**Gajim**](https://gajim.org)**)。不要被那些把安全作为营销流行语的公司忽悠了(尤其不要用 WhatsApp、脸书和 Telegram)。**

在这篇文章中，我主要关注安全性的一小部分:加密即时消息。其他重要问题我会在其他帖子里说。这对我很重要，因为我看到了人们所做的、他们所关心的和他们没有意识到的之间的巨大差距。这是我看到的人们最大的潜力，在那里他们可以最容易地产生最大的影响。

# 你为什么要在乎？

为什么您应该关心保护您的通信和一般数据？

> “争辩说你不在乎隐私权是因为你没什么好隐瞒的，这和说你不在乎言论自由是因为你无话可说没什么区别。”—爱德华·斯诺登

[https://en.wikipedia.org/wiki/Nothing_to_hide_argument](https://en.wikipedia.org/wiki/Nothing_to_hide_argument)

隐私权就是自我的权利。你“拥有”你。你决定什么时候想分享你，什么时候不想。

这里有一个很长的论点列表，不幸的是用德语，如果你有类似的英语列表，请在评论中告诉我:[https://wiki.piratenpartei.de/Ich_habe_nichts_zu_verbergen!](https://wiki.piratenpartei.de/Ich_habe_nichts_zu_verbergen!)

或者让它不那么抽象:你介意给我你的电子邮件密码吗？没有吗？为什么不呢？我以为你没什么好隐瞒的？

为什么对你来说，我看不完你所有的邮件比默克尔、特朗普和扎克伯格更重要？

为什么你更信任川普而不是我？

是的，这是真实的，计算机实际上正在读取你的未加密通信(电子邮件和聊天)。然后，当有“调查”时，许多人、警察、政客、[安全](https://hackernoon.com/tagged/security)机构可以深入查看并阅读你的每一封电子邮件，仅仅因为你 10 年前写了一封电子邮件给一个现在可能涉嫌犯罪的人。这正是斯诺登身上的许多模糊之处。[https://en . Wikipedia . org/wiki/PRISM _(监视 _ 程序)](https://en.wikipedia.org/wiki/PRISM_%28surveillance_program%29)

我希望直到现在你都确信你想要加密你的通信。不要担心，重要的是，自 2016 年以来，这比以前容易得多。事实上，现在任何人都可以做到这一点。是的，我妈妈给我发了加密信息。

好消息是，这并不意味着你的通信有任何开销，你只需要安装一个新的应用程序。我会比较不同的可能性，并根据合理的理由进行排序。

你应该意识到的是，当你选择一个 IM 客户端时，你同时做了两个选择:一个是客户端本身，另一个是这个客户端使用的协议。这意味着您将只能与使用与您的客户端使用相同协议的客户端的人通信。

我认为如何呈现这个决定最好，并决定通常协议和应用仍然是耦合的，目前最好的是有一个列表，而不是分成两个列表。接下来，我将为您列出协议和应用的重要特性，您可以通过这些特性来确定协议/应用的好坏，并列出协议建议和您可以(或许应该)做到的特性；))在决定一个协议时所关心的。

# 决策衡量准则

*   开源(客户端和协议):你不能相信任何闭源加密，是的，就是这么简单。原因:你怎么分辨他们只是在 UI 里给你看了一个锁，而不是真的做了加密？是的，你不能。没人能。你不相信我，公司绝不会这么不诚实地去做那件事？请看这里:【https://hannover.ccc.de/~nexus/bubcon/bubcon.html(概要:他们宣传安全，但实际上并不安全)——你是说大公司永远不会这么做？大部分都是被迫:【https://en.wikipedia.org/wiki/National_security_letter】T4—所以开源是主要标准，非开源的东西都不能推荐。当然，仅仅有一个协议和客户端开源是不够的，还需要一些安全审计。我将包括需要闭源服务器的应用程序，因为从理论上讲，如果端到端加密足够可靠，那么客户端是开源的就足够了。然而，它“更好”也不符合下一个标准。
*   联邦/分权:想想电子邮件。如果你使用 gmail，你可以给使用 hotmail 或 yahoo 的人发邮件。如果你不能给他们发邮件，那就太疯狂了，对吧？你为什么接受即时消息？如果你用的是 Whatsapp 你只能和 Whatsapp 用户对话，很蠢吧？让一切(客户端和协议)开源和联合的结果是，用户可以选择客户端，因此如果他们发现自己在 gmail 获得了更好的体验，就可以从(电子邮件示例)雅虎切换到 gmail。
*   **群聊**:你希望能够和一群朋友聊天吧？
*   **多设备**:你希望能够从你的两部手机和三台电脑上聊天，并在它们之间无缝切换，对吗？
*   **离线消息**:是的，以前有协议不支持你可以在对方当前离线的情况下发消息，傻呵？
*   **最近的安全审计**:开源是好的，但是当没有人真正进行适当的安全审计时，没有人知道加密是否真的有价值。如果上一次代码审计是在过去 12 个月内，EFF 会给出一个绿色标志。
*   **系统支持**(协议):看联盟，你希望所有主流操作系统和浏览器(Linux、Android、MacOS、iOS、Windows、Firefox、Chrome)都有可用的客户端。我将使用以下评级方案:一个应用程序从 5*开始，对于每个完全缺失的操作系统(iOS、Android、Linux、MacOS、Windows)，它会失去 2 颗星(同时支持任何浏览器计数)，仅支持浏览器或仅支持桌面是一颗星，如果操作系统中可用性最高的客户端< 4，它也是一颗星。
*   **匿名**:对于相当多的人来说，保持匿名是很重要的，这意味着他们的即时消息账户不与电话号码或电子邮件相关联。
*   **可用性**:你希望你的用户体验很棒。这也包括发送文件和图像是很好的和容易的。我将根据自己的经验给出一个 5 星的评级，请随意运用您自己的判断并计算排名。
*   指纹/密钥验证:这是关于加密的唯一一件非常重要的事情，到目前为止还没有完全解决。这需要它自己的文章，但是让我用几句话来试试:你需要发现你实际上是在和你认为你在交谈的人交谈。否则，攻击者就可以声称他们是你真正想与之交谈的人，而你的加密就消失了。那么，如何发现你是否在和正确的人交谈呢？您需要手动验证应用程序中显示的联系人的密钥或指纹是否与他们的实际指纹/密钥匹配。为了验证您需要使用现有的可信通信渠道。例如见面、通过电话、信任网或社交媒体打电话(希望攻击者无法访问)。在如何实现这一点以及要求用户进行指纹验证方面，可用性存在巨大差异。我将对我如何判断可用性和过程给出 5 颗星的个人评级，以鼓励用户这样做。
*   **完美前向保密(PFS)** :如果你的密钥被泄露，攻击者就无法解密过去的通信。

协议分级遵循这个分级方案:支持联合/去中心化、群聊、多设备、离线消息、前向保密、匿名和被审计。评分为 5 减去缺失属性数，此评分的权重加倍。是的，从技术上讲，并非所有属性都是真正的协议属性，但它是您获得的产品包的一部分，因此我将它包括在此评级中，而不是为了使事情过于复杂。

# 排名，截至 2017 年 1 月

Encrypted/Secure IM Ranking, as of January 2017

# 推荐

**XMPP+Omemo (** [**对话**](https://play.google.com/store/apps/details?id=eu.siacs.conversations) **，**[**ChatSecure**](https://itunes.apple.com/us/app/chatsecure-encrypted-messenger/id464200063)**，**[**Gajim**](https://gajim.org/downloads.php)**)**:我的推荐(截至 2017 年 1 月)。XMPP 有着悠久的成功历史，并且得到了非常好的标准化和支持，不幸的是，到目前为止，没有多少客户选择 Omemo ( [状态页面](http://www.omemo.top))。而唯一的桌面客户端(Gajim)只有非常一般的可用性。此外，Gajim 很难设置，虽然 Conversations 很棒，但它要么在 Android 商店花费 3€，要么必须通过 F-Droid 安装，这也是一个麻烦的设置。因此，即使是我目前的推荐也远非完美。但这是你目前能得到的最好的，而且比 2015 年能得到的要好得多。
[**信号**](https://whispersystems.org/) :直到 2016 年 11 月这一直是我的推荐，因为 Riot 在那之前不支持 Olm e2e 加密，直到 2017 年 1 月才出现 iOS 版 XMPP+Omemo 客户端。截至 2017 年 1 月我不再推荐 Signal，因为 XMPP+Omemo 和 Riot 是更好的替代方案。此外，它还有一堆通知和重复消息的小故障/错误。

# 不推荐

因为它们是**封闭源** :
Whatsapp、脸书、Skype、谷歌 Hangouts、谷歌 Allo、Facetime、QQ Mobile、微信、Viber、LINE、黑莓 Messenger、Threema、TrustCase、whistle.im

因为**没有安全审计** :
Wire、RetroShare、Tox、Surespot

因为**协议是垃圾**(不支持群聊、多设备或离线消息):
OTR、Telegram*、静音电话、Tox、跳弹、Surespot

*是的，信不信由你，Telegram 不提供加密群聊和多设备。他们实际上是“不要被愚弄”的警告非常正确的应用程序/公司之一:是的，他们提供 e2e 加密，但只有当你明确启动它，然后只在两个移动设备之间(不，不支持桌面和浏览器)。它们也是一个很好的例子，说明为什么发明自己的加密技术通常不是一个好主意:[http://www . cryptofails . com/post/70546720222/telegraphs-cryptanalysis-contest](http://www.cryptofails.com/post/70546720222/telegrams-cryptanalysis-contest)

[](http://www.theverge.com/2017/1/11/14237136/trump-leak-telegram-security-cracked-russia-encryption) [## 特朗普泄密引发关于电报安全的新问题

### 昨晚，BuzzFeed 发布了一份未经证实的情报报告，内容是关于当选总统唐纳德·特朗普与俄罗斯的关系…

www.theverge.com](http://www.theverge.com/2017/1/11/14237136/trump-leak-telegram-security-cracked-russia-encryption) 

# 附录

我投入了大量时间从各种资源中收集本文中的信息。这反映了我目前的知识状态。我打开我在这里写的所有东西来讨论，当有新的、合理的信息出现时，我非常乐意更新我的知识、信念和这篇文章。也就是说，我提供以下 SLA:我将阅读每一条评论，直到 2017 年 5 月，并在必要时更新文章。

**参考文献**

我使用了很多来自(也更新了)的信息:

[https://en . Wikipedia . org/wiki/Comparison _ of _ instant _ messaging _ clients # Messengers _ with _ client-to-client _ encryption](https://en.wikipedia.org/wiki/Comparison_of_instant_messaging_clients#Messengers_with_client-to-client_encryption)

 [## 客户-维基百科

### 这是一个即时通讯软件。

de.wikipedia.org](https://de.wikipedia.org/wiki/Liste_von_XMPP-Clients) [](http://www.omemo.top) [## 我们到了吗？巴斯赫特著

### 在各种 XMPP 客户端中跟踪 OMEMO 集成的进度。

www.omemo.top](http://www.omemo.top) [](https://www.eff.org/de/node/82654) [## 安全消息记分卡

### 这是我们记分卡的 1.0 版本；它已经过时了，在这里保存下来纯粹是出于历史原因。请…

www.eff.org](https://www.eff.org/de/node/82654) 

**安全审计**

Omemo(由 Conversations、ChatSecure、Gaijm 使用):[https://conversations.im/omemo/audit.pdf](https://conversations.im/omemo/audit.pdf)

olm(Riot 使用):[https://matrix . org/blog/2016/11/21/matrixs-olm-端到端-加密-安全-评估-发布-实施-跨平台-on-riot-at-last/](https://matrix.org/blog/2016/11/21/matrixs-olm-end-to-end-encryption-security-assessment-released-and-implemented-cross-platform-on-riot-at-last/)

信号:[https://eprint.iacr.org/2016/1013.pdf](https://eprint.iacr.org/2016/1013.pdf)

crypto cat:[https://least authority . com/static/publications/least authority-crypto cat-audit-report . pdf](https://leastauthority.com/static/publications/LeastAuthority-Cryptocat-audit-report.pdf)

跳弹:【https://ricochet.im/files/ricochet-ncc-audit-2016-01.pdf】T2

据 https://www.eff.org/de/node/82654[报道](https://www.eff.org/de/node/82654)最近有一个关于:信号，无声电话，电报，OTR(洋泾浜)和 ChatSecure 的审计。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！