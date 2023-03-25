# 测试您的 Haskell

> 原文：<https://medium.com/hackernoon/putting-your-haskell-to-the-test-193cc82b1823>

您在生产代码中遇到过多少次回归错误？对于软件工程师来说，这可能是最令人沮丧的经历之一。您发布了代码，并确信它可以工作。现在发现它打破了别的东西。避免这些错误的最好方法是让测试检查您的代码是否存在这些情况。那么 Haskell 中的测试是如何工作的呢？

在 Haskell 中，我们有一个口头禅，如果你的代码编译了，它应该工作。这在 Haskell 中可能比在其他语言中更真实。但这仍然是一个不太符合要求的半开玩笑的评论。在 Haskell 中，通常有不同的方法来实现相同的目标。但是我们应该努力让编译器更有可能捕捉到我们的错误。例如，我们可以使用 newtypes 作为类型同义词的替代来限制错误。

但是，在某一点上，如果你想对你的代码有信心，你必须开始写测试。幸运的是，作为一门纯函数式语言，Haskell 在测试方面有一些优势。在某些方面，它比面向对象的语言更容易测试，也更自然。它的功能特性让我们对测试 Haskell 更有信心。让我们来看看为什么。

# 功能测试优势

当我们测试特定的功能时，测试效果最好。我们传递输入，我们得到输出，我们期望输出符合我们的期望。在 Haskell 中，这是一种非常合适的方法。功能是一等公民。我们的程序很大程度上是由函数的组合定义的。因此，我们的代码默认被分解成可测试的单元。

相比之下，面向对象的语言，如 Java。我们可以很容易地测试一个类的静态方法。这些通常与纯函数没有太大区别。但是现在考虑在对象上调用方法，尤其是 void 方法。因为该方法没有返回值，所以它的效果都是内部的。通常，我们无法检查内部效果，因为这些字段可能是私有的。

我们可能还想尝试检查某些边缘情况。但是这可能涉及到构造任意状态的对象。同样，我们会在私有领域遇到困难。

在 Haskell 中，我们所有的函数都有返回值，而不是依赖于效果。这使得我们很容易检查他们的真实结果。**纯函数**也给了我们另一个巨大的胜利。我们的函数一般没有**没有副作用**并且不依赖于全局状态。因此，我们不必担心许多可能影响我们系统的病理情况。

# 测试驱动开发

既然我们知道了为什么我们对我们的测试有点信心，让我们探索一下编写测试的过程。第一步是为特定模块定义公共 API。为此，我们定义了一个将要公开的特定函数，以及它将作为输入输出的类型。然后我们可以像这篇关于[编译驱动学习](https://www.mmhaskell.com/blog/2017/4/3/compile-driven-learning)的文章中建议的那样，把它作为未定义的东西剔除掉。这使得调用它的代码仍然可以编译。

现在，对于大多数开发人员来说，最大的诱惑就是直接开始编写函数。毕竟这是一个新功能，你应该对此感到兴奋！

但是从长远来看，如果你首先花时间定义你的测试用例，你会更好。您应该首先为您的函数定义特定的输入集。然后，您应该将它们与这些参数的预期输出相匹配。我们将在下一节讨论这方面的细节。然后，您将在测试套件中编写您的测试，并且您应该能够编译和运行测试。因为你的函数还没有定义，所以它们都会失败。但是现在你可以**逐步实现这个功能**。

您的下一个目标是让函数运行完成。每当你发现一个你不确定如何填充的值时，试着想出一个基础值。一旦运行完成，测试将告诉您不正确的值，而不是错误。然后你就可以逐渐把越来越多的事情做对了。也许你的一些测试会得到验证，但是你忽略了一个特殊的情况。测试会让你知道的。

# 胡尼特

测试代码的一种方法是使用`QuickCheck`。这种方法更关注抽象的属性，而不是具体的例子。在这篇关于[单子定律](https://mmhaskell.com/blog/2017/3/13/obey-the-type-laws)的文章中，我们用这个库来看几个例子。在本文中，我们将结合使用`HUnit`库和`Tasty`测试框架来测试一些代码。

假设一开始，我们正在编写一个接受三个输入的函数。它应该将前两个相乘，然后减去第三个。我们将从定义它开始:

```
simpleMathFunction :: Int -> Int -> Int -> Int
simpleMathFunction a b c = undefined
```

我们将取出输入和输出的组合，并把它们做成这样的测试用例:

```
simpleMathTests :: TestTree
simpleMathTests = testGroup "Simple Math Tests"
  [ testCase "Small Numbers" .
      simpleMathFunction 3 4 5 @?= 7
  , testCase "Bigger Numbers" .
      simpleMathFunction 22 12 64 @?= 20
  ]
```

我们从定义一组具有更广泛描述的测试开始。然后我们制作单独的测试用例，每个用例都有自己的名字。然后在每一个例子中，我们使用`@?=`操作符来检查实际值是否等于期望值。确保你的订单是正确的，把实际价值放在第一位。否则你会看到混乱的输出。然后我们可以在测试套件中运行它，我们将获得以下信息:

```
Simple Math Tests
  Small Numbers:  FAIL
    Exception: Prelude.undefined
  Bigger Numbers: FAIL
    Exception: Prelude.undefined
```

正如所料，我们的测试用例失败了，所以我们知道如何改进我们的代码。所以让我们实现这个函数:

```
simpleMathFunction :: Int -> Int -> Int -> Int
simpleMathFunction a b c = a * b - c
```

现在一切都成功了！

```
Simple Math Tests
  Small Numbers:  OK
  Bigger Numbers: OKAll 2 tests passed (0.00s)
```

# 行为驱动发展

当你从事更大的项目时，你会发现你不仅仅是与团队中的其他工程师互动。像项目经理和 QA 测试人员这样的技术涉众往往较少。这些人对代码的内部工作不太感兴趣，但仍然关心代码的更广泛的行为。在这些情况下，您可能想要采用“行为驱动开发”这就像测试驱动开发，但是有不同的味道。在这个框架中，您通过一组行为来描述您的代码及其预期效果。理想情况下，这些足够抽象，以至于不太懂技术的人也能理解。

作为工程师，您希望能够将这些行为转化为代码。幸运的是，Haskell 是一种极具表现力的语言。你可以经常以这样一种方式定义你的函数，使得它们读起来几乎像英语。

# Hspec

在 Haskell 中，您可以使用`Hspec`库实现行为驱动开发。有了这个库，你可以用一种特别有表现力的方式来描述你的函数。你所有的测试规范将属于一个`Spec`单子。

在这个 monad 中，您可以使用可组合的函数来描述测试用例。你通常会用“描述”功能来开始一个测试用例的描述。这需要一个描述测试用例总体概况的字符串。

```
simpleMathSpec :: Spec
simpleMathSpec = describe "Tests of our simple math function" $ do
  ...
```

然后，您可以通过为每个单独的案例添加不同的“上下文”来修改它。上下文函数也接受一个字符串。但是`context`的习惯用法是，你的字符串要以“when”或者“with”这样的词开头。

```
simpleMathSpec :: Spec
simpleMathSpec = describe "Tests of our simple math function" $ do
  context "when the numbers are small" $
    ...
  context "when the numbers are big" $
    ...
```

现在你将描述每一个实际的测试用例。你将使用函数“它”，然后进行比较。`Hspec`框架中的组合子是带有描述性名称的函数，如`shouldBe`。所以你的案例会从一个句子般的描述和案例的背景开始。案件结束“应该有一定结果”:`x`“应该”`y`。实际情况是这样的:

```
main :: IO ()
main = hspec simpleMathSpec simpleMathSpec :: Spec
simpleMathSpec = describe "Tests of our simple math function" $ do
  context "when the numbers are small" $
    it "Should match the our expected value" $
      simpleMathFunction 3 4 5 `shouldBe` 7
  context "when the numbers are big" $
    it "Should match the our expected value" $
      simpleMathFunction 22 12 64 `shouldBe` 200
```

也可以完全省略上下文:

```
simpleMathSpec :: Spec
simpleMathSpec = describe "Tests of our simple math function" $ do
  it "Should match the our expected value" $
    simpleMathFunction 3 4 5 `shouldBe` 7
  it "Should match the our expected value" $
    simpleMathFunction 22 12 64 `shouldBe` 200
```

最后，您将得到带有不同测试用例描述的整洁的格式化输出。通过编写表达性的函数名并添加您自己的组合子，您可以使您的测试代码更加自文档化。

```
Tests of our simple math function
  when the numbers are small
    Should match the our expected value
  when the numbers are big
    Should match the our expected valueFinished in 0.0002 seconds
2 examples, 0 failures
```

# 结论

这就结束了我们对 Haskell 测试的概述。我们简要描述了测试驱动开发的一般实践。我们看到了为什么它在像 Haskell 这样的函数式类型语言中更加强大。我们讨论了一些基本的测试机制，你可以在 HUnit 库中找到。然后我们描述了“行为驱动开发”的过程，以及它与普通的 TDD 有什么不同。最后，我们展示了 Haskell 中的`HSpec`库是如何将 BDD 带入生活的。

如果你想看到 TDD 的运行，并在此过程中了解一个很酷的函数范式，你应该看看我们的[递归工作簿](https://www.mmhaskell.com/workbook)。它有 10 个带测试的练习题，所以你可以逐步改进你的代码，最终看到测试通过！

如果你以前从未用 Haskell 编程，并且想看看这种流行是怎么回事，你应该下载我们的[入门清单](https://www.mmhaskell.com/checklist)。它将带您了解下载和安装 Haskell 的一些基础知识，并向您展示其他一些工具来帮助您！

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！