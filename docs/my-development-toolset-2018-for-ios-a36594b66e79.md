# 我的 iOS 开发工具集 2018

> 原文：<https://medium.com/hackernoon/my-development-toolset-2018-for-ios-a36594b66e79>

![](img/d099f52cd2ce6c467af115144831cd93.png)

A cat always attracts the attention.

2017 年即将结束，我将介绍我在这一年中使用的 iOS [开发](https://hackernoon.com/tagged/development)工具集，我将在来年继续使用。
请注意，这个列表并不详尽，只包括我今年发现的或者我在工作中发现的一些非常有用的[工具](https://hackernoon.com/tagged/tools)和库。

# 工具

*   我不知道为什么有些人鄙视 [SourceTree](https://www.sourcetreeapp.com) ，但是我发现它对管理我的 git repos 真的很有用，目前是我最喜欢的 git 工具。它有我需要的一切。
*   [Visual Studio 代码](https://code.visualstudio.com):在过去的一年里，我尝试了很多编辑器，从 Sublime 到 Atom，从 TextMate 到 TextWrangler，但是我发现 VS 代码符合我的需求。并不完美，但它很好地结合了实用性和速度。
*   Postman :我们在 Dottori.it 的开发团队中使用它，它对于通过测试、文档和共享来构建 API 请求非常有帮助。
*   我真的很喜欢它。它保留从剪贴板复制的数据，并允许通过快捷方式轻松访问这些数据。这大大增加了您的日常复制/粘贴工作流程，我的工作效率也更高了。

# 图书馆

*   [PromiseKit](https://github.com/mxcl/PromiseKit) :我加入 Dottori.it 的时候就开始用了，现在每一段异步代码都是和 PromiseKit 结合使用。
*   [PMSuperButton](https://github.com/Codeido/PMSuperButton) :是我在 2017 年期间写的 UIButton 的一个包装器。它允许你创建自定义和复杂的按钮与自定义属性，直接添加到 iOS 界面生成器，非常容易集成到每个项目。
*   [JTAppleCalendar](https://github.com/patchthecode/JTAppleCalendar) :在[agenda doctori](https://www.dottori.it/app-agenda-gestione-studio-medico)的开发过程中，发现它真的很有帮助。这是一个超级可定制的日历，可以满足每个人的需求。
*   ObjectMapper :是一个库，允许你将模型对象(类和结构)与 JSON 相互转换。我从 Swift 2 开始使用它，现在在支持[的 Swift 4 中，可编码的](https://developer.apple.com/documentation/swift/codable)不太相关，但已经很有用了，因为它支持很多特性，比如自定义转换。
*   [Couchbase Lite](https://github.com/couchbase/couchbase-lite-ios) :在新版本的 AgendaDottori(在 Swift 4 中从头开始重写)中，我们需要一个移动数据库来存储日历中的所有事件。潜在的大量数据。我尝试了 CoreData 和 Realm，但在那之后我看到了 Couchbase Lite 2，字面意思是“在移动设备上本地运行的全功能嵌入式 NoSQL 数据库”，我开始使用它，它满足了我的所有需求。
*   我用它在调试模式和我的单元测试中生成假数据。真的很有帮助，有很多可以生成的数据。
*   [toast](https://github.com/devxoul/Toaster):像 GitHub 页面描述是一个类似 Android 的 toast，界面非常简单。用它来通知用户一个动作是非常有用的。
*   [SwiftDate](https://github.com/malcommac/SwiftDate) :最后但并非最不重要的一点，我必须提到(对我来说)在 Swift 上管理日期的最佳库。是由我的朋友 Daniele 创建的，我在所有的 iOS 项目中都使用它。

# 网站和服务

*   [one signal](https://onesignal.com):2017 年 1 月，mobile SDK 发布后，我开始做[customely](https://www.customerly.io)的手机 app，这是一家和一些朋友在爱尔兰推出的创业公司。我们的移动应用程序的一个主要功能是推送通知，这对于希望从移动设备提供客户支持的用户非常有用。在我体验了 Firebase 云消息(这不完全符合我的需求)之后，我们尝试了一个非常特别的信号。他们提供实时分析、无限推送通知、交付自动化、本地化、完整 API、无限分段、A/B 测试等等。太神奇了。
*   在 Dottori.it 中，我们使用了很多深度链接，当我们需要创建一个新的链接时，我们通常会使用 Firebase。AgendaDottori 的问题是 Firebase 只为项目提供了一个链接，而 Firebase 的项目包括两个应用程序，这就存在冲突。然后，今年我们尝试将深度链接引擎迁移到 Branch.io 上，branch . io 是一种提供领先移动链接平台的服务，提供跨不同设备、平台和渠道统一用户体验和测量的解决方案。简单有效。
*   [语法](http://grammarly.com):英语不是我的母语，我用英语写作时并不完美，尤其是当有很多东西要写的时候。这项服务对我提高英语写作质量和纠正一些错误帮助很大。
*   [独立黑客](https://www.indiehackers.com/businesses):这是一个网站，每周你都可以读到一篇关于一个人的新采访，这个人从副业项目开始创业。我真的很喜欢它，因为它打开了我的心扉，让我看到了一些我没有考虑到的方面，并且有助于了解他们在开始的每一项业务中赚了多少钱。是鼓舞人心和激励人心的。

# 额外:有用的文章和链接

*   [网络开发者路线图](https://github.com/kamranahmedse/developer-roadmap)
*   [乔尔·斯波尔基语录](https://quotefancy.com/joel-spolsky-quotes)
*   [如何在 Swift 🕷制作网络爬虫](/swiftly-swift/how-to-make-a-web-crawler-in-swift-3ed4977a181b)
*   [Web 开发人员安全清单](https://simplesecurity.sensedeep.com/web-developer-security-checklist-f2e4f43c9c56)
*   [PMAlertController](https://github.com/Codeido/PMAlertController)
*   [史诗般的 SaaS 创业指南](/@cliffordoravec/the-epic-guide-to-bootstrapping-a-saas-startup-from-scratch-by-yourself-part-1-4d834e1df8c1)
*   [工程师营销](https://github.com/LisaDziuba/Marketing-for-Engineers)

## 倾听:

*   通过想象龙悬浮起来
*   杀手乐队的布莱特赛德先生
*   这是邦·乔维的《我的生活》
*   喷火战机乐队的《永恒》
*   红辣椒乐队的《桥下》
*   老鹰加州酒店

# **感谢**你**阅读**！我希望你和我一样喜欢这篇文章🤠

如果你想在社交媒体上关注我，这里有一些链接。 [*github*](https://github.com/Codeido) *，*[*Twitter*](http://twitter.com/pmusolino)*，*[*LinkedIn*](https://www.linkedin.com/in/paolomusolino/)

去年我读了 Durul Dalknat 的一篇有用的帖子，今年我想创建我的个人列表。