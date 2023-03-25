# 遵守法律！

> 原文：<https://medium.com/hackernoon/obey-the-type-laws-ffb2fa4e5fe2>

我们现在应该对[函子](https://mmhaskell.com/blog/2017/1/30/the-easiest-haskell-idiom)、[应用函子](https://mmhaskell.com/blog/2017/2/6/applicatives-one-step-further)和[单子](https://mmhaskell.com/blog/2017/2/13/finally-understanding-monads-part-1)有了相当好的理解。如果你需要复习，一定要看看这些文章！现在我们理解了这些概念，所以是时候学习它们周围的**定律**了。

记住 Haskell 用一个类型类来表示每一个数学类。每个类型类都有一个或两个主要功能。所以，只要我们实现了那些函数和类型检查，我们就有了一个新的函子/应用/单子，对吗？

不完全是。是的，你的程序将被编译，你将能够使用这些实例。但这并不意味着你的实例遵循数学构造。如果他们不这样做，你的实例就不会满足其他程序员的期望。每个类型类都有自己的“法则”。例如，让我们回想一下我们在函子文章中创建的`GovDirectory`类型。假设我们创建了一个不同的仿函数实例:

```
data GovDirectory a = GovDirectory {
  mayor :: a,
  interimMayor :: Maybe a,
  cabinet :: Map String a,
  councilMembers :: [a]
}instance Functor GovDirectory where
  fmap f oldDirectory = GovDirectory {
    mayor = f (mayor oldDirectory),
    interimMayor = Nothing,
    cabinet = f <$> cabinet oldDirectory,
    councilMembers = f <$> councilMembers oldDirectory
  }
```

正如我们将看到的，这将违反函子定律之一。在这种情况下，它不是真正的函子。它的行为会让任何试图使用它的程序员感到困惑。我们应该注意确保我们的实例有意义。一旦您对这些类型类有了感觉，您将创建的实例就有可能遵循这些规律。所以，如果其中一些令人困惑，不要担心。这篇文章将非常数学化，我们不会在概念上纠缠太多。不用背熟这些规律也能理解和使用这些类**。所以事不宜迟，让我们深入法律！**

# 函子定律

有两个函子定律。首先是一个**同一律**。对于每个类型类，我们会看到这种思想的一些变化。记住`fmap`如何在我们的容器上“映射”一个函数。如果我们将 identity 函数映射到一个容器，结果应该是相同的容器对象:

```
fmap id = id
```

换句话说，我们的函子不应该应用任何额外的变化或副作用。它应该只应用函数。第二个定律是一个**合成定律**。它声明我们的仿函数实现不应该破坏函数的组成:

```
fmap (g . f) = fmap g . fmap f-- For reference, remember the type of the composition operator:
(.) :: (b -> c) -> (a -> b) -> (a -> c)
```

一方面，我们组合了两个函数，并将结果函数映射到我们的容器上。另一方面，我们映射第一个函数，得到结果，然后映射第二个函数。函子合成法则规定这些结果应该是相同的。这听起来很复杂。但是你不需要太担心。如果你违反了 Haskell 中的合成法则，你也很可能违反了同一性法则。

这是函子仅有的两条定律！让我们继续讨论应用函子。

# 适用法律

应用函子稍微复杂一点。他们有**四种不同的法律**。不过，第一个很简单。这是另一个简单的恒等式。上面写着:

```
pure id <*> v = v
```

在左侧，我们包装了标识函数。然后我们把它涂在我们的容器上。适用的同一律规定这应该导致一个相同的对象。很简单。

第二个定律是**同态**定律。假设我们在`pure`中包装了一个函数和一个对象。然后，我们可以对包装的对象应用包装的函数。当然，我们也可以在普通对象上应用普通函数，然后用 pure 包装它。同态定律表明这些结果应该是相同的。

```
pure f <*> pure x = pure (f x)
```

我们应该在这里看到一个独特的模式。几乎所有这些法律的首要主题是我们的类型类是容器。类型类函数不应该有任何副作用。他们应该做的就是促进数据的**包装、解包和转换**。

第三个定律是交换定律。有点复杂，不要太多心。它指出我们包装东西的顺序并不重要。一方面，我们将任何应用程序应用到一个纯包装的对象上。另一方面，首先我们包装一个函数**，将对象**作为参数。然后我们将它应用于第一个应用程序。这些应该是一样的。

```
u <*> pure y = pure ($ y) <*> u
```

最终的适用法则模仿第二函子法则。这是一个合成定律。它指出函数组合适用于函子内的应用程序:

```
pure (.) <*> u <*> v <*> w = u <*> (v <*> w)
```

这里法律的数量可能有点多。记住，你做的例子可能会遵循法律！让我们继续最后一个例子:单子。

# 单子定律

单子有三个定律。前两个是简单的**恒等法则**，就像我们其他的职业一样:

```
return a >>= f = f
m >>= return = m
```

这是左右身份。它们有效地声明了`return`函数唯一被允许做的**事情是包装对象(听起来熟悉吗？).它**不能以任何方式操纵数据**。我们的主要收获是，以下代码示例是等效的:**

```
func1 :: IO String
func1 = do
  str <- getLine
  return strfunc2 :: IO String
func2 = getLine
```

第三定律更有趣一点。它告诉我们单子中的结合律成立:

```
(m >>= f) >>= g = m >>= (\x -> f x >>= g)
```

但是我们看到第三个定律和其他合成定律有相似的结构。在第一种情况下，我们分两步应用两个函数。在第二种情况下，我们首先组合函数，然后应用结果。这些应该是一样的。

综上所述，所有定律都有两个主要观点。首先，**身份**应该保留在包装函数上，像`pure`和`return`。第二，**功能组合**应该贯穿我们的结构。

# 检查法律

正如我之前所说的，你想出的大多数实例会自然地遵循这些规则。随着你对不同类型的类有了更多的经验，这将会更加真实。然而，确保万无一失也是值得的。Haskell 有一个很好的工具来验证你的实例是否通过了某个法律。

这个工具是快速检查。它可以采用任何特定的规则，根据该规则生成**许多不同的测试用例**，并验证该规则是否成立。在这一节中，我们将对我们的`GovDirectory`仿函数实例进行一些测试。我们将看到`QuickCheck`如何证明它最初的失败，以及最终的成功。首先，我们需要在我们的类型上实现`Arbitrary`类型类。只要内部类型也是`Arbitrary`，比如内置的 string 类型，我们就可以这么做。然后我们将使用存在于我们内部类型上的所有其他`Arbitrary`实例:

```
instance Arbitrary a => Arbitrary (GovDirectory a) where
  arbitrary = do
    m <- arbitrary
    im <- arbitrary
    cab <- arbitrary
    cm <- arbitrary
    return $ GovDirectory
      { mayor = m
      , interimMayor = im
      , cabinet = cab
      , councilMembers = cm }
```

一旦你完成了这些，你就可以为一个特定的规则编写一个测试用例。在这种情况下，我们检查函子的单位函数:

```
main :: IO ()
main = quickCheck govDirectoryFunctorCheckgovDirectoryFunctorCheck :: GovDirectory String -> Bool
govDirectoryFunctorCheck gd = fmap id gd == gd
```

现在让我们在上面使用的错误实例上测试一下。我们可以看到一个特定的测试**将会失败**:

```
*** Failed! Falsifiable (after 2 tests):
GovDirectory {mayor = "", interimMayor = Just "\156", cabinet = fromList [("","")], councilMembers = []}
```

它为我们指定了一个测试失败的任意实例。现在假设我们纠正了这个实例:

```
interimMayor = f <$> (interimMayor oldDirectory),
```

我们会看到测试通过的！

```
+++ OK, passed 100 tests.
```

# 摘要

我们已经讨论了三个主要的类型类:函子、应用函子和单子。他们都有他们应该遵循的特定法律。其他使用你的代码的程序员会希望你做的任何实例都遵循这些法则。一旦你熟悉了这些类型，你就有可能创建遵循这些规律的实例。但是如果您不确定，您可以使用快速检查实用程序来验证它们。

我们的单子系列到此结束！现在，您应该拥有了在实践中开始使用它们所需的所有工具。请记住，它们是一个很难的概念，您可能需要复习几次。但最终，你会明白的！

如果你现在想开始使用 Haskell，一定要看看我们免费的[入门清单](https://www.mmhaskell.com/checklist)！它将通过帮助您完成下载过程和使用 Stack 制作您的第一个项目来帮助您开始 Haskell 体验！

如果你准备迎接更大的挑战，你应该得到我们的[递归练习册](https://www.mmhaskell.com/workbook)。也是免费的！它有几章关于递归和高阶函数的内容。它还有 10 道练习题供你试用！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！