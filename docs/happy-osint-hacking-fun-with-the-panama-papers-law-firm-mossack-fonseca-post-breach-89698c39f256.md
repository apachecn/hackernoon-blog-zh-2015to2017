# 巴拿马文件的律师事务所，莫萨克丰塞卡后违规快乐 OSINT 黑客乐趣

> 原文：<https://medium.com/hackernoon/happy-osint-hacking-fun-with-the-panama-papers-law-firm-mossack-fonseca-post-breach-89698c39f256>

![](img/c11adad5e54bf73fa47fd0bfaeda61aa.png)

我参与巴拿马文件是出于好奇。最初，我在一次核 ICS 安全会议上提交了一份报告，我打算在会上展示 OSINT 技术如何暴露核设施。演示应该有趣和令人兴奋，尤其是在工程师会议上。在它被接受的一两天后，达伊沙开始威胁欧洲核设施。我没有给出 ISIS tw*ts 可以使用的方法，而是把目标转向了巴拿马文件惨败背后的律师事务所 Mossack Fonseca。在第一次关于巴拿马文件泄露的新闻曝光后，我决定四处打探一下，准备好研究数据。最初，我打算让我的发现更加保密。然而，在 2016 年，我写了一份长达 40 页的详细报告，并泄露给了巴拿马报纸的一名记者。包含证明，一步一步的指示如何验证我的结果，创建帐户和数据库访问。希望有帮助。

这篇文章是调查结果的总结。

# 多金

Dorking 利用搜索引擎索引，Google，DuckDuckGo，Bing 等。，扫描和索引 web 信息。多金让你搜索他们已经扫描过的内容。被动侦察技术。我的第一次全面扫描，寻找任何文本文件 MossFon.com 是 bl**dy 惊人。Mossack Fonseca 客户端门户的 API，没有安全性。更改日志、系统配置。不仅仅是一页结果，而是两页谷歌结果。我在黑客天堂，OMG。

![](img/58ce0a725a3bee0bd9f7fd1f55571cf8.png)

Figure 1 General Dorking for text files on MossFon.com search results

# 阿帕奇版本什么？！

MossFon.com 有一台老旧过时的网络服务器，已经多年没有打补丁了。这个版本，Apache 2.2.15 很容易被利用。MITRE 的 CVE 细节列出了暴露在互联网上的版本的 47 个漏洞。更不用说[可用的两个 Metasploit 开发模块](https://www.cvedetails.com/metasploit-modules/version-93077/Apache-Http-Server-2.2.15.html)。还有其他问题，位于不安全、未加密端口上的 Oracle 数据库也暴露在互联网上。数据库是主要目标；它们包含信息，像>律师事务所泄露出来的 2600 GB 信息。我在 GPEN/OSCP 预科学校的渗透测试课程中教授这两种方法，YouTube 上有很多操作视频。

![](img/5541c68ab39c789bfaa7b3eac7a071dd.png)

Figure 2 MossFon.com error condition exposing system information

# Drupal 版本什么？！

Drupal，一个开源的客户管理系统(CMS)。一家跨国的、有影响力的、富有的律师事务所在处理一些客户和交易时，应该保持非常安全。早在 2014 年 10 月，Drupal 发布了一份[高度关键的安全建议](https://www.drupal.org/forum/newsletters/security-public-service-announcements/2014-10-29/drupal-core-highly-critical)。脆弱性等级中最危险的类型是临界；Drupal 声明这超出了这个范围。世界末日，完全控制，远程，简单，网站通过自动化工具和后门妥协。

![](img/33ba38d8bb53285aba1192e9239733ac.png)

Figure 3 Snippet of the Drupal Highly Critical public service announcement

Drupal 的变更日志被暴露在互联网上，显示这家富有的律师事务所从未更新过他们的 Drupal，也没有修补过危险的状况。这家律师事务所坚韧不拔，让他们的一名 IT 员工在一个欧洲国家被捕，试图将部分责任归咎于此人的失职。我花了很长时间才找到他的律师，并寄了一份报告。几年前，我是一名网络管理员，试图保护一家全球工程公司的系统和日常运营。在与首席财务官和其他高管会面后，他的秘书把我拉到一边私下交谈。她说，“戴夫更希望你在会议中不要再提防火墙这个词。他不知道这意味着什么，让他看起来很糟糕。”信息安全的缺失是从高层开始的。

![](img/66b3aa8e510a5c1f66cd02120d02d23b.png)

Figure 4 MossFon.com Drupal change log showing the installed version was below 7.32

Drupal 使用各种模块来提供特性和基本功能。Mossack Fonseca 安装的未打补丁版本的其中一个模块是调度程序和调度程序测试。这个模块不仅是公开的，而且允许设置一个管理帐户😊从这里，权限升级到完全管理是可能的。

![](img/01d9c197f9406c22e6791056b8f49c62.png)

Figure 5 MossFon.com Client Portal scheduler admin for testing

Shodan 建议为客户门户购买一个捕鼠器！

Mossack Fonseca 使用客户端互联网和 API 可访问的门户。在这里，客户可以登录、查看发票、付款、买卖股票。在某些情况下，空壳公司拥有大量股份。在我进行研究的时候，MossFon.com 和客户端门户托管在 192.230.92.15，一个位于美国特拉华州多佛的 IP 地址。使用不同类型的搜索引擎来索引系统信息。MossFon.com 的网络服务器有许多开放的端口和服务，并暴露在互联网上。更像瑞士奶酪的网络服务器。暴露了 37 个面向互联网的端口和服务。

![](img/721d22b21882ed05b23b9003c7edf0f6.png)

Figure 6 Shodan scan result showing ports and services exposed to the internet on MossFon.com

众所周知，其中一些港口像双曲棍球棒一样危险。6666、444、81 等端口。Shodan 可以报告在端口上运行的应用程序的确切名称，如果该应用程序广告它自己的话。大多数人都会表明自己的身份，甚至是犯罪软件或远程访问特洛伊木马(RAT)。深入调查可疑港口，我发现了鼠患。至少有八只老鼠，但我需要为我在核会议上的报告做总结。对于幻灯片，我添加了与 Mossack Fonseca 客户端门户上运行的 rat 相关的新闻故事。不错，多汁的罪犯和政府级。当时，荷兰人是唯一被法律允许回黑或黑任何东西的人，但老鼠似乎不是他们的风格。这让我想知道是哪些政府安装了老鼠？好消息是，大多数老鼠是不安全的，这相当于双倍快乐的黑客微笑😊 😊

![](img/bccc0987d92d2352bd5edc8d68dbbc28.png)

Figure 7 Four of the RATs installed on MossFon.com with related news information

# 这是一个包含超过 2600GB 数据的可利用的存档服务器，还是您的 web 服务器很高兴见到我？

我最喜欢的一个工具是 Maltego，一旦装载了定制模块和 API 连接器，这个工具就棒极了。然而，我没有使用商业版，而是选择了 Kali 附带的免费社区版。MossFon.com 的预算剥削，这似乎是合适的。其中一个转换和设置产生了一个令人兴奋的子域，它包含了很多很多完全未加密的文档和文件。试图使用名为 FOCA 的 Windows 工具快速下载时，FOCA 崩溃了。第二天去做手术时，在我泄露了报告并出院后，我用了不同的方法。

![](img/e0eac2d2b582925a81b5d3ae673b545e.png)

Figure 8 Maltego scan of MossFon.com’s Crypt server

莫萨克·丰塞卡如此软弱，提供了如此丰富的信息，我和其他一些目标，川普，共和党，UKIP，民主党全国委员会，格特·威尔德斯，荷兰和德国选举委员会系统一起变成了一本书。最初泄露的技术报告和一份编辑过的、更加完美的副本将很快发布到 [Peerlyst](https://www.peerlyst.com/users/chriskubecka#!) 上。