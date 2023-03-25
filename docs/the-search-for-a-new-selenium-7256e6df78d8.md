# 寻找新的硒元素

> 原文：<https://medium.com/hackernoon/the-search-for-a-new-selenium-7256e6df78d8>

功能性 web 应用程序测试领域正在发生一些根本性的变化。让我们把它归结为:

这个领域长期以来一直由硒主导。仔细想想， [Selenium](https://hackernoon.com/tagged/selenium) 确实是一套令人惊叹的[工具](https://hackernoon.com/tagged/tools) (Selenium web driver，Selenium IDE 等)，但是…

## 如果一个产品有一个弱点，总会有人试图向你出售可以修复它的东西。

硒怎么了？没什么大不了的，但是随着开发人员对他们的工具期望越来越高，Selenium 的困难越来越多:

***开发者期待好的调试:***

像 Chrome devtools 这样的工具在应用程序的错误和状态方面提供了非常好的可视性。即使你不在前端，普遍的共识是人们正在习惯好的调试工具。

Selenium 运行在与驱动它的代码完全不同的进程上，它只能向您发回失败日志(或许还可以截图)。

***开发者期望更快的反馈循环:***

能够更快地重启/重新运行一个系统能够快速地给开发效率带来回报。有些人喜欢 nodeJS 的原因是因为它的启动速度比 Java 快一百万倍(夸张)。此外，进入 web 应用程序开发是因为 CMD-r 的存在！

Selenium 的反馈循环极其缓慢，因为每次测试都要启动一个浏览器！那太慢了。

# 那么，人们想卖什么呢？

该社区正在进行一些实验，最具突破性的是使用技术来完全避免 Selenium，方法是在页面中注入一些触发动作和做出断言的脚本。这利用了浏览器工具的强大功能来进行调试和更快的反馈循环。

1.  Cypress.io
2.  测试咖啡馆

(如果您知道更多信息，请发表评论，我会将其添加到列表中)

## 这些新工具会流行吗？

这些工具权衡了 Selenium 的优缺点。这完全取决于开发者和团队更看重什么。令人惊讶的是，Selenium 具有跨浏览器的特性，并且一直支持 IE8……但是这真的很快会有问题吗？

调试和反馈循环的速度和价值对你来说比用你选择的语言编写测试更重要吗？

新技术的母公司能够平衡商业和社区，并创造一个足够开放的产品以获得信任和采用吗？(如果公司/项目死了，你还能继续使用代码吗？)

像往常一样，看看我的新工具，帮助你在[https://www.snaptest.io/](https://www.snaptest.io/)生成/组织你的硒测试

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！