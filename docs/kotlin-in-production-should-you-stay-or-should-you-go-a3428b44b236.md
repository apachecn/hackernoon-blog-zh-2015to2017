# 生产部的柯特林:你应该留下还是离开？

> 原文：<https://medium.com/hackernoon/kotlin-in-production-should-you-stay-or-should-you-go-a3428b44b236>

![](img/a5ca305be49d6a4233b8b513369abea9.png)

Source: [https://www.flickr.com/photos/nbabaian/13591249654](https://www.flickr.com/photos/nbabaian/13591249654)

在[安卓](https://hackernoon.com/tagged/android)世界[科特林](http://kotlinlang.org)似乎无处不在。很难去参加一个关于 Android 的会议或者阅读一篇关于 Android 的博客而不提及 Kotlin。我记得在去年的柏林 Droidcon 上，我交谈过的大多数人都已经开始在生产中使用 Kotlin(两个月后，我发布了我的第一个应用程序更新，包括我自己的 Kotlin)。事实上，科特林在安卓社区的影响力比在 T4 Java 社区要大得多。我敢肯定，即使是喷气大脑也对此感到惊讶。
但这是有道理的:自从 Android 诞生以来，我们开发者就被一种过时语言的过时版本所束缚，而其他平台和语言也在进化。科特林让我们再次变得有竞争力。

# 停车标志

但有时开发商的热情会被利益相关者所抑制。尽管 Kotlin 在代码基础和速度上有很大的优势，但很有可能你会因为使用 Kotlin 而被拒绝。他们的一些论点很容易被驳回，而另一些则有一定的道理。

让我们看看一些:

## 团队中的每个人都需要学习一门新语言

一个明显相关的论点。但好消息是:柯特林的 ***学习曲线******确实很低*** 。例如，RxJava 的学习曲线比 Kotlin 的要陡峭得多。
在开始的时候，您将使用 Kotlin 关键字编写 Java 风格，这种方式非常有效，如果有疑问，可以将复制和粘贴转换直接构建到 IDE 中。
但是你当然需要想办法让每个人上车。这意味着时间，时间对您的利益相关者来说意味着金钱。
开始时没有任何风险的好方法是测试。测试是试用新工具的安全环境。从迁移您的测试开始，或者至少在 Kotlin 中编写新的测试。实际上，Kotlin 带来了[很棒的特性](https://speakerdeck.com/dpreussler/kotlin-all-your-tests-codefest-dot-ru-2017)，甚至会改进你的测试代码。过一会儿，每个人都会知道 Kotlin 是如何工作的，然后您就可以进行下一步了。
对了: ***你的团队学习一门新语言是一件好事*** 。

## 很难找到有科特林经验的开发人员

实际上，大多数开发人员都渴望探索新事物，那些不愿意的人不是你应该考虑雇佣的人。于是，科特林就给应聘者提供了利用 ***增加*** ***雇佣*** 他们的机会。
我们在 Swift 上看到了这一点；已经很难找到喜欢用 Objective C 工作的人了，斯威夫特才 3 岁。问题是真实的。记住我们生活的这个世界:在这个世界里，我们需要招聘人员从现有项目中找到最优秀的人才。我们有比申请的开发人员更多的职位需要填补。用一项新技术挑战申请人是找到关心这项技术的人的好方法，这将推动你的产品向前发展。

## 科特林没有得到谷歌的支持

这是事实，但是 Android 依赖于字节码。来自 Kotlin 和 Java 的字节码最终会是同样的东西。我们只是在谈论我们编写代码所用的语言。Google 刚刚[解除了 Jack 工具链](https://android-developers.googleblog.com/2017/03/future-of-java-8-language-feature.html)并确认支持 javac 编译器对 Java8 的支持。所以这里有一个光明的未来。
这里你需要问的真正问题是，JetBrains 是否为 Android 备份了 Kotlin。令人高兴的是，他们完全忠于职守。这是一个巨大的成功故事，我们为此付出了很多努力。上次一只金丝雀(！)Android Studio 的构建破坏了 Kotlin 支持，第二天 Kotlin 插件中的修复可用。
编辑:其实谷歌自己已经在用 Kotlin 了！查看 Android 的[数据绑定编译器](https://android.googlesource.com/platform/frameworks/data-binding/+/master/compiler/src/main/kotlin/android/databinding/tool)。感谢ľuboš·穆德拉克指出这一点。

## 把 Kotlin 放在一个制作 app 里风险太大。

从 Kotlin 开始，该应用的运行时间没有任何风险。如前所述，当转换成字节码时，语言就结束了。也许你正在做的每一次库更新都比添加 Kotlin 更有风险，甚至更多的特性如空安全使你的代码库更加安全。Kotlin 也不再是一种新语言了。它已经发布了一段时间，变得稳定，刚刚发布了第一个特性。拥有大量用户的公司已经部分或全部转向 Kotlin。如果他们愿意冒这个“风险”，也许你也可以。

## 这只是另一种 JVM 语言

以前你可以用每种 JVM 语言编写 Android 应用程序，但是没有人这么做，为什么现在我们要这么做？我记得人们试图在 Android 上编写 Scala 应用，但这很痛苦，因为你需要提供运行时库。
Kotlin 是用 Android 设计的，运行时很小。您使用的大多数库都要大得多。 ***这个*** 现在有什么不同！

## 我们没有时间重写我们的应用程序

你不需要！Kotlin 和 Java 完美地结合在一起。你从 Kotlin 调用 Java，反之亦然。不需要像 ObjectiveC 和 Swift 那样的互操作或类似操作。所以你可以为你写的每一个类决定哪种语言更适合你。

## 一门新的语言会让我们慢下来

Bob 叔叔不久前写了一篇关于这个的文章:每一种新的语言都让我们回到过去，因为我们的工具链需要从头开始编写。看看 Swift，即使是现在，支持也不如 XCode 中的 Objective C 好。Kotlin 的伟大之处在于它来自一家工具制造商。它来自为我们带来 Android Studio 基础 IntelliJ 的人。IntelliJ 中的工具支持和 Java 一样好。实际上，Gradleware 开始为 Gradle 脚本支持 Kotlin 是件好事。原因是工具支持！Groovy，当前的语言，虽然已经存在了很多年，但是从来没有得到像 Kotlin 现在这样好的支持。

此外，所有使用字节码的代码检查工具都可以开箱即用。剩下的可能是直接作用于源代码的静态检查工具。这是一个小小的退步，但社区很快就会解决这个问题。与此同时，我们有 [Klint](https://www.google.com/url?q=https://github.com/shyiko/ktlint&sa=D&ust=1490979050558000&usg=AFQjCNGsBJX1qntkL-lCPLuh-80cD1_g9w) 作为替代，以确保我们继续交付高质量的代码。

# 那么，为什么我们的利益相关者犹豫不决呢？

移动成为大多数公司的核心业务。无论你在哪个地区，你的大部分客户都有可能通过这个应用找到你。但是在你的核心业务上，你倾向于小心变化，这是完全正确的。

## 变化，移动的本质

但是我们也要记住:移动开发是由持续的变化定义的。除了后端开发世界，事情一直在快速变化。在 Android 存在的几年里，我们至少有三种完全不同的用户界面:前赫萝、赫萝和材料设计。我们有三种不同的 API 来发送推送通知:C2DM、GCM 和 Firebase。就在几年前，传播最广的应用程序架构涉及一个内容提供商，它会对你隐藏 HTTP 调用。您的 REST 客户机(可能基于 Apache)总是将结果写入 SQLite 数据库。就在几年前，由于性能原因，依赖注入还是一种反模式。没有 ReactiveX，函数式编程停留在后台或者大学里。

如果说我们移动开发者学到了一件事，那就是变化是持续的。积木不起作用，因为当你把它们写下来的时候，它们可能已经过时了。一个 3 年没碰过的 app 可以扔了。这在 Android 上过去有效，现在仍然有效。尝试雇佣一个愿意开发一个仍然支持 Android 2 和/或使用上面提到的 ContentProvider 模式的应用程序的开发人员！祝你好运。

## 不要被落下

科特林只是这些变化中的一个。不要错过变化。谁知道呢，你现在保护的 Java 代码库，最终可能会成为我们所说的遗留系统。

PS:这个讨论火了，所以这里有一些翻译:
[巴西葡萄牙语](/@orogersilva/kotlin-em-produção-ter-cautela-ou-ir-em-frente-8df66491c8c6)
[中文](http://www.jianshu.com/p/272d3260dbf5)
[韩语](/@fwith7/kotlin-in-production-should-you-stay-or-should-you-go-bd21ff7a7fde)
[泰语](https://blacklenspub.com/kotlin-in-production-should-you-stay-or-should-you-go-b0bb71bf2418)

![](img/303e3191187a2dcde8d47cddd941fcb4.png)

Source: [https://www.flickr.com/photos/dandydanny/10032614756](https://www.flickr.com/photos/dandydanny/10032614756)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)