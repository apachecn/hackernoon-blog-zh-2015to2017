# ADA 合规性和您的网站—您真的应该担心吗？

> 原文：<https://medium.com/hackernoon/ada-compliance-lawsuits-and-your-web-presence-797ef03cdef2>

![](img/9b19cd97743827e6bcc93f3e2cb3d539.png)

Nobody wants to end up here, aside from the lawyers maybe…

*2018 年 12 月 31 日更新——注意，这篇文章写于 2017 年 7 月，这意味着按照网络标准，它已经过时了。我会尽快花些时间更新这篇文章，但在此之前，我建议你与其他来源交叉检查这里的陈述。*

[温-戴茜最近面临并输掉了一场 ADA 合规诉讼](https://www.forbes.com/sites/legalnewsline/2017/06/13/first-of-its-kind-trial-goes-plaintiffs-way-winn-dixie-must-update-website-for-the-blind/#132175111b38)，这让[有些](http://www.fooddive.com/news/grocery--winn-dixie-case-may-compel-retailers-to-make-their-websites-ada-compliant/445700/) [好看](http://www.law.com/sites/almstaff/2017/06/22/how-a-miami-court-ruling-could-affect-ada-compliance-nationwide/?slreturn=20170528123910) [轰动](http://www.natlawreview.com/article/ada-digital-age-federal-court-strikes-down-inaccessible-website) [头条](http://www.lexology.com/library/detail.aspx?g=99ee0a42-ba7c-4902-b162-d4f7eec346dd)。网站可访问性现在是一个热门话题，所以每个对某人来说出错的案例必然会导致一些相当狂热的头条新闻。但是你真的应该担心吗？简而言之——也许吧。

因此，让我们尽量让 ADA 合规性、谁需要担心以及您可以做些什么变得尽可能简单易懂。完全披露——我不是你的律师，这里没有任何法律建议。

# ADA 合规性是什么意思？

ADA 是 1990 年成为法律的《美国残疾人法案》的缩写。它禁止在公共生活的所有领域歧视残疾人。《反倾销协定》,至少是第三篇(私营部门企业),只适用于雇用 15 人以上的公司。

2018 年 1 月，一些新的联邦法规将生效。到目前为止，所有联邦机构的网站在 [WCAG 2.0](https://www.w3.org/TR/WCAG/#text-equiv) 中的所有项目上都必须符合 AA 标准。我们稍后会讨论这意味着什么。

# 为什么 ADA 合规性突然变得更重要了？

法律先例正在改变，与 ADA 合规性相关的诉讼变得越来越成功，法院也因此看到了更多这样的案例。《美国残疾人法案》第三章涉及私营企业。最近，随着网络和移动应用在我们的日常生活中变得越来越必要，这些保护措施正在更频繁地扩展到数字领域。

# 谁需要合规？

现在普遍的共识是，任何被认为是“公共场所”的企业都应该有一个符合 ADA 的网站。

“公共通融”可以适用于大多数事情，这取决于由谁来解释。然而，一般来说，这将是指 B2C，零售，或任何业务，一般公众应该能够使用，理解和访问容易。

在法院认为该网站与实体店的结合过于紧密后，对温-迪克西的判决被确定。这可能是因为他们在网站上投放了每周广告。

# 我需要做些什么才能合规？

为什么这么简单，只要遵循 WCAG 2.0 中列出的所有 [61 条指导方针就可以达到 AA 或 AAA 级！](https://www.w3.org/TR/WCAG/#text-equiv)

听起来吓人吗？没有看上去那么糟。你的网站可能已经满足了许多这些规则，其他的只需要一个网站开发者几分钟就可以达到标准。但是，根据具体情况的不同，有些项目很难修复。

*   文本必须满足相对于背景的最小对比度，这会显著影响您的设计。
*   你的网站必须完全可以通过键盘导航。这通常包括跳过导航按钮之类的东西，并且可能涉及到在任何地方手动设置 tabindex。
*   你的网站应该可以用屏幕阅读器软件导航。这可能很难测试，并且可能涉及一些艰巨的修复，类似于键盘导航所必需的。
*   你的网站必须处理高达 200%的文本缩放，而不会导致水平滚动或内容中断的布局问题。同样，在一些复杂的设计中，这可能更难解决。

# 我如何检查所有这些？

多种软件可用于测试 ADA 合规性。

*   [WAVE](http://wave.webaim.org/) 是一个好的开始，但是会产生很多误报，尤其是对比度问题。
*   [Lighthouse](https://developers.google.com/web/tools/lighthouse/) 可以帮助生成关于潜在问题的报告。
*   使用此[计算器](http://leaverou.github.io/contrast-ratio/)手动测试对比度。
*   使用屏幕阅读器软件进行手动测试
*   仅使用键盘导航的手动测试

自动化工具将捕捉许多简单的问题，但是如果你想确保满足需求，几乎所有的网站仍然需要手工测试。

希望这能让你对情况有所了解，以及这对你的企业意味着什么。或者，如果你是一名网站开发人员，如何主动帮助你的客户。

让我们让世界变得更美好，一次一个合规网站。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)