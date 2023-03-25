# 何时代码复制是可接受的

> 原文：<https://medium.com/hackernoon/when-code-duplication-is-acceptable-51ce33ecd0f5>

## 当代码复制使测试的意图更加清晰时，它是可以接受的

![](img/c90585527dbaddfa1995877d8ad7b202.png)

A picture from [The Matrix](https://en.wikipedia.org/wiki/The_Matrix) movie showing several clones of [Agent Smith](https://en.wikipedia.org/wiki/Agent_Smith) wearing sunglasses in the rain. They all seem to be looking at the screen with an angry face.

出于本文的目的，我们将生产代码称为“[](https://hackernoon.com/tagged/production-code)****”**包含项目逻辑并在生产中运行的系统部分。我们称之为" [**测试代码**](https://hackernoon.com/tagged/test-code) **"** 项目的一部分，它包含了验证应用程序(产品代码)是否按预期工作的测试。**

**测试只是一个假的客户端，它使用被测试的 API，就像它在系统内部(对于单元测试)或系统外部(对于集成测试)被使用一样。不同之处在于，它包含了验证被测试内容正确性的断言。测试也被称为“第一客户”,因为它是您正在编写的代码的第一个消费者。**

**它所使用的 API 可以是一个函数、一个对象或整个服务。最重要的方面是，它应该尽可能简单，在没有太多复杂性或逻辑的情况下使用它正在测试的功能，只是一组(最好)满足[Arrange-Act-Assert](http://defragdev.com/blog/?p=783)(AAA)模型的**声明表达式**。**

> **一个测试不应该包含太多的复杂性或逻辑性**

**当生产代码包含一系列重复多次的步骤或需要在多个地方使用一个值时，可以认为是[不良代码气味](/@fagnerbrack/code-smell-92ebb99a62d0)。这是因为当我们为一个没有 100%覆盖率的系统在几个地方更改相同的生产代码时，无论是使用[“复制/粘贴”](https://medium.freecodecamp.com/the-benefits-of-typing-instead-of-copying-54ed734ad849)“搜索和替换”还是手动输入，错过更改系统重要部分的可能性都很高。**

**然而，对于一个声明性的测试代码，当它执行一系列步骤或者需要一个值在多个测试中使用时，那么在一些情况下，如果代码是重复的，也不是那么糟糕。测试应该表达出他们应该做什么的明确意图。复制仍然是一个问题，当然，我们应该尽量在任何情况下都不要复制代码。然而，它可能没有生产代码中的重复那么糟糕。**

> **测试代码中的重复可能没有生产代码中的重复糟糕**

**让我们想象一个假设的场景，其中一个测试覆盖了系统更广泛的功能，并且它的一部分(一个单元)在产品代码中被重用。那个被重用的小单元被一个不同用例的测试所覆盖。如果该单元中的一个 bug 导致了一次测试中断，那么就没有必要在其他使用它的地方的产品代码中再次覆盖它。进行一次单独的测试也将修复系统中没有被明确覆盖的部分。**

**在测试代码中，如果我们错误地修改了一些东西，我们没有任何东西可以突出问题。在这种情况下，应用 [DRY](https://en.wikipedia.org/wiki/Don't_repeat_yourself) 会使代码变得比必要的更复杂，因为它混淆了本可以更加清晰的信息。**

> **我们不能测试测试好吗？**

**如果我们要抽象出一些有助于运行测试的东西，我们应该像对待产品代码一样覆盖它。然而，在测试代码中创建太多的抽象和助手是一种[坏代码味道](/@fagnerbrack/code-smell-92ebb99a62d0)的迹象。测试应该是简单的，创建许多抽象表明设计中的潜在问题。**

**下面的例子展示了几个不重复值和断言步骤的测试。相反，它们将值存储在几个常量和函数中，以便重复使用，并且只有一个地方可以更改:**

**[https://jsfiddle.net/fagnerbrack/9w0wttdf/](https://jsfiddle.net/fagnerbrack/9w0wttdf/)**

**在另一个例子中，我们删除了常量和额外的函数。通过这样做，我们还减少了代码量。有人可能会说代码现在更清晰了，因为所有的东西都包含在每个测试中，而不是分散在整个文件中，每个测试都必须访问更高的范围:**

**[https://jsfiddle.net/fagnerbrack/sdx35wgu/](https://jsfiddle.net/fagnerbrack/sdx35wgu/)**

> **对于一个声明性的并且覆盖了系统大部分的测试代码来说，重复是可以接受的，只要他们清楚地表达了测试的意图**

**一些操作可以在测试环境中重复。毕竟，我们需要为每一个测试执行“行为”。但是，有些东西我们可以抽象，比如“排列”和“断言”。然而，如果我们看到抽象某些东西的需要，那么我们应该只抽象测试关注点，而不是测试代码。**

**例如，如果我们正在使用面向对象程序设计，并为`cart`对象的许多不同状态测试方法`cart.addProduct(fakeProduct)`，那么为每个检查不同行为的测试重复`cart.addProduct`代码是完全合理的。一个测试检查**添加**后是否可以从购物车中检索到有效产品，另一个测试检查**当我们试图将无效产品添加到购物车时，是否没有添加**。把“行为”部分(`cart.addProduct`)抽象成`addFakeProductTo(cartInstance)`这样的东西，意义不大。在这种情况下，重复是可以的，尽管一般来说应该避免重复。**

**[https://gist.github.com/FagnerMartinsBrack/76d496d2e71e66dd458a0d9ac4a94c46](https://gist.github.com/FagnerMartinsBrack/76d496d2e71e66dd458a0d9ac4a94c46)**

**但是，如果我们想要为不止一个测试准备一个相似的产品状态，那么它更有可能从抽象中受益。在本例中，我们并不关心假布料的尺寸或颜色，它应该是裤子或衬衫:**

**[https://gist.github.com/FagnerMartinsBrack/4faa92f4e5d01bac7229fb4e61a71637](https://gist.github.com/FagnerMartinsBrack/4faa92f4e5d01bac7229fb4e61a71637)**

> **当在测试代码中创建抽象时，我们应该只抽象测试问题，而不是测试代码。**

**测试是一小段代码，应该是声明性的，没有太多的复杂性或逻辑性。有时，可能有必要在“排列”或“断言”部分创建一些小的抽象。在测试环境中，复制没有生产代码中的复制糟糕。**

**原则只是原则，它们不是硬性规定，应该在有意义的时候使用。在这种情况下，在某些情况下可能需要复制。到处敷干不会总有好处。**

**尽量不要重复自己。然而，要明白有些情况下你可能需要。**

**感谢阅读。如果您有一些反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。**