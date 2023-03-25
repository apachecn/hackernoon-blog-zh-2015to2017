# 手榴弹！相关型神经网络

> 原文：<https://medium.com/hackernoon/grenade-dependently-typed-neural-networks-c139efdbaa43>

[在过去的](https://mmhaskell.com/blog/2017/9/11/deep-learning-and-deep-types-tensor-flow-and-dependent-types) [几周](https://www.mmhaskell.com/blog/2017/9/18/checking-its-all-in-place-placeholders-and-dependent-types)里，我们探讨了我在这个博客上提出的最复杂的话题之一。我们检查了使用张量流时可能发生的潜在运行时故障。这些问题包括尺寸不匹配和缺少占位符。在理想的情况下，我们应该在编译时捕捉这些问题。在目前阶段，Haskell 张量流库不支持这一点。但是我们演示了通过使用依赖类型来添加一个层来实现这一点是可能的。

现在，我仍然是依赖类型的新手，所以我提出的解决方案相当笨拙。本周我将展示一个来自另一个库的更好的例子。手榴弹库到处都使用依赖类型。它允许我们极其简洁地构建可验证有效的神经网络。所以让我们一起来看看这到底是怎么回事吧！

# 形状和层

使用这个库首先要学习的是形状和层这两个概念。形状最好与张量流中的张量相比较，除了它们存在于文字级别。在张量流中，我们可以建立任意维数的张量。手雷目前最多只支持三维。因此，不同的形状类型要么以`D1, D2`开始，要么以`D3`开始，这取决于形状的维度。然后，每个类型构造函数都接受一组自然数参数。因此，以下是手榴弹中所有有效的“形状”类型:

```
D1 5
D2 4 12
D3 8 10 2
```

第一个表示具有 5 个元素的向量。第二个表示 4 行 12 列的矩阵。第三个代表一个 8x10x2 的矩阵(或者张量，如果你喜欢的话)。不同的数字代表类型级别的值**，而不是术语级别的值。如果这看起来令人困惑，这里有一个[很好的教程](https://www.schoolofhaskell.com/user/konn/prove-your-haskell-for-great-safety/dependent-types-in-haskell)，它更深入地讲述了依赖类型的基础知识。最重要的想法是，`D1 5`类型的东西可以**只有**有 5 个元素。4 或 6 个元素的向量不会进行类型检查。**

现在我们知道了形状，让我们检查层。层描述了我们的形状之间的关系。它们封装了发生在我们数据上的转换。以下是所有有效的图层类型:

```
Relu
FullyConnected 10 20
Convolution 1 10 5 5 1 1
```

层`Relu`描述了接收任何形状的数据并输出相同形状的层。在此期间，它将`relu`激活功能应用于输入数据。既然不改变形状，就不需要任何参数。

一个`FullyConnected`层代表神经网络的规范层。它有两个参数，一个用于输入神经元的数量，一个用于输出神经元的数量。在这种情况下，该层将接受 10 个输入并产生 20 个输出。

一个`Convolution`层代表一个 2D 卷积，就像我们在 [MNIST](https://mmhaskell.com/blog/2017/9/4/deeper-still-convolutional-neural-networks) 网络中看到的那样。此特定示例有 1 个输入要素，10 个输出要素，使用 5x5 面片大小和 1x1 面片偏移。

# 描述网络

现在我们对形状和层有了基本的了解，我们可以看到它们是如何组合在一起形成一个完整的网络的。网络类型有两个类型参数。第二个参数是我们的数据在整个网络中任何给定点的形状列表。第一个参数是表示数据变换的图层列表。假设我们想描述一个非常简单的网络。它将采用 4 个输入，并使用全连接层产生 10 个输出。然后它将执行一个`Relu`激活。这个网络看起来像这样:

```
type SimpleNetwork = Network
  ‘[FullyConnected 4 10, Relu]
  ‘[ ‘D1 4, ‘D1 10, ‘D1 10]
```

列表和`D1`术语前面的撇号表示这些是提升的构造函数。所以它们是类型而不是术语。为了“读取”这种类型，我们从第一种数据格式开始。我们通过应用转换层来处理每个连续的数据格式。例如，我们从一个四维向量开始，然后用一个完全连通的层将其转换成一个十维向量。然后我们通过应用`relu`将这个 10-向量转换成另一个 10-向量。这就是全部了！我们可以在上面应用另一个`FullyConnected`层，它将有 3 个输出，如下所示:

```
type SimpleNetwork = Network
  ‘[FullyConnected 4 10, Relu, FullyConnected 10 3]
  ‘[ ‘D1 4, ‘D1 10, ‘D1 10, `D1 3]
```

让我们看看 MNIST，看看一个更复杂的例子。我们将从 28x28 的数据图像开始。然后我们将执行我上面提到的卷积层。这给了我们一个大小为 24x24x10 的三维张量。然后，我们可以在此基础上执行 2x2 max 合并，从而得到 12x12x10 张量。最后，我们可以应用一个`Relu`层，保持它的大小不变:

```
type MNISTStart = MNISTStart
  ‘[Convolution 1 10 5 5 1 1, Pooling 2 2 2 2, Relu]
  ‘[D2 28 28, D3 24 24 10, D3 12 12 10, D3 12 12 10]
```

下面是一个完整的 MNIST 示例(根据库的 Github 页面上的[自述文件](https://github.com/HuwCampbell/grenade)):

```
type MNIST = Network
    '[ Convolution 1 10 5 5 1 1, Pooling 2 2 2 2, Relu
     , Convolution 10 16 5 5 1 1, Pooling 2 2 2 2, FlattenLayer, Relu
     , FullyConnected 256 80, Logit, FullyConnected 80 10, Logit]
    '[ 'D2 28 28, 'D3 24 24 10, 'D3 12 12 10, 'D3 12 12 10
     , 'D3 8 8 16, 'D3 4 4 16, 'D1 256, 'D1 256
     , 'D1 80, 'D1 80, 'D1 10, 'D1 10]
```

这是一个比我们在张量流中得到的对我们网络的更简单和更简洁的描述！让我们来看看这个库使用依赖类型的方式。

# 依赖类型的魔力

如果您以前从未使用过依赖类型，那么将我们的网络描述为类型似乎是一个奇怪的想法。但是它给了我们一些额外的好处！

我们获得的第一个重大胜利是，生成我们网络的起始值非常容易。既然它有特定的类型，我们可以让类型推理来指导我们！我们不需要任何特定于我们网络形态的术语级代码。我们需要做的就是附上类型签名并调用`randomNetwork`！

```
randomSimple :: MonadRandom m => m SimpleNetwork
randomSimple = randomNetwork
```

这会给我们所有需要的初始值，所以我们可以开始了！

第二个(也是更重要的)胜利是我们不能建立一个无效的网络！假设我们试图把我们的简单网络，以某种方式不正确地格式化。例如，我们可以说输入形状的大小不是 4，而是 7:

```
type SimpleNetwork = Network
  ‘[FullyConnected 4 10, Relu, FullyConnected 10 3]
  ‘[ ‘D1 7, ‘D1 10, ‘D1 10, `D1 3]
-- ^^ Notice this 7
```

这将导致编译错误，因为各层之间不匹配。第一层期望输入为 4，但第一个数据格式的长度为 7！

```
Could not deduce (Layer (FullyConnected 4 10) ('D1 7) ('D1 10))
        arising from a use of ‘randomNetwork’
      from the context: MonadRandom m
        bound by the type signature for:
                   randomSimple :: MonadRandom m => m SimpleNetwork
        at src/IrisGrenade.hs:29:1-48
```

换句话说，它注意到使用`FullyConnected 4 10`层从`D1 7`到`D1 10`的链是无效的。所以它不让我们制造这个网络。如果我们使层本身无效，同样的事情也会发生。例如，我们可以使两个完全连接的层的输出和输入不匹配:

```
-- We changed the second to take 20 as the number of input elements.
type SimpleNetwork = Network 
  '[FullyConnected 4 10, Relu, FullyConnected 20 3]
  '[ 'D1 4, 'D1 10, 'D1 20, 'D1 3]…/Users/jamesbowen/HTensor/src/IrisGrenade.hs:30:16: error:
    • Could not deduce (Layer (FullyConnected 20 3) ('D1 10) ('D1 3))
        arising from a use of ‘randomNetwork’
      from the context: MonadRandom m
        bound by the type signature for:
                   randomSimple :: MonadRandom m => m SimpleNetwork
        at src/IrisGrenade.hs:29:1-48
```

因此，通过提供关于网络有效性的编译时保证，Grenade 使我们的程序更加安全。维度导致的运行时错误是不可能的！

# 在 Iris 上培训网络

现在让我们快速浏览一下我们实际上是如何训练这个神经网络的。眼尖的读者可能已经注意到，我们建立的`SimpleNetwork`就是我们用来训练虹膜数据集的同一个网络。因此，我们将在那里进行一次训练，使用以下步骤:

1.  写下网络类型并从中生成一个随机网络
2.  将我们的输入数据读入手雷使用的格式
3.  编写一个运行训练迭代的函数。
4.  运行它！

# 1.写出网络类型并生成网络

所以我们已经在很大程度上完成了第一步。不过我们会稍微调整一下名字。请注意，我将把导入列表作为本文的附录。还有，代码在我的`IrisGrenade.hs`中的 [Haskell 张量流](https://github.com/jhb563/HTensor)库的`[grenade](https://github.com/jhb563/HTensor/tree/grenade)` [分支](https://github.com/jhb563/HTensor/tree/grenade)上！

```
type IrisNetwork = Network 
  '[FullyConnected 4 10, Relu, FullyConnected 10 3]
  '[ 'D1 4, 'D1 10, 'D1 10, 'D1 3]randomIris :: MonadRandom m => m IrisNetwork
randomIris = randomNetworkrunIris :: FilePath -> FilePath -> IO ()
runIris trainingFile testingFile = do
  initialNetwork <- randomIris
  ...
```

# 2.接受我们的输入数据

我们将利用第一次使用 Iris 时使用的`readIrisFromFile`函数。然后我们将创建一个名为`IrisRow`的依赖类型，它使用了`S`类型。这个`S`类型是一个形状的容器。我们希望我们的输入数据对 4 个输入特征使用`D1 4`。那么我们的输出数据应该使用`D1 3`来表示三个可能的类别。

```
-- Dependent type on the dimensions of the row
type IrisRow = (S ('D1 4), S ('D1 3))
```

如果我们有格式错误的数据，类型将不会匹配，所以我们需要返回一个`Maybe`来确保成功。注意，我们通过除以 8 来标准化数据。这会将所有数据放在 0 和 1 之间，从而获得更好的训练结果。我们是这样解析数据的:

```
parseRecord :: IrisRecord -> Maybe IrisRow
parseRecord record = case (input, output) of
  (Just i, Just o) -> Just (i, o)
  _ -> Nothing
  where
    input = fromStorable $ VS.fromList $ float2Double <$>
      [ field1 record / 8.0, field2 record / 8.0, field3 record / 8.0, field4 record / 8.0]
    output = oneHot (fromIntegral $ label record)
```

然后，我们将这些纳入我们的主要功能:

```
runIris :: FilePath -> FilePath -> IO ()
runIris trainingFile testingFile = do
  initialNetwork <- randomIris
  trainingRecords <- readIrisFromFile trainingFile
  testRecords <- readIrisFromFile testingFile let trainingData = mapMaybe parseRecord (V.toList trainingRecords)
  let testData = mapMaybe parseRecord (V.toList testRecords) -- Catch if any were parsed as Nothing
  if length trainingData /= length trainingRecords || length testData /= length testRecords
    then putStrLn "Hmmm there were some problems parsing the data"
    else …
```

# 3.编写一个函数来训练输入数据

这是一个多步骤的过程。首先，我们将建立我们的学习参数。我们还将编写一个函数，允许我们在特定的行元素上调用`train`函数:

```
learningParams :: LearningParameters
learningParams = LearningParameters 0.01 0.9 0.0005-- Train the network!
trainRow :: LearningParameters -> IrisNetwork -> IrisRow -> IrisNetwork
trainRow lp network (input, output) = train lp network input output
```

接下来，我们将编写另外两个帮助函数来帮助我们测试结果。第一个需要网络和一个测试行。它会将其转换为网络的预测输出和实际输出。第二个函数将获取这些输出，并反转`oneHot`过程以获取标签(0、1 或 2)。

```
-- Takes a test row, returns predicted output and actual output from the network.
testRow :: IrisNetwork -> IrisRow -> (S ('D1 3), S ('D1 3))
testRow net (rowInput, predictedOutput) = (predictedOutput, runNet net rowInput)-- Goes from probability output vector to label
getLabels :: (S ('D1 3), S ('D1 3)) -> (Int, Int)
getLabels (S1D predictedLabel, S1D actualOutput) = 
  (maxIndex (extract predictedLabel), maxIndex (extract actualOutput))
```

最后，我们将编写一个函数，它将接受我们的训练数据、测试数据、网络和迭代次数。它将返回新训练的网络，并记录一些关于我们做得如何的结果。我们将首先只取训练数据的一个样本，并调整我们的参数，以便学习变得更慢。然后，我们将通过折叠采样数据来训练网络。

```
run :: [IrisRow] -> [IrisRow] -> IrisNetwork -> Int -> IO IrisNetwork
run trainData testData network iterationNum = do
  sampledRecords <- V.toList <$> chooseRandomRecords (V.fromList trainData)
  -- Slowly drop the learning rate
  let revisedParams = learningParams 
        { learningRate = learningRate learningParams * 0.99 ^ iterationNum}
  let newNetwork = foldl' (trainRow revisedParams) network sampledRecords
  ....
```

然后，我们将通过查看我们的测试数据来总结这个函数，看看我们做对了多少！

```
run :: [IrisRow] -> [IrisRow] -> IrisNetwork -> Int -> IO IrisNetwork
    run trainData testData network iterationNum = do
      sampledRecords <- V.toList <$> chooseRandomRecords (V.fromList trainData)
      -- Slowly drop the learning rate
      let revisedParams = learningParams 
            { learningRate = learningRate learningParams * 0.99 ^ iterationNum}
      let newNetwork = foldl' (trainRow revisedParams) network sampledRecords
      let labelVectors = fmap (testRow newNetwork) testData
      let labelValues = fmap getLabels labelVectors
      let total = length labelValues
      let correctEntries = length $ filter ((==) <$> fst <*> snd) labelValues
      putStrLn $ "Iteration: " ++ show iterationNum
      putStrLn $ show correctEntries ++ " correct out of: " ++ show total
      return newNetwork
```

# 4.运行它！

我们现在从我们的主函数调用它，迭代 100 次，我们就完成了！

```
runIris :: FilePath -> FilePath -> IO ()
runIris trainingFile testingFile = do
 ...
  if length trainingData /= length trainingRecords || length testData /= length testRecords
    then putStrLn "Hmmm there were some problems parsing the data"
    else foldM_ (run trainingData testData) initialNetwork [1..100]
```

# 与张量流相比

现在我们已经看到了一个不同的库，我们可以考虑它是如何与张量流相抗衡的。所以首先，优势。手榴弹的主要优势是它提供了依赖型设施。这意味着编写不正确的程序更加困难。你建立的基本网络保证有正确的维度。此外，它不使用“占位符”系统，因此您也可以避免这些类型的错误。这意味着你可能会有更少的运行时错误使用手雷。

简洁是另一个主要优点。当把我们的数据转换成手榴弹的格式时，训练代码有点复杂。但并不比张量流复杂。当涉及到网络本身的精确定义时，我们只用几行代码就完成了。如果您是依赖类型的新手，理解这些行的含义会很复杂。但是看了几个简单的例子后，你应该能理解大致的模式了。

当然，这并不意味着张量流没有优势。正如我们几周前看到的，在张量流程序中加入非常全面的测井并不困难。张量板应用程序将为您提供这些数据的可视化效果。用手雷获得中间测井结果有些困难。网络的内在价值没有太多的透明度(至少我发现是这样的)。尽管网络类型是可组合的。所以有可能得到你操作的中间步骤。但是如果你把你的网络分成不同的类型，然后把它们缝合在一起，你将会去掉一些网络的简洁。

此外，张量流还有更丰富的机器学习工具生态系统可供访问。手雷仍然局限于最常见的机器学习层的子集，如卷积和最大池。张量流的 API 允许支持向量机和线性模型等方法。所以张量流给了你更多的选择。

我将在以后的文章中探讨的一个问题是比较这两个库的性能。我怀疑张量流更快，因为它是如何把所有的数学都归结到 C 层的。但是我还不太熟悉 HMatrix(手雷的数学依赖于它)和它的效率。所以我肯定是错的。

# 结论

手榴弹提供了一些真正可怕的设施，建立一个简洁的神经网络。一个手榴弹程序可以在编译时证明网络结构良好。它还允许一种难以置信的简洁方式来定义你的神经网络有哪些层。它没有张量流那样的谷歌级支持。所以它缺少很多很酷的功能，比如日志和可视化。但是就其范围而言，它是一个非常整洁的库。我没有提到的一件事是它的生成/对抗网络的机制。我很想尽快尝试一下！

与张量流相比，手榴弹是一个更简单的库，可以合并到堆栈中。如果你想比较这两者，你应该看看我们的 [Haskell 张量流指南](https://www.mmhaskell.com/tensorflow)，这样你就可以安装 TF 并开始使用了！

如果你以前从未写过一行 Haskell，不要害怕！下载我们的[入门清单](https://www.mmhaskell.com/checklist)，获取一些免费资源，开始您的 Haskell 教育！

# 附录:编译器扩展和导入

```
{-# LANGUAGE DataKinds #-}
{-# LANGUAGE BangPatterns #-}
{-# LANGUAGE TupleSections #-}
{-# LANGUAGE GADTs #-}import           Control.Monad (foldM_)
import           Control.Monad.Random (MonadRandom)
import           Control.Monad.IO.Class (liftIO)
import           Data.Foldable (foldl')
import           Data.Maybe (mapMaybe)
import qualified Data.Vector.Storable as VS
import qualified Data.Vector as V
import           GHC.Float (float2Double)
import           Grenade
import           Grenade.Core.LearningParameters (LearningParameters(..))
import           Grenade.Core.Shape (fromStorable)
import           Grenade.Utils.OneHot (oneHot)
import           Numeric.LinearAlgebra (maxIndex)
import           Numeric.LinearAlgebra.Static (extract)import           Processing (IrisRecord(..), readIrisFromFile, chooseRandomRecords)
```