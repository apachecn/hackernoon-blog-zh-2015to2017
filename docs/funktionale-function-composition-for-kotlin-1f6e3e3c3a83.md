# funKTionale:为科特林创作的函数

> 原文：<https://medium.com/hackernoon/funktionale-function-composition-for-kotlin-1f6e3e3c3a83>

functionale 是一个为[科特林](https://hackernoon.com/tagged/kotlin)设计的函数库，它提供了几种功能模式和构造。

# 一点历史

我在 2012 年末开始研究 funKTionale 背后的想法，2013 年 4 月 12 日发布了第一个公开版本(0.1.5)(这是 JetBrains 之外的第一批 Kotlin 库之一)。最初的想法是让 Kotlin 体验更接近 Scala(我的日常工作包括编写 Scala 代码，最近还有[的 TypeScript](https://hackernoon.com/tagged/typescript) )但是它演变成了有自己身份和风格的东西

# 让我们编写一些函数

函数组合是将一个函数的返回应用到另一个函数以产生第三个函数。很简单。

funKTionale 为函数类型提供了扩展函数，实现了函数合成(喝游戏思路:本帖每个“函数”一个镜头)。

要使用它，您需要在您的 repos 上添加 JCenter:

然后 functional-composition(8Kb)到您的依赖项:

或者，如果你想要所有的好东西，GOTY 版，整个 enchilada，你可以添加 funkonionale-all(1372 kb):

对于我们的例子，一个正常而无聊的发票价格计算(我确信有更好的方法来计算发票价格，为了这个例子，请原谅我)。

因此，一个项目数据类和一个存根 ItemDao

现在，价格计算

所以，三次地图迭代:

*   从商品到一对价格和折扣
*   从价格和折扣对到价格和折扣百分比
*   从价格和折扣百分比对到价格

最后是求和运算。

现在，我们可以将这些函数移到 val 中，以获得更清晰的外观

仍然是三次地图迭代

现在，借助函数组合的强大功能，我们可以通过一次映射迭代将所有三个函数转换成一个函数(商品到价格)

但是，这又是什么呢？

funKTionale 提供了三个函数复合运算符

*   [](https://github.com/MarioAriasC/funKTionale/blob/master/funktionale-composition/src/main/kotlin/org/funktionale/composition/namespace.kt#L31)**:将第二个函数的结果作为第一个函数的参数。**
*   **[**forward compose**](https://github.com/MarioAriasC/funKTionale/blob/master/funktionale-composition/src/main/kotlin/org/funktionale/composition/namespace.kt#L23):将第一个函数的结果作为第二个函数的参数。**
*   **[**然后**](https://github.com/MarioAriasC/funKTionale/blob/master/funktionale-composition/src/main/kotlin/org/funktionale/composition/namespace.kt#L19) :是 forwardCompose 的别名。**

# **四人帮:油漆工**

**最近[Lovis ml ler](https://lmller.github.io/)写了一篇文章描述了一些 [GoF 模式在 Kotlin](https://dev.to/lovis/gang-of-four-patterns-in-kotlin) 上的实现。**

**马里奥富斯科正确地指出，装饰者更好的实现应该使用函数组合。**

**所以让我们用函数组合来实现 Lovis 的例子**

**所以，只有几个简单的方法**

**使用函数引用(::)，它们可以以几种方式组合。**

# **目前就这些了**

**在下一篇文章中，我将介绍其他功能特性，到时见。再见**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **要了解更多信息，请阅读我们的“关于”页面、[在脸书](http://bit.ly/HackernoonFB)上给我们点赞/发消息，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**