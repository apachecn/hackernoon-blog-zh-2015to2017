# 承诺不是代理

> 原文：<https://medium.com/hackernoon/promises-are-not-proxies-fd00751eb980>

## 试图澄清一些常见的误解

![](img/cca85079a944e065f1e2930682b6664c.png)

A couple, hooking their fingers as if they are making a promise

让我们用一句话概括一下 Promises 的主要目的:

> 将同步属性添加到异步代码

[大家可能都知道](https://blog.domenic.me/youre-missing-the-point-of-promises/)，之所以创建 Promises，是为了让异步函数的行为类似于同步代码，比如能够**返回值**和**抛出错误。**

让我们假设我们处在一个并行的世界中，IO 永远不需要 Promises，我们可以使用 [JavaScript](https://hackernoon.com/tagged/javascript) 中的同步代码从两个资源中检索数据，而不会出现现有 API(如同步 XHR、`localStorage`、`readFileSync`等)的任何性能缺陷。

一个资源包含足球比赛，而另一个资源包含足球结果。我们希望将数据结构转换为包含所有足球比赛和结果的单个数组。

请注意，对于本示例，我们希望使用命令性逐行代码依次加载一个请求**，而不是并行加载**。`Promise.all`已出桌。****

****在理想情况下，代码如下所示:****

****A code example showing the fetching of soccer matches and soccer results using an imperative style****

****非常简单，它从资源中获取`soccerMatches` ，将数据与`soccerResults`相关联，然后在控制台中打印分数和匹配名称。****

****但这不管用。****

****不幸的是，在撰写本文时[，我们无法编写这样的代码。我们需要做些别的，不同的事。承诺的意义就在这里:](https://jakearchibald.com/2014/es7-async-functions/)****

****A code example showing the fetching of soccer matches and soccer results using the Promises style****

****正如您在这里看到的，同步代码几乎是相同的。不同之处在于，我们返回的不是来自`fetchSoccerMatches*` 和`*fetchSoccerResults**`的最终值，而是一个[承诺](https://hackernoon.com/tagged/promise)，它将在稍后用所需的值进行解析。注意，在代码被改为使用 Promises 之后，**中的** `**soccerMatches**` **变量被重命名为** `**fetchSoccerMatches**` *。*原因是变量不再保存数据，而是保存一个概念，即数据将在以后出现的“承诺”。****

****如何命名变量的区别非常重要。****

****如果你在一个变量中保存了对一个承诺的引用，并一直把这个变量当作已解析的值，那么下一个将阅读代码的开发人员将很难区分**期望值**和**期望得到那个值**。这违反了最小惊奇原则，即“系统的一个组件的行为方式应该与该组件的用户可能期望它的行为方式相一致”。因此，在这种情况下，当处理承诺时，为了使意图清晰，命名非常重要。****

> ****将承诺引用视为已解析的值，将很难区分预期值和获得该值的期望。****

****使用变量名来对待值，就好像不管有没有承诺它们都是一样的，这是一个错误。虽然一个[代理](https://sourcemaking.com/design_patterns/proxy) **不会改变一个已有组件的接口**并且可以被视为你正在使用原始组件，但是**一个承诺会改变**。****

****换句话说，承诺不是代理。****

****当使用小代码库工作时，上面的问题并不清楚，只有当您开始使用更大的系统时，其中有已经从异步数据源检索的变量和应该稍后检索的其他变量的混合。根据上下文，你不能通过阅读代码来判断变量是承诺还是解析值。这可能是危险的，因为它可能会通过诱导审查者将 [duck typed](https://pt.wikipedia.org/wiki/Duck_typing) 变量解释为正确的解析值而使代码审查中的错误不被注意到，而实际上它并不是。****

****当你在阅读别人(甚至是你自己)写的代码时，你不需要理解代码中发生的事情的整个上下文。一小段代码应该能够清楚地表达一小部分业务逻辑，而不需要太多的努力。****

****考虑到这一点，让我们以上面的例子为例，断章取义。您唯一需要知道的是，代码所在的模块会创建一个包含正确结果的匹配列表:****

****A subset of the first code example showing the fetching of soccer matches from a resource using the Promises style****

****`soccerResults`是承诺还是解决的价值？你应该从这个名字推断出是哪种类型？****

****[给事物命名是困难的](http://martinfowler.com/bliki/TwoHardThings.html)，但是将 Promise 对象视为用[代理](https://hackernoon.com/tagged/proxy)模式创建的对象是误解的表现，这可能导致不容易被发现的错误。****

****不要认为承诺应该像最终数据结构一样对待，它们只是解决 JavaScript 中异步数据处理效率低下的实用工具。****

****感谢阅读。如果你有一些反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。****