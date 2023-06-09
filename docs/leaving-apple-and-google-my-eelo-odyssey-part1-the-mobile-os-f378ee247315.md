# 离开苹果和谷歌:我的“eelo odyssey”——第一部分:移动操作系统

> 原文：<https://medium.com/hackernoon/leaving-apple-and-google-my-eelo-odyssey-part1-the-mobile-os-f378ee247315>

![](img/3589c4559922a5c50f86bb260d7bdeca.png)

**编辑:** **现在支持 eelo！Kickstarter 活动** [**刚刚开启**](https://www.kickstarter.com/projects/290746744/eelo-a-mobile-os-and-web-services-in-the-public-in) **！**

所以我公开了离开苹果和谷歌的决定。这是一种生活方式的选择，通过将我的个人数据私有化来逃避将我作为产品的科技巨头。我不喜欢苹果现在的所作所为，苹果的态度，新的 iPhone 和他们的价格…这也是我的孩子和所有关心他们的人的自由行为:我希望他们有选择，并且对他们的选择如何影响他们的生活和他们的经济生态系统有一个清晰和知情的看法。这就是 eelo 的宗旨:为用户的数字生活提供一个可行且有吸引力的选择。

在这篇新文章中，我将描述目前为止我在移动领域为摆脱谷歌和苹果已经做了什么，以及还有什么要做(剧透:还有很多)。在下一部分，我将解释在 web 服务上需要解决什么问题，并画出 eelo 项目的全貌。

# 默认 AOSP/线性 geOS 有什么问题？

谈到 LineageOS，你可能会想“为什么你要黑掉一些已经大部分开源并且运行良好的东西？”

答案很简单:AOSP/linegeos 的核心是可用的，性能很好，但对我的需求来说还不够好:设计不是很有吸引力，有大量的微观细节可能会成为普通用户的绊脚石。此外，除非你是一个极客，否则如果你不想要 google inside，LineageOS 实际上是不可用的。

# 设计点

关于设计，我知道一些 Android 用户喜欢它，但我真的不喜欢默认的图形用户界面。我觉得它很丑:图标不好看，颜色很悲伤，我不喜欢启动器的人机工程学和行为。

![](img/d57dc1f0c49c1bbda19c89e231228c23.png)

所以至少我们需要一个新的启动器，和更好的图标。默认通知看起来也不太好，我也不太喜欢设置部分。与 UI 的其他部分相比，它可能会更糟，但它仍然很糟糕，只有一个绿色的线条。我喜欢更吸引人的，可能更有条理的。

“好消息”:你可以在谷歌 Play 商店找到数百个定制的启动器和图标主题。但是，要么你必须为它们付费，要么你得到带有大量广告和诈骗的免费东西。所以对我来说不是。

# 坏消息，好消息

坏消息是，我是 Android 开发新手，我不认为自己是一个伟大的开发人员。我可以黑东西，我可以重新编译和集成东西，但我没有足够的实践来从头开始编程一个新的发射器，而不花几个星期的时间。

好消息是，我找到了一个非常有才华的全栈开发人员，他对这个项目感兴趣。我们已经同意，作为第一次合作，发布一个新的发射器，新的通知系统和新的“控制中心”。

# 首次成功

我选择在 LeEco Le2 上测试 LineageOS/eelo 的定制版本。这是一款漂亮的 5.5 英寸智能手机，1080×1920 像素屏幕，3GB 内存，32GB 存储，背面有手指传感器，还有一个 4K 摄像头。大约要花 130€。是的，那大约是 150 美元。是的。

还有我在等一个小米 Mi 5S。它的屏幕较小，我更喜欢较小的设备供自己使用。我可能会尝试一下 LG G6。(想建议一个设备？[告诉我](https://twitter.com/gael_duval)！)

经过几周的工作，我们现在有了一个新的发射器！它仍然缺少一些功能(如卸载应用程序)，但它已经功能齐全。在这个视频中，你可以看到“图标组”功能，并在几个启动页面之间滑动:

[eelo BlissLauncher 1](https://www.dailymotion.com/video/x6ii707) from eelo

在这里你可以看到“停靠图标”功能:

[eelo BlissLaucher docking action](https://www.dailymotion.com/video/x6ii709)

我们称它为“BlissLauncher ”,只是因为它是一个伟大的发射器。我们还有第一个新的通知系统和新的解锁屏幕:

下一次将有所有的默认集成在一个新的全新构建。在完成这篇文章的时候，我已经成功地刷新了一个新的版本，有了新的启动程序和新的通知系统。

# 彻底摆脱谷歌的东西

现在我们有了一个更好的 eelo 发射器，我正在和一个伟大的非常专业的设计师一起工作。他过去对 Mandrake Linux 界面图标贡献很大，当时我们重新定义了所有的用户界面和所有图标。后来，他还参与了 Ulteo 的第一次发布，当时它还是一个云操作系统项目，而不是 Citrix 的替代品。我们正在一起重新设计默认的应用程序图标，一些壁纸，闪屏，还有第一个真正的 eelo 标志。从长远来看，我们必须重新设计完整的用户界面。

![](img/0e33892c9062ec487da70f25f9263da3.png)

但是我们想要的不仅仅是好看、有吸引力、好用的东西。我们想要更多的隐私！谷歌服务也不符合我的隐私观。

因此，我们不想要谷歌服务。我们不想要 Google play 商店。我们可能不想要大多数谷歌应用程序，如日历、电子邮件等。

此外，我们可能也不想要脸书和其他一些所谓的“免费”服务。这将是用户的选择安装与否。我知道我们不能在一次迭代中改变世界，这将是一步一步的。

这每一点都需要在 Eelo 中解决。我们需要一个独立的应用程序库，一个独立安全的电子邮件提供商，一个独立的在线驱动器，在线办公服务…所有这些都很好地集成在 eelo 中。用户利益第一。

# 没有谷歌的第一轮

我第一次能够重新编译和刷新 LineageOS 时，我很快就不得不安装谷歌 Play 商店和 Google Play 服务来安装常见的应用程序，否则我几乎什么也做不了。

但是有一些替代商店。例如，F-Droid 是一个非常成功的 APK 应用程序库，只提供 100%开源软件应用程序。

对于非开源应用程序，还有其他可供选择的应用程序商店。比如有 Aptoide。它提供了最常见的应用程序，如 Twitter，Waze 等。但不幸的是，当我检查 Aptoide APK 包裹的签名和尺寸时，我发现它们与谷歌 Play 商店上的不一样。我不确定是否能很好地理解这种情况背后的原因，至少对于常见的应用程序来说是这样，所以我寻找其他的替代方法。

我发现 APKPure 是一个很棒的免费应用商店。相信我，很多应用程序都是免费的！事实上，我意识到我的 iPhone 上只有免费的应用程序。我知道许多人只使用免费应用程序。因此，如果你不想使用谷歌 Play 商店，也不需要非免费的应用程序，APKPure 是一个很好的选择。我检查了他们的许多软件包，它们和谷歌 Play 商店上的一模一样。只有官方套餐。

除 APKPure 之外，Yalp 也是一种选择。Yalp 是一个开源应用程序，充当谷歌 Play 商店的一种匿名代理，也只提供官方的 APK 软件包。

所以对于应用程序，我现在同时使用 F-Droid 和 APKPure。这已经非常舒适了，我成功地尝试了几十个应用程序，包括最常用的应用程序(脸书、Messenger、Twitter、Waze、Telegram、Skype、LinkedIn、Spotify……)。

但我认为我们需要一个“eelo 商店”来提供这两者:

*   像 APKPure 这样的官方免费应用
*   像 F-Droid 中的开源应用程序

所有这些都集成到一个单一的、有吸引力的、快速的应用程序中，用户可以轻松地检查一个应用程序是否是开源的，用户可以评估应用程序的隐私级别，用户可以报告一些欺诈问题。我们肯定需要将这一点添加到 eelo 路线图中。

# 可爱的谷歌服务

谷歌创造了一个功能，把用户关在他们的环境里。这就是所谓的“谷歌服务”。例如，如果你想使用谷歌 Play 商店，你必须安装一个非开源服务。它也被一些应用程序使用。它提供的服务包括:

*   分析学
*   帐户认证
*   云消息(通知)
*   驱动器
*   地理爱好
*   地图 API
*   移动广告
*   游戏 API
*   …

Android 应用程序的开发人员不会被迫使用它们，但显然谷歌正在尽最大努力使它们尽可能令人满意，如果不是强制要求某些功能的话。

好消息是，许多普通的应用程序，那些每个人每天都在使用的应用程序，并没有使用谷歌服务，或者他们并不太依赖谷歌服务。可能很多开发者不喜欢被禁锢在一个单一的生态系统里。

据我尝试，这方面问题最多的应用好像是一些游戏，比如 Pokemon Go。除非你安装了 Google Play 服务，否则这个似乎不可用。

好消息是，有一个不错的项目正在为谷歌服务提供开源替代方案。叫 [MicroG](https://microg.org/) ，eelo 大概会集成。

谷歌的另一个“好主意”是他们的安全网认证 API。它是 Android 应用程序开发人员可以用来检查用户设备是否是符合谷歌环境的官方设备的东西。它检查硬件、软件，检查设备是否是根设备。最后，如果环境不符合谷歌的规则，这可以用来阻止应用程序运行。幸运的是，有“ [Magisk](https://magiskmanager.com/) 来规避这个问题。我们可能也需要在 eelo 中默认集成它。

# 网络搜索呢？

现代操作系统的许多部分会导致“隐私泄露”。到目前为止，我已经谈论了来自系统内部的隐私问题。

但如果你在谷歌上搜索某样东西，很有可能谷歌能确定你在找某样特别的东西。例如，即使你在 Chrome 浏览器中没有使用谷歌账户，他们也可以追踪你的 IP 地址。

所以我们肯定需要提供一个默认的搜索引擎来替代谷歌搜索。或许我们也不想要必应或雅虎，尽管使用各种搜索引擎会更好，这样它们就不会确切地知道你搜索的所有信息，因此无法有效地整合你的私人信息。我们有几个选择:

*   众所周知的 DuckDuckGo:尽管它严重依赖谷歌搜索结果，但它提供了谷歌所没有的隐私保证。
*   Qwant 是一个新的搜索引擎，正在取得巨大的进步，现在有自己的索引，并提供隐私保障
*   还有完全开放的 CommonSearch:项目，但它还没有准备好

因此，我正在考虑将 DuckDuckGo 和 Qwant 作为 eelo 搜索的默认搜索引擎和 eelo 附带的网络浏览器，同时仍然提供 Google(和其他公司)作为选项。的确，在某些情况下，它仍然提供最好的结果。

# 还有…

有一长串的互联网服务可以以多种方式跟踪你，发送和处理你的个人数据。例如，使用 Gmail(或类似的)电子邮件帐户是谷歌了解你的一个很好的方式。

而且，你们中的一些人可能知道非常快的谷歌域名解析器:8.8.8.8 和 8.8.4.4。DNS 解析器一直被许多应用程序使用。他们把域名转换成 IP 地址。我说:不要使用谷歌 DNS 解析器。每次你的智能手机寻找一个域名，谷歌知道它，他们可以将这些信息添加到他们知道的关于你的其他信息中。

相反，您可以使用 9.9.9.9(或 2620:fe::fe IPv6 ),这是一个由非营利研究机构运营的快速公共 DNS 解析器，不会存储您的 IP。并且可以通过安全协议(TLS)访问。

当然，这是我们需要解决的所有网络服务生态系统。正如我之前所说的，eelo 将提供一个隐私更好的移动系统，但也提供一些网络服务，如在线办公套件，一些在线存储等。我们将聚合一些现有的 web 服务，如果需要的话改进它们，或者如果没有可用的服务，构建新的服务。

![](img/0408a60ec28b694a62a222f89db706aa.png)

然而，我们将面临一个黑暗地带:智能手机上的底层专有硬件驱动程序。它们驱动着摄像机、GPS、各种传感器……硬件供应商不提供这些驱动程序的源代码。除非进行一些繁重且耗费资源的逆向工程，否则它们极难重写。当然，一些“黑匣子”驱动程序可能会泄露用户的私人数据。

eelo 解决这一问题的未来选择是:

*   与 FairPhone 或类似的 100%开放硬件项目合作
*   审核低级驱动程序以检测不当行为
*   设计一款 eelo 手机…

# 加入 eelo odyssey！

如你所见，eelo 是一个真正的奥德赛。但我认为，也许这是第一次，所有的砖块都可以用来建立一个新的、一致的、有吸引力的、独立的、几乎免费的数字生态系统，它将更加尊重用户，尊重他们的隐私。这可能最终会挑战广告模式，而广告模式很可能是这种糟糕的、被认为是“免费”的模式的源头。

再说一次，eelo 是一个非盈利项目，是一个符合公众利益的项目。每个想加入的人，请吧！

**投稿方式很多:**

*   打个招呼！🙂有支持者帮助很大
*   贡献一些想法，一些资源，你擅长的
*   把我们介绍给能帮忙的人
*   谈论 eelo，分享 eelo 新闻和文章…
*   提供一些 mƀ支付一些服务器

此外，我已经开始为 eelo 进行众筹活动，因为需要一些资源来正确启动这个项目。我不确定这个活动到底能提供什么奖励，但我正在考虑。欢迎任何人的建议！

与 eelo 保持联系:[在 eelo.io](https://eelo.io) 登陆页面注册。联系我:gael @ eelo。超正析象管

**编辑:阅读更多:**

*   支持 eelo 早期开发！加入我们的 [Indiegogo 活动](https://www.indiegogo.com/projects/eelo-a-mobile-os-and-web-services-with-values-android)和[成为我在 Patreon 的赞助人](https://www.patreon.com/eelo)！
*   [离开苹果和谷歌:我的“eelo odyssey”——简介](https://www.indidea.org/gael/blog/leaving-apple-google-eelo-odyssey-introduction/)
*   离开苹果和谷歌:我的“伊洛奥德赛”第 2 部分:Web 服务
*   [eelo 创始人致埃隆·马斯克的公开信](/@gael_duval/an-open-letter-to-elon-musk-from-eelo-founder-6d6feafb53a2)
*   [eelo 不仅仅是一项技术，它还是一个自由和民主的社会项目](https://hackernoon.com/eelo-is-more-than-tech-its-a-societal-project-for-freedom-and-democracy-951ea5c8f162)

—gal(在 Twitter 上关注我[，在乳齿象上关注我](https://twitter.com/gael_duval)/)

PS 阅读这个系列文章的第一部分:[离开苹果和谷歌:我的“eelo odyssey”——简介](https://www.indidea.org/gael/blog/leaving-apple-google-eelo-odyssey-introduction)

PPS 本文原[贴在我的网站](https://www.indidea.org/gael/blog/leaving-apple-google-eelo-odyssey-part1-mobile-os/)