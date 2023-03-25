# 带管道的智能数据

> 原文：<https://medium.com/hackernoon/smart-data-with-conduits-b61fad3f4bad>

如果你现在是一名程序员，有一个现实你最好习惯。人们希望你知道如何处理大数据。这种数据需要一段时间来处理。如果你想同时把所有的东西都放进内存，它会让你的程序崩溃。但是您也希望避免对数据库发出单独的 SQL 请求来访问每一行。那会非常慢。那么如何解决这个问题呢？更具体地说，在 Haskell 中如何解决这个问题？

这正是`Data.Conduit`库存在的问题类型。本文将通过一个简单的例子。我们将使用一个管道“source”来传输一些整数，并将它们加在一起。幸运的是，我们一次只需要看到来自源的一个数字，所以我们不需要把大量的数据带到内存中。让我们先来看看我们是如何创建这个源的。

# 获取我们的数据

假设我们只是试图找出从 1 到 1000 万的数字的总和。最简单的方法是让我们的数字“来源”是一个原始列表，然后我们取这个列表的总和:

```
myIntegerSourceBad :: [Integer]
myIntegerSourceBad = [1..10000000]sumFromSource :: [Integer] -> Integer
sumFromSource lst = sum lst
```

这种方法需要同时将整个列表保存在内存中。想象一下，如果我们在查询一个拥有数千万条目的数据库。这可能会有问题。我们解决这个问题的第一步是使用`Data.Conduit`从这个列表中创建一个整数的“源”。我们可以用`sourceList`函数来做这件事。注意`Identity`是简单的恒等单子。也就是实际上不做任何事情的单子基单子:

```
import Control.Monad.Identity (Identity)
import Data.Conduit (Source)
import Data.Conduit.List (sourceList)myIntegerSource :: Source Identity Integer
myIntegerSource = sourceList [1..10000000]
```

所以现在不是返回一个`Int`值的集合列表，我们实际上是得到一个我们称之为`Source`的值流。让我们来描述一些导管类型的含义，以便更好地了解这里发生了什么。

# 导管类型

导管类型都建立在`ConduitM`之上，这是导管的基本单子。这种类型有四个不同的参数。我们将把它的定义写成这样:

```
type ConduitM i o m r
```

您应该将每个管道视为一个数据处理漏斗。`i`类型是您可以带入漏斗的输入。`o`型代表输出。这些是你可以从漏斗中推出的价值。`m`类型是底层单子。我们将看到带有`Identity`单子和`IO`单子的例子。

我们可以把最后一个`r`类型看作是“结果”。但是和输出不一样。这是函数本身在完成后返回的值。稍后我们写 sink 的时候会看到一个返回类型。但除此之外，我们通常只使用`()`。因此，我们可以使用`Conduit`类型同义词重写一些类型。这个同义词有助于忽略返回类型。但是它也很烦人，因为它颠倒了`m`和`o`类型的位置。不要让这个绊倒你。

```
type Conduit i m o = ConduitM i o m ()
```

现在我们可以定义我们的源和接收器类型。A `Source`是一个没有输入(因此`i`是单元类型)并产生一些输出的管道。相反，`Sink`是一个接受一些输入但不产生输出的管道(因此是`Void`类型):

```
type Source m o = Conduit () m o
type Sink i m r = ConduitM i Void m r
```

所以在上面的例子中，我们的函数是一个“源”。它创建一系列整数值，而不接受任何输入。我们将了解如何匹配具有不同匹配类型的不同导管。

# 原始函数

管道库中有三个基本函数:产出、等待和剩余。收益是我们如何将一个值传递到下游的另一个管道。换句话说，它是一种“生产”功能，或者说是价值的来源。我们只能产生与我们的管道函数的输出类型相匹配的类型。

那么我们如何从上游获得价值呢？答案是`await`函数。这就像一个接收器，允许一个函数在等待接收来自上游的值时保持惰性。自然，这个值必须是管道输入的类型。现在，`await`的实际结果类型是一个`Maybe`值。我们可能会收到一个值`Nothing`。这表明我们的上游来源已经终止，我们不会再收到任何值。这通常是我们让函数返回的情况。

最后一个功能是`leftover`功能。这允许您从上游获取一个值，并将其放回上游。这样，我们可以从不同的接收器或者该接收器的不同迭代中再次使用该值。文档补充的一个警告是，您不应该对您自己创建的值使用`leftover`。您应该只使用从上游获得的值。

# 书写我们的管道

所以上面我们已经为我们的玩具程序写了一个源管道。我们返回一个整数列表，这样我们就可以把它们一个接一个地输入到我们的程序中。现在，我们将编写一个接收这些值并将它们相加的接收器。这将采用带有累加器参数的递归函数的形式。接收器经常递归地接收它们的下一个输入。

所以我们将从等待来自上游管道的一些值开始。

```
myIntegerSink :: Integer -> Sink Integer Identity Integer
myIntegerSink accum = do
  maybeFirstVal <- await
  ...
```

如果这个值是`Nothing`，我们就知道不会有更多的值出现。然后，我们可以返回我们所拥有的任何累计值。

```
myIntegerSink :: Integer -> Sink Integer Identity Integer
myIntegerSink accum = do
  maybeFirstVal <- await
  case maybeFirstVal of
    Nothing -> return accum
    ...
```

如果该值包含另一个`Int`，我们将首先通过将它们相加来计算新的总和。然后，我们将使用新的累加器参数对 sink 函数进行递归调用:

```
myIntegerSink :: Integer -> Sink Integer Identity Integer
myIntegerSink accum = do
  maybeFirstVal <- await
  case maybeFirstVal of
    Nothing -> return accum
    Just val -> do
      let newSum = val + accum
      myIntegerSink newSum
```

真的就这样了！我们的例子很简单，所以代码也很简单。

# 结合我们的管道

现在我们要结合我们的管道。我们用“fuse”操作符来做这件事，写成`=$=`。Haskell 库因为拥有奇怪的操作符而臭名昭著。这个库不一定是个例外。但是把导管想象成一个隧道，这个操作符看起来像是连接隧道的两个部分。

使用该操作符，第一个管道的输出类型需要与第二个管道的输入类型相匹配。就我们如何设置管道而言，情况就是这样。因此，现在为了润色，我们将`runConduitPure`与我们的组合导管一起使用:

```
fullConduit :: Integer
fullConduit = runConduitPure $ 
  myIntegerSource =$= myIntegerSink 0
```

使用 fuse 添加更多导管通常非常容易。例如，假设我们想让偶数加倍计数。我们可以在我们的源或汇中实现这一点，但我们也可以添加另一个管道。它将把一个`Int`作为输入，并产生一个`Int`作为输出。它需要输入一个整数，检查它的值，如果是偶数，就加倍它的值。然后我们将产生结果值。同样，如果我们没有看到管道的末端，我们需要递归地跳回到管道中。

```
myDoublingConduit :: Conduit Integer Identity Integer
myDoublingConduit = do
  maybeVal <- await
  case maybeVal of
    Nothing -> return () 
    Just val -> do
      let newVal = if val `mod` 2 == 0
            then val * 2
            else val
      yield newVal
      myDoublingConduit
```

然后我们可以用保险丝操作器将这个导管插在其他导管之间！

```
fullConduit :: Integer
fullConduit = runConduitPure $ 
  myIntegerSource =$= myDoublingConduit =$= myIntegerSink 0
```

# 矢量化

也有一些时候，您会想要批量处理某些事务。一个很好的例子就是当你想把所有结果从你的接收器插入到一个数据库中。您不希望一直发送单独的插入查询。相反，您可以等待并将一组输入组合在一起，然后发送批量插入。

对于我们的玩具示例，我们将把我们的 int 以 100000 为一组收集起来，这样我们就可以随时更新日志进度。这将需要改变我们的管道来生活在木卫一上。但是一旦我们做了这个改变，我们可以像这样使用`conduitVector`函数:

```
fullConduitIO :: IO Integer
fullConduitIO = runConduit $ 
  myIntegerSourceIO =$= conduitVector 100000 =$= myIntegerVectorSink 0myIntegerSourceIO :: Source IO Integer
myIntegerSourceIO = sourceList [1..100000000]myIntegerVectorSink :: Integer -> Sink (Vector Integer) IO Integer
myIntegerVectorSink accum = do
  maybeFirstVal <- await
  case maybeFirstVal of
    Nothing -> return accum
    Just vals -> do
      let newSum = (Vec.sum vals) + accum
      lift $ print newSum
      myIntegerVectorSink newSum
```

通过对管道进行矢量化，我们需要更改 sink，以便它将`Vector Int`而不是`Int`作为其输入类型。然后我们从`await`得到一个向量，所以我们也必须对这些值求和。

# 摘要

数据。Conduit library 允许您以可持续的方式处理大量数据。您可以使用它从数据库或其他来源传输数据。这比一次将大量信息放入内存更有效。它还允许你通过被称为管道的“隧道”传递信息。你可以让它们执行许多复杂的操作。您主要由`yield`、`await`和`leftover`功能组成管道功能。使用“融合”操作符`=$=`将导管合并成一个更大的导管。您也可以使用`conduitVector`功能来批处理某些操作符。

这是一个更高级的话题。如果您以前从未编写过 Haskell，那么它并不像本文描述的那样可怕！查看我们的[入门清单](https://www.mmhaskell.com/checklist)，迈出 Haskell 之旅的第一步！

如果你以前学过一点 Haskell，但是需要更多的基础练习，你应该下载我们的[递归工作簿](https://www.mmhaskell.com/workbook)。它有一些关于递归的很棒的材料以及 10 个练习题！

下周我们将继续一些更高级的话题。我们将研究`Data.Aeson`库，以及它如何允许我们将 Haskell 对象序列化为 JSON 格式！所以请继续关注周一早上的哈斯克尔！

# 附录

我前面提到过，操作符可能是 Haskell 中的一个主要痛点。不幸的是，文档也是如此。在为管道概念寻找合适的文档时，尤其如此。因此，作为参考，这里是我使用的所有导入:

```
import Conduit (conduitVector, lift)
import Control.Monad.Identity (Identity)
import Data.Conduit (Source, Sink, await, runConduitPure, (=$=), Conduit, yield, runConduit)
import Data.Conduit.List (sourceList)
import Data.Vector hiding (sum)
import qualified Data.Vector as Vec
```

注意`Data.Conduit`和`Data.Conduit.List`来自 hackage 上的`conduit`库。但是，`Conduit`模块其实来自`conduit-combinators`。这很有欺骗性。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！