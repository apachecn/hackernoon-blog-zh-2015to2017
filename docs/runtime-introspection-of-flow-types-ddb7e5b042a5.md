# 流类型的运行时自省

> 原文：<https://medium.com/hackernoon/runtime-introspection-of-flow-types-ddb7e5b042a5>

感谢飓风马修席卷哥伦比亚，我不得不利用我的时间做一些有意义的事情。所以很自然地，我一直在考虑类型系统，特别是 [JavaScript](https://hackernoon.com/tagged/javascript) 的 **Flowtype** 。

我们的团队在 Red Badger 使用流类型已经有一段时间了，我们已经看到了一些真正的好处。也就是说，更健壮的代码更容易、更安全地重构，并且由于使用了优秀的核素 ide 工具，生产率显著提高(感谢脸书！).

不参与静态和动态打字的争论，我相信**渐进打字**提供了自信和灵活性的极好平衡。我看到它在一个大的(重要的)JavaScript 代码库上有效地工作。

# 灵感

昨天我在 clojure.spec 上看了一场精彩的《奇兵》脱口秀:

Stuart Halloway’s Talk on clojure.spec

对于那些不熟悉的人来说，它是动态类型语言 Clojure 的规范语言。它允许开发人员指定函数期望的一些数据的形状，并指定在执行之前或之后应该满足的一些不变量。它似乎受到了契约式设计的启发，并且与 T10 型系统提供的保证有一定的重叠。

然而，clojure.spec 背后的强大思想是这些规范的执行完全由开发人员决定。尽管有一些内置的实用功能的帮助。为此，规范必须在运行时可用**。**

静态类型语言通常只在编译时使用类型信息。然而，像 C#和 Java 这样的语言也有在运行时访问类型信息的机制，作为“**反射 API”、**的一部分已经有很长时间了**。**

为了将来自 **core.spec** 的一些想法引入 JS，我着手为流类型化 JavaScript 构建一个类似的特性。

# 在运行时公开流类型

Flow 是用 OCaml 而不是 JavaScript 编写的，类型检查发生在开发过程中的 Flow 流程中。这意味着 JavaScript 无法访问类型信息。

此外，Flow 的用户通常通过 Babel 运行他们的 JS 代码，其中 **flow-strip-types** 删除任何注释并输出可执行代码。为了保留类型信息，我们可以用 **flow-runtime-types、**我为此编写的一个小转换库**来代替这个插件。**

**flow-runtime-types** 将我们的流注释的 AST 暴露给运行时代码，然后我们可以解释这个 AST 来构建一些有趣的特性。

AST 看起来像这样:

理解起来非常简单，并且足够低，可以构建一些利用它的工具。

# 用例

## 类型一致性验证

在任何系统的边缘，无论是否进行了类型检查，都有必要实现验证逻辑来确保数据处于正确的形式。当使用 Flow 时，将这些规则编码成类型系统的一部分，然后再编码成可执行代码的一部分，可能会觉得多余。

相反，我们可以利用流注释 AST 来产生数据的自动运行时验证。

我选择将流类型转换成 Joi 模式对象([https://github.com/hapijs/joi](https://github.com/hapijs/joi))，这样验证就可以由一个久经考验的库来处理。例如，使用上面的类型定义:

这并不意味着我们可以删除所有其他的验证逻辑，因为没有办法在 Flow 中编码任意的约束(例如，字符串的最大长度)。然而，它的确将我们从结构验证的单调中解救出来，因此我们可以转而关注于验证数据的**语义**。

我已经实现了这一点，作为 Flow 中基本类型和一般类型的概念证明，它似乎工作得很有效。我们当然也可以使用这种方法来完全取代 React 组件中的运行时 **propType 验证**。

## 为基于属性的/单元测试生成测试用例

我们还可以使用流 AST 来生成有效的 fixtures，以便在基于单元或属性的测试中使用。用法如下:

我们可以多次执行这个函数，为我们的系统产生随机但有效的输出。当应用于 UI [编程](https://hackernoon.com/tagged/programming)时，我们可以在**基于属性的测试**或**模糊测试**(如在[https://github.com/carteb/carte-blanche](https://github.com/carteb/carte-blanche)等工具中所见)中直接使用它。

# 局限性和改进

这种方法的一个限制是运行时自省只适用于**显式类型声明。**流的主要好处之一是类型推断。解决这一限制可以采用以下方法之一:

*   通过带有`— json '标志的 CLI 从流中请求类型化的 AST 信息。核素和其他工具使用这种类型的集成。完成这项工作所需的具体电话还不存在，但正在 https://github.com/facebook/flow/issues/248 进行讨论。
*   将 Flow (OCaml)编译成 JS。使用像 https://github.com/ocsigen/js_of_ocaml 这样的工具，这在理论上是可能的，并且会打开许多其他的集成机会，但是看起来像是 T2 的大量工作。如果可能的话，这也意味着我们可以直接使用 Flow 的类型系统模型，而不是在 JavaScript 中重新实现我们自己的较弱的形式。

# 相关项目

*   【https://github.com/codemix/babel-plugin-typecheck 
*   [https://github.com/gcanti/babel-plugin-tcomb](https://github.com/gcanti/babel-plugin-tcomb)

# 有意思？

您能想到运行时类型自省的其他用例吗？或者更简单的实现方式？

GitHub 上提供了概念验证，但尚未分发给 NPM:

[](https://github.com/JoeStanton/babel-transform-flow-introspection) [## 乔斯坦顿/巴别塔-转换-流动-内省

### babel-transform-flow-introspection-Rough PoC 用于向运行时代码公开流类型注释

github.com](https://github.com/JoeStanton/babel-transform-flow-introspection) 

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！