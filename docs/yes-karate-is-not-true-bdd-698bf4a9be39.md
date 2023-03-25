# 是的，空手道不是真正的 BDD

> 原文：<https://medium.com/hackernoon/yes-karate-is-not-true-bdd-698bf4a9be39>

![](img/a9ceef0e98fdfc62ed9095975f5a2a8b.png)

Ryan Hayashi; a Japanese Karate Association certified 3rd Dan black belt [in action](http://www.mixedmartialarts.com/vault/karate/blind-folded-samurai-master-has-incredible-skills). Some cucumbers were harmed.

空手道开源还不到两个月，特别是感谢[乔·科兰托尼奥](https://www.joecolantonio.com/2017/03/23/rest-test-tool-karate-api-testing/)和 [T. J .马赫](http://www.tjmaher.com/2017/03/new-automation-framework-released-to.html)的博客帖子——反响非常好。感谢 Joe 和 T. J .给了这个全新的开源项目一个急需的能见度提升！

直奔主题。我想解释一下空手道和[黄瓜](https://cucumber.io)以及 BDD 的关系。随着空手道被越来越广泛地采用，我预计这将是一个非常常见的问题——这篇博客旨在成为我从现在开始可以向人们提供的权威参考。

# 为什么是黄瓜

> 编辑/ Jan-2019:从 0.9.0 版本开始，空手道*不再是基于黄瓜的*([见为什么](https://github.com/intuit/karate/issues/444#issuecomment-419852761))——而是保留了语法。如果你正在考虑使用 BDD 进行 API 测试——你真的应该读一读我在 Stack Overflow 上发表的文章。

在黄瓜 JVM 之上建立空手道并使用小黄瓜 T21 标准的决定被证明是一个伟大的决定。原因如下:

*   免费的 IDE 插件同时适用于 [Eclipse](https://cucumber.io/cucumber-eclipse/) 和 [IntelliJ](https://www.jetbrains.com/idea/help/cucumber.html) 。使用语法着色，脚本看起来更加引人注目，最棒的是——您可以“右键单击”并运行测试，而无需编写一行 Java 代码。我真的认为这是一笔大买卖，让非程序员更容易接触到 web-API 测试。甚至 GitHub 也内置了对语法着色的支持，[看看文档就知道了](https://github.com/intuit/karate#the-cucumber-way)——我不会轻易放弃它。
*   在幕后，Cucumber-JVM 提供了与单元测试框架(如 JUnit 和 TestNG)的现成集成，提供了开箱即用的良好报告，同样，空手道可以免费获得所有这些。有时候站在巨人的肩膀上似乎是不公平的。但这就是开源的美妙之处。
*   Cucumber-JVM 简化了解析脚本文件和将控制权交给定制 Java 代码的整个过程——而空手道可以专注于正确的“粘合”代码，完成 HTTP 调用和 JSON / XML 数据断言的重要任务。
*   而空手道免费获得 - **时** - *给定的【BDD 味】语法。稍后会有更多的介绍，但毫无疑问，这使得测试*更具可读性***。看看空手道[‘hello-world’的例子](https://github.com/intuit/karate#hello-world)，试着想象一下没有 BDD 语法的情况。很简单，就是不一样了。***

# ****空手道 BDD 也是如此？****

**空手道当然会给你使用 BDD 关键字的**选项**，例如给定的**，时的*，**然后是**，*和*。而熟悉黄瓜的人已经知道，当*或者**然后**的时候，你用**给定**还是*都没关系，它只是“*语法糖*”，幕后完全被忽略。*****

**但是回到问题上来。我不想给你讲 BDD 应该如何实际应用，因为这是 Cucumber 的创造者和一些固执己见的用户最好的表述。如果你正在考虑使用 Cucumber——甚至 BDD 那么考虑一下下面的博文吧。即使你*认为*你正在以正确的方式进行 BDD，我也强烈建议你阅读这些文章。你可能会感到惊讶。**

1.  **[世界上最容易被误解的协作工具](https://cucumber.io/blog/2014/03/03/the-worlds-most-misunderstood-collaboration-tool)——Aslak hellesy**
2.  **乔纳斯·尼可拉斯——你弄错了**
3.  **[为什么要费心检测黄瓜？](https://www.jackkinsella.ie/articles/why-bother-with-cucumber-testing) —作者杰克·金塞拉(这是对第二条的批判性回应)**

**我想在下面引用链接 3 中的一段话:**

> **Cucumber 有它的用途，主要是作为大型多语言项目的高级分析工具。也就是说，很少有程序员从事这种工作，除了常规集成测试的方法名称之外的验收测试似乎是一种浪费。大多数 Rails 程序员使用的 Cucumber 只不过是基本集成测试的笨拙包装器。**

****宾果**。我确信这将会引起很多人的共鸣，他们在某个时候使用过某种风格的 BDD，为现有的*系统编写测试。并且一直在想——*一个普通的集成测试难道就不够用了吗*？***

# ***如果空手道是黄瓜的“误用”，那就这样吧！***

***空手道坦然接受这样一个事实，即它是用于 HTTP web 服务的集成测试。它包含了大量的功能来简化这项工作。它直接与 web 服务 API 测试工具领域的[放心](http://rest-assured.io)之类的竞争——例如，看看这个[空手道与放心](http://tinyurl.com/karatera)的详细对比。***

***空手道尤其对创建和维护 web 服务的“平台”团队有意义，在那里产品所有者参与验收测试不是最高优先级的。***

# ***常见问题解答***

***偶尔，我会遇到一个“BDD 爱好者”,他看一眼空手道，就会大声说这是这个世界上令人厌恶的东西。我将用一个“常见问题”部分来结束这篇博客，试图与这些人讲道理，但我担心在许多情况下这是一个失败的事业——因为根深蒂固的认知失调。但不管怎样，我来了。***

## ***你不就是用黄瓜来炒作 BDD 吗？***

***不。有很好的理由，请看上面的“为什么是黄瓜”一节。***

## ***这违背了 BDD 和小黄瓜的全部目的！脚本中有如此多的实现细节。BDD 应该是可读的、可执行的规范！***

***如果有一件事空手道做得很好，那就是它是 HTTP、JSON 和 XML 的“t [rue DSL](https://ayende.com/blog/2984/dsl-vs-fluent-interface-compare-contrast) ”。DSL 的全部目的是将普通编程语言中的多行代码简化成一行代码。***

***但是，是的，如果你担心空手道看起来像一种编程语言，它确实是，而且是通过设计的。值得重复的是，对于希望练习 ***真*** BDD 的队伍来说，空手道是**不是**。***

## ***空手道的目标用户是谁？它显然不适合非技术业务人员。它看起来像一个抽象概念，所以这将适合技术人员吗？***

***是的，空手道是为需要测试网络服务的技术人员准备的，它是*激光聚焦的*尽可能使这变得**简单**。创建空手道的动机首先是为了避免使用 Java 代码来实现 web 服务，因为存在着明显的阻抗不匹配。这值得我写一篇完整的博文，我希望将来能这样做。***

***空手道还[添加了 Cucumber](https://github.com/intuit/karate#cucumber-vs-karate) 中缺少的一些特性——这些特性可能不属于 BDD 环境，但是从集成测试的角度来看是非常需要的。***

***有一种观点认为，一些语言(例如 [Python](https://hackernoon.com/tagged/python) 和 Ruby)可以在某种程度上减少阻抗不匹配，但这是有争议的——特别是因为空手道已经将 JSON 和 XML [“原生地”](https://github.com/intuit/karate#validate-every-element-in-a-json-array)融入到语法中。无论如何，我们已经看到开发人员和质量工程师以比以前高得多的速度创建 API 测试自动化。空手道的一个伟大之处在于它是语言中立的，不会因为它的设计而牺牲力量或表现力。***

## ***我还是不相信。你也在用空手道污染 BDD 的世界，你会把 BDD 的新手引入歧途。***

***好吧，就把这篇博文看作是我试图引导用户走上正确道路的一种方式。我也将显著地链接到这个博客——来自空手道文档。***

***也就是说，看看引用的博客帖子，以及多年来，大量程序员陷入了错误使用 Cucumber 进行集成测试的陷阱。这可能会持续一段时间，即使空手道不存在。***

***最后，在你尝试之前，不要挑剔它！我绝不是没有偏见的，但是我强烈建议你试试空手道，或者让你的团队来评估一下。我相信，随着时间的推移，你会找到一些好的东西来谈论它。***

***[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)******[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)******[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)***

> ***[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。***
> 
> ***如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！***

***![](img/be0ca55ba73a573dce11effb2ee80d56.png)***