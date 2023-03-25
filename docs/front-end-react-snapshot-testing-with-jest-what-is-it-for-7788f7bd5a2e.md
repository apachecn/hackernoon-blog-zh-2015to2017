# 用 Jest 进行前端(反应)快照测试:是为了什么？

> 原文：<https://medium.com/hackernoon/front-end-react-snapshot-testing-with-jest-what-is-it-for-7788f7bd5a2e>

从 14.0 版本开始，Jest 增加了一个新的[快照](https://hackernoon.com/tagged/snapshot)特性。有一篇博客文章宣布了这一特性，并解释了脸书实施这一特性的[原因](https://facebook.github.io/jest/blog/2016/07/27/jest-14.html#why-snapshot-testing)。但我觉得还是根据自己的经历去探索比较好。

![](img/fbd92b9e35559f6dbea4ceef7a204c79.png)

首先，重要的是要强调每种类型的测试都是不同的，都有特定的目的。**例如，单元测试**验证软件的每个单元都按照预期执行。集成测试确保这些单元能够很好地协同工作。**用户界面测试(端到端测试)**从用户角度测试应用程序界面。

还有一些技术，像**测试驱动开发** (TDD)定义了“何时应该测试”(或者在编码前编写测试)，以及**行为驱动开发** (BDD)接近“你为什么编码”(或者编码前的行为和规格)。

# **回归测试**

**快照测试**与之前的测试类型和技术无关。快照有助于确保某事物的*状态在未来不会改变。所以，不可能用快照测试来进行 TDD 或 BDD，因为你应该先写代码，后测试。*

快照测试是**回归测试**的一种，旨在验证之前开发和测试的软件在更改后是否正确运行。

# **测试声明性接口和集成**

下面的*购物车物品*组件示例经常被测试覆盖。让我们考虑一下。但是首先，请确保您意识到代码是声明性的，没有任何类型的逻辑。

考虑和证明声明性代码的测试是困难的。正如本文所指出的，测试声明性代码的任务以测试声明本身的功能而告终。但是可读性和意图较差。的确，**声明性代码实际上是形式规格说明**。

BDD 测试用例确保*数量*和*图像*出现在界面上是公平的。这两个资产是特性规范的一部分:*购物车条目*应该有图像和数量。

此外，用快照测试来覆盖这些是没有意义的。当你在将来改变它的时候，你故意这样做。但另一方面，*购物车*(或其他组件)可以**快照其与*购物车项目*的集成。可能是改了*购物车物品*的开发者不知道*购物车*用的。**

# 遗留的和测试不良的代码

依我拙见，一个具有遗留代码库的项目应该是最适合快照测试的。使用这种类型的测试，很容易创建最小的测试代码覆盖率，而不会浪费单元测试的时间。

看一看[一个对使用](https://github.com/jcemer/minimalistic-shop/commit/78679a5220b90de82b4ae30eb228aa1360c04352)[反应](https://hackernoon.com/tagged/react)和[酶](https://github.com/airbnb/enzyme)的应用程序进行快照测试的例子。注意，我使用了`render`酶法，而不是`shallow`或`mount`，因为我打算测试最终的 HTML 结果。

如果你正在做这样的项目，我祝你好运，也建议你看看类似于 [PhantomCSS](https://github.com/Huddle/PhantomCSS) 的自动化**可视化回归测试**的工具。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)