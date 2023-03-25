# 运行 Windows XP 的 ATM 机会有安全风险吗？你可以放心！

> 原文：<https://medium.com/hackernoon/do-atms-running-windows-xp-pose-a-security-risk-you-can-bank-on-it-1b7817902d61>

![](img/142bfeb022629d192e1ba0f029eadb2b.png)

Photo courtesy of [Alistair McMillan](https://www.flickr.com/photos/alistairmcmillan/2337950351)

我最初是在 2014 年 2 月为 CSO 写的[这篇文章。尽管我的文章已经发表三年了，不幸的是这个问题*仍然*及时且相关！现在是 2017 年，在微软停止支持](http://www.cso.com.au/article/537369/do_atms_running_windows_xp_pose_security_risk_can_bank_it/)[安全](https://hackernoon.com/tagged/security)和稳定性补丁很久之后，世界各地的 ATM 仍在运行 Windows XP embedded。有关更多信息，请参阅其他人最近撰写的文章:

*   [Windows XP 和 USB 端口使取款机易受攻击](https://www.scmagazineuk.com/windows-xp-and-usb-ports-making-cash-machine-vulnerable-to-attack/article/531761/)
*   [印度的大多数自动取款机很容易成为黑客攻击的目标&恶意软件攻击](https://www.thequint.com/technology/2016/12/13/most-atm-india-are-still-running-microsoft-windows-xp-leaving-them-vulnerable-to-cyber-attack-hackers)

我只是一个和你一样的普通人。我在加拿大皇家银行有一个支票账户。注销信用卡后，这是我唯一的金融产品。我不时地看看我的平衡，我有这样的想法，“咻！付完房租后，我每周将有 100 美元用于购买杂货，并且还有足够的钱来预定《人物角色 5》！但我还是得等到下一次发工资的时候，才能买那些我看中的 80 美元的可爱鞋子。”我是工薪阶层，如果我的钱通过银行漏洞被盗，我会比富人受到更大的伤害。我知道我的许多读者都有同样的处境，除了你可能有孩子和家庭需要抚养。

去年，我和结婚五年的同居丈夫肖恩·鲁尼分手了。我现在与音乐家、艺术家和独立唱片公司老板 Rob Michalchuk 幸福地相爱并生活在一起。有趣的是，与我以前从事网络安全职业的前未婚夫不同，Rob 给了我许多文章的想法！

所以，这篇文章中关于肖恩·鲁尼是我未婚夫的部分现在已经过时了。但是仍然运行 Windows XP 的自动取款机的真正网络安全威胁并不存在。

请随意在 Twitter 上分享这篇文章，如果你认为我的文章值得一读，请点击绿色的心。你可以在推特上通过 [@kim_crawley](https://twitter.com/kim_crawley) 找到我。

世界知名的 IT 安全专家 [Bruce Schneier](https://www.schneier.com/) 是我的一个朋友。他和我的未婚夫肖恩·鲁尼在几个项目上进行了广泛的合作。因此，除了我与职业相关的需要以跟上最新的 IT 安全新闻，我还定期阅读 Schneier 的网站，就像人们在脸书上查看他们朋友的页面一样。“他最近在忙什么？他在想什么？”

1 月 27 日，他[发布了](https://www.schneier.com/blog/archives/2014/01/new_security_ri_1.html)一个让我感兴趣的东西。银行向微软施压，要求继续支持 Windows XP，因为他们仍然在 ATM 上使用该操作系统。

这让我很震惊，因为我无知地认为，考虑到 ATM 对数据的敏感性，它们只会使用 UNIX 或 Linux 内核。

我必须指出，我在这篇文章中表达的所有观点完全是我自己的。微软产品的安全性或不安全性是我所在领域的一个热点问题。根据多年的直接经验，当涉及到代码中的漏洞时，我对微软的评价很低。

在撰写本文时，Windows XP 发布于 12 年前，按照操作系统的标准来看，肯定是过时了。

一份关于仍然存在的 XP 漏洞的文档需要至少一千页的书才能完全覆盖，所以我只给你一个“科尔斯笔记”版本，重点放在几个可能对 ATM 造成特殊威胁的方面。

*   较新的自动取款机可以进行远程补丁管理。但是旧的自动取款机，包括美国仍在使用的大部分机器，只能手动修补。这意味着银行的 IT 专业人员必须一个分行一个分行地访问机器，才能应用微软的 Windows XP 嵌入式系统安全补丁。拥有手动修补 ATM 所需的专业知识的 IT 专业人员是昂贵的。他们的时间很昂贵。拥有老式自动取款机的大银行可能会雇佣其中的许多人。但是考虑到我所看到的许多大公司，包括金融公司，对劳动力费用的吝啬态度，这是我所期望的。他们的雇主可能不愿意在嵌入式 Windows XP 补丁可用后的几天内将他们部署到他们的每一台纯手动 ATM 上。微软和厂商中立的安全公司一得到漏洞信息就公布出来。这可能会在漏洞被公开和人工管理的 ATM 收到补丁之间留下几天甚至几周的时间。如果一个黑帽能破解一台 ATM 机，即使只有一台，对他们来说也是非常有利可图的。所以，动机是存在的。
*   许多运行 XPe(嵌入式 Windows XP)的 ATM 可能没有使用增强型写入过滤器。EWF 旨在防止恶意软件在驱动器上运行，从而破坏文件。在 XPe 中运行 EWF 总是可选的。正如我们在无数事件中看到的那样，比如去年 Target 和 Neiman Marcus 的 POS(销售点)系统被感染，大公司可能会雇佣最好的 IT 员工。但是他们需要得到雇主的批准，他们的安全策略才能生效。最终决策者往往是技术专长有限的高管。通常，这些高管决定不采纳他们雇佣的“电脑极客”的建议。“西装”们通常认为他们通过不让他们的 IT 和软件开发部门拥有他们要求的预算来省钱。但是，我不认为，从长远来看，采取这种态度的公司高管是在为他们的公司省钱。根据一家银行的公司结构，一套“西装”也可能对经营 EWF 说不，它有自己需要管理的补丁。
*   不同型号的自动柜员机的硬件配置通常是不同的。每个硬件组件都需要一个驱动程序才能在 XPe 中正常运行。特别是微软的操作系统，驱动程序通常包含专有(闭源)代码。Schneier 指出，第三方开发者，包括那些受雇于硬件组件供应商和银行的开发者，更容易为开源驱动程序开发定制软件。那些开发人员不断地学习新的[漏洞](http://resources.infosecinstitute.com/a-world-of-vulnerabilities/)，他们必须为其编写补丁代码。其他嵌入式操作系统，比如那些用 Linux 内核构建的操作系统，更有可能拥有开源驱动程序。自动柜员机使用的专用硬件是一个利基。
*   微软将于今年 4 月 8 日停止支持标准的 Windows XP。自 2015 年 7 月 14 日起，将不再有反恶意软件补丁。XPe 特定支持将于 2016 年初停止。由于如此大比例的 ATM 仍在运行 12 年前发布的操作系统，银行在两年内将所有 ATM 迁移到嵌入式系统 Windows 7 或 Linux/UNIX 内核嵌入式操作系统的可能性有多大？为了运行更新的操作系统，许多 ATM 型号及其硬件组件将需要完全更换。对于一家拥有数千台自动取款机的银行来说，这可能非常昂贵。

“我和一些客户谈过，由于这样或那样的原因，他们在 4 月 8 日之前不会完全从 Windows XP 迁移过来。我甚至和一些客户谈过，他们说除非运行的硬件出现故障，否则他们不会从 Windows XP 迁移。

银行和像 Diebold 这样的 ATM 制造商通常对他们使用的硬件和软件的细节保密。这完全可以理解。

富国银行、美国银行和加拿大皇家银行是全球仍在运营大量 XPe ATMs 的众多银行之一。

如果我们在 2016 年及以后看到对 XPe 运行的自动取款机的重大攻击，相比之下，这将使[零售 POS 攻击](http://resources.infosecinstitute.com/buyer-beware-credit-card-debit-card-data-can-stolen-cash-register/)看起来微不足道。请记住，自动柜员机必须连接到银行的中央电子银行系统才能运行。数万亿美元可能面临风险。

如果你真的喜欢我的写作，并且能抽出几块钱，请支持我的 [Patreon](https://www.patreon.com/kim_crawley) 。我没有赚很多钱，我也没有直接从我的媒体文章中赚*任何*钱。我慷慨的赞助人帮助我前进，我真的会从你们的支持中受益。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)