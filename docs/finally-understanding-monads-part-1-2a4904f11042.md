# (最后)理解单子(第 1 部分)

> 原文：<https://medium.com/hackernoon/finally-understanding-monads-part-1-2a4904f11042>

这篇文章最初发表在 2017 年 2 月 13 日的[周一早间哈斯克尔博客](https://mmhaskell.com/blog/2017/2/13/finally-understanding-monads-part-1)上。查看博客获取更多 Haskell 内容！

我们现在应该对[函子](https://mmhaskell.com/blog/2017/1/30/the-easiest-haskell-idiom)和[适用函子](https://mmhaskell.com/blog/2017/2/6/applicatives-one-step-further)有了一个不错的理解(如果你没有，请查看链接！).现在是时候迈出下一步了。我们将处理可怕的单子概念。网上有几十个 monad 教程和描述。这是有道理的。单子对于用 Haskell 编写任何有意义的程序都是至关重要的。它们不是函数式编程中最难的概念，但是由于它们的重要性，它们是最大的障碍。在这一系列文章中，我们将尝试以小的、可管理的块来处理这个概念。

所以事不宜迟，下面是我的定义:单子用特定的**上下文**包装一个值或一个计算。单子必须定义一种在上下文中包装正常值的方式**，以及一种在上下文中组合计算**的方式**。**

这个定义相当宽泛。所以让我们从更实际的层面来理解这一点。

# 单子类型类

就像函子和应用函子一样，Haskell 用类型类表示单子。它有两个功能:

```
class Monad m where
  return :: a -> m a
  (>>=) :: m a -> a -> m b -> m b
```

这两个功能对应了上面的两个思路。`return`函数指定了如何在单子的上下文中**包装**值。`>>=`操作符，我们称之为“bind”函数，指定如何在上下文中**组合**两个操作。让我们通过探索几个具体的单子实例来进一步阐明这一点。

# 可能单子

正如`Maybe`是函子和应用函子，它也是单子。为了激励`Maybe`单子，让我们考虑这个代码。

```
maybeFunc1 :: String -> Maybe Int
maybeFunc1 “” = Nothing
maybeFunc1 str = Just $ length strmaybeFunc2 :: Int -> Maybe Float
maybeFunc2 i = if i `mod` 2 == 0
  then Nothing
  Else Just ((fromIntegral i) * 3.14159)maybeFunc3 :: Float -> Maybe [Int]
maybeFunc3 f = if f > 15.0
  then Nothing
  else $ Just [floor f, ceil f]runMaybeFuncs :: String -> Maybe [Int]
runMaybeFuncs input = case maybeFunc1 input of
  Nothing -> Nothing
  Just i -> case maybeFunc2 i of
    Nothing -> Nothing
    Just f -> maybeFunc3 f
```

我们可以看到，当我们继续对连续函数调用的结果进行模式匹配时，我们开始开发一个可怕的三角形模式。如果我们给它添加更多的功能，它会变得越来越糟糕。当我们将`Maybe`视为单子时，我们可以使代码更加清晰。让我们看看 Haskell 是如何作为单子实现`Maybe`的。

```
instance Monad Maybe where
  return = Just
  Nothing >>= _ = Nothing
  Just a >>= f = f a
```

Maybe monad 描述的上下文很简单。Maybe 中的计算可能会失败，也可能会成功，其值为。我们可以将任何值包装在这个上下文中，称之为“成功”。我们用`Just`构造函数来做这件事。我们用`Nothing`来代表失败。

我们通过检查第一次计算的结果来组合这种情况下的计算。如果它**成功**，我们取其值，并将其传递给**第二次计算**。如果它失败了，那么我们就没有值可以传递给下一步。所以**总计算失败**。让我们看看如何使用 bind 操作符来组合我们的操作:

```
runMaybeFuncs :: String -> Maybe [Int]
runMaybeFuncs input = maybeFunc1 input >>= maybeFunc2 >>= maybeFunc3
```

这个看起来干净多了！让我们来看看为什么这些类型行得通。`maybeFunc1 input`的结果简单来说就是`Maybe Int`。然后，bind 操作符允许我们获取这个`Maybe Int`值，并将其与`maybeFunc2`组合，后者的类型是`Int -> Maybe Float`。**绑定操作符将**这些解析为`Maybe Float`。然后我们通过 bind 操作符将它传递给`maybeFunc3`，得到我们的最终类型`Maybe [Int]`。

不过，您的函数不会总是组合得如此清晰。这就是`do`符号发挥作用的地方。我们可以将上述内容改写为:

```
runMaybeFuncs :: String -> Maybe [Int]
runMaybeFuncs input = do
  i <- maybeFunc1 input
  f <- maybeFunc2 f
  maybeFunc3 f
```

`<-`操作符是特殊的。它有效地**从单子展开**右边的值。这意味着值`i`具有类型`Int`，**，即使**的结果为`Maybe Int`。绑定操作在幕后进行，如果函数返回`Nothing`，那么整个`runMaybeFuncs`函数将返回`Nothing`。

乍一看，这比 bind 示例更复杂。然而，它给了我们更多的灵活性。考虑一下，在调用`maybeFunc2`之前，我们是否想在整数上加 2。在`do`符号中，这**很容易**处理，但是当简单地使用绑定时就更困难了:

```
runMaybeFuncs :: String -> Maybe [Int]
runMaybeFuncs input = do
  i <- maybeFunc1 input
  f <- maybeFunc2 (i + 2)
  maybeFunc3 f-- Not so nice
runMaybeFuncsBind :: String -> Maybe [Int]
runMaybeFuncsBind input = maybeFunc1 input
  >>= (\i -> maybeFunc2 (i + 2))
  >>= maybeFunc3
```

如果我们想在一个函数调用中使用**多个先前的结果**，收益会更加明显。使用绑定，我们将不得不在匿名函数中不断地积累参数。关于 do 符号的一个注意事项:我们从不使用`<-`来展开 do 块中的最后一个操作。我们对`maybeFunc3`的调用具有类型`Maybe [Int]`。这是我们的最终类型(不是`[Int]`)，所以我们不打开包装。

# 要么是单子

现在，让我们检查一下`Either`单子，它与`Maybe`单子非常相似。定义如下:

```
instance Monad (Either a) where
  return r = Right r
  (Left l) >>= _ = Left l
  (Right r) >>= f = f r
```

鉴于`Maybe`要么成功，要么失败，而`Either`单子**将信息**附加到失败。就像`Maybe`一样，它通过调用 successful 将值包装在其上下文中。一元行为还通过在第一次失败时短路来组合操作。让我们看看如何使用它来使上面的代码更加清晰。

```
maybeFunc1 :: String -> Either String Int
maybeFunc1 “” = Left “String cannot be empty!”
maybeFunc1 str = Right $ length strmaybeFunc2 :: Int -> Either String Float
maybeFunc2 i = if i `mod` 2 == 0
  then Left “Length cannot be even!”
  else Right ((fromIntegral i) * 3.14159)maybeFunc3 :: Float -> Either String [Int]
maybeFunc3 f = if f > 15.0
  then Left “Float is too large!”
  else $ Right [floor f, ceil f]runMaybeFuncs :: String -> Either String [Int]
runMaybeFuncs input = do
  i <- maybeFunc1 input
  f <- maybeFunc2 i
  maybeFunc3 f
```

以前，每次失败只是给了我们一个`Nothing`值:

```
>> runMaybeFuncs ""
Nothing
>> runMaybeFuncs "Hi"
Nothing
>> runMaybeFuncs "Hithere"
Nothing
>> runMaybeFuncs "Hit"
Just [9,10]
```

现在，当我们运行我们的代码时，我们可以查看产生的错误字符串，这将告诉我们**哪个函数实际上失败了**。

```
>> runMaybeFuncs ""
Left "String cannot be empty!"
>> runMaybeFuncs "Hi"
Left "Length cannot be even!"
>> runMaybeFuncs "Hithere"
Left "Float is too large!"
>> runMaybeFuncs "Hit"
Right [9,10]
```

注意，我们通过错误类型来参数化`Either`单子。如果我们有:

```
maybeFunc2 :: Either CustomError Float
…
```

这个函数现在在不同的单子上。将它与我们的其他功能结合起来就不那么简单了。如果你想知道我们会怎么做，看看 quora 上的这个[答案。](https://www.quora.com/What-are-some-practical-uses-of-bifunctors-in-Haskell/answer/James-Bowen-13)

# 木卫一单子

IO 单子可能是 Haskell 中最重要的单子**。这也是最难理解的单子之一。第一次学习单子时，讨论它的实际实现有点太复杂了。所以我们就通过例子来学习。**

IO monad 将计算包装在以下上下文中:“该计算可以从终端、文件系统、操作系统和/或网络读取信息或向其写入信息”。如果你想让**用户输入**，**打印**一条消息给用户，从**文件**中读取信息，或者进行**网络调用**，你需要在 IO Monad 中完成这些。这些都是“副作用”。我们不能从“纯”Haskell 代码中执行它们。

几乎所有计算机程序最重要的工作就是以某种方式与外界互动。因此，所有可执行 Haskell 代码的根都是一个名为`main`的函数，类型为`IO ()`。所以每个程序都从 IO 单子开始。从这里，您可以获得您需要的任何输入，用输入调用相对“纯”的代码，然后以某种方式输出结果。反之则不行。你不能从纯代码中调用 IO 代码，就像你可以从纯代码中调用`Maybe`函数一样。

让我们看一个简单的程序，展示一些基本的 IO 功能。我们将使用 do 符号来说明与我们讨论过的其他单子的相似性。为了清楚起见，我们列出了每个 IO 函数的类型。

```
main :: IO ()
main = do
  -- getLine :: IO String
  input <- getLIne
  let uppercased = map Data.Char.toUpper input
  -- print :: String -> IO ()
  print uppercased
```

所以我们再次看到程序的每一行都有类型`IO a`。(一个`let`语句**可以出现在任何一个单子**中)。正如我们可以在 maybe 示例中展开`i`以获得`Int`而不是`Maybe Int`，我们可以使用`<-`来**展开`getLine`的结果**作为`String`。然后我们可以使用字符串函数操作这个值，并将结果传递给`print`函数。

这是一个简单的 echo 程序。它从终端读取一行，然后以全部大写的形式打印出来。希望它能让您对 IO 的工作原理有一个基本的了解。我们将在接下来的两篇文章中讨论更多的细节。

# 摘要

单子将计算包装在一个**特定的上下文**中。它为上下文中的**包装值**和上下文中的**组合操作**定义了函数。**也许**是单子。我们通过说它的计算可以**成功或失败**来描述它的上下文。**任一**类似于 Maybe，除了它可以将错误信息添加到故障中。 **IO monad** 非常重要，它封装了读写终端、网络和文件系统的操作上下文。学习一元代码最简单的方法是使用 **do 符号**。在这个符号中，每一行都有一个右边的单子值。然后，您可以使用`<-`操作符打开左侧的值。

如果你想了解单子，一定要去博客[看看！本周我们有一篇关于](https://mmhaskell.com/)[读者和作者单子](https://mmhaskell.com/blog/2017/2/20/how-to-read-and-write-with-monads)的新文章，并展示了它们如何封装我们可能从 IO 单子获得的不同种类的副作用。

希望这篇文章已经让你开始(最终)理解单子。如果您还没有编写任何 Haskell 代码，并且想要开始测试您的单子知识，请务必查看我们免费的 Haskell 入门清单!

还没有为单子做好准备，但想尝试一些不同的 Haskell 技能？查看我们的[递归工作簿](https://www.mmhaskell.com/workbook)。它包括 2 章关于递归和高阶函数的内容，以及 10 个测试练习题。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！