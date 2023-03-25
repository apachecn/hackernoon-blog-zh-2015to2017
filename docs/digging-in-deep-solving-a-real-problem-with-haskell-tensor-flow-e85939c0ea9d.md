# 深入挖掘:用 Haskell 张量流解决一个实际问题

> 原文：<https://medium.com/hackernoon/digging-in-deep-solving-a-real-problem-with-haskell-tensor-flow-e85939c0ea9d>

上周，我们熟悉了张量流的核心概念。我们学习了常量、占位符和变量张量之间的区别。Haskell 和 Python 绑定都有表示这些的函数。不过 Python 版本要简单一些。一旦我们有了张量，我们就写了一个程序来“学习”一个简单的线性方程。

本周，我们将解决一个实际的机器学习问题。我们将使用[鸢尾数据集](https://archive.ics.uci.edu/ml/datasets/iris)，它包含不同鸢尾花的测量值。每一种花属于三个物种中的一个。我们的程序将“学习”一个从测量值中选择物种的函数。这项功能将涉及一个完全连接的神经网络。

# 格式化我们的输入

几乎所有机器学习问题的第一步都是数据处理。毕竟，我们的数据不会神奇地被解析成 Haskell 数据类型。幸运的是，[木薯](https://hackage.haskell.org/package/cassava)是一个很好的图书馆来帮助我们。Iris 数据集由中的数据组成。csv 文件，每个文件都有一个标题行和一系列记录。它们看起来有点像这样:

```
120,4,setosa,versicolor,virginica
6.4,2.8,5.6,2.2,2
5.0,2.3,3.3,1.0,1
4.9,2.5,4.5,1.7,2
4.9,3.1,1.5,0.1,0
...
```

每行包含一条记录。一个记录有四个花测量值和一个最终标签。在这种情况下，我们有三种类型的花，我们试图进行分类:鸢尾、杂色鸢尾和海滨鸢尾。因此，最后一列包含数字 0、1 和 2，分别对应于这些类别。

让我们创建一个代表每条记录的数据类型。然后我们可以逐行解析文件。我们的`IrisRecord`类型将包含特征数据和结果标签。这种类型将作为我们的原始数据和张量格式之间的桥梁，我们将需要运行我们的学习算法。我们将为我们的记录类型派生“通用”typeclass，并使用它来获得`FromRecord`。一旦我们的类型有了`FromRecord`的实例，我们就可以轻松地解析它。注意，在整篇文章中，我将**从代码示例中省略**imports 部分。我在底部的附录中包含了从这些文件导入的完整列表。我们还将全程使用`OverloadedLists`扩展。

```
{-# LANGUAGE DeriveGeneric #-}
{-# LANGUAGE OverloadedLists #-}...data IrisRecord = IrisRecord
  { field1 :: Float
  , field2 :: Float
  , field3 :: Float
  , field4 :: Float
  , label  :: Int64
  }
  deriving (Generic)instance FromRecord IrisRecord
```

现在我们有了自己的类型，我们将编写一个函数，`readIrisFromFile`，它将从一个 CSV 文件中读入我们的数据。

```
readIrisFromFile :: FilePath -> IO (Vector IrisRecord)
readIrisFromFile fp = do
  contents <- readFile fp
  let contentsAsBs = pack contents
  let results = decode 
    HasHeader contentsAsBs :: Either String (Vector IrisRecord)
  case results of
    Left err -> error err
    Right records -> return records
```

我们不想总是将我们的整个数据集输入到我们的训练系统中。因此，给定一系列这些项目，我们应该能够挑选出一个随机样本。

```
sampleSize :: Int
sampleSize = 10chooseRandomRecords :: Vector IrisRecord -> IO (Vector IrisRecord)
chooseRandomRecords records = do
  let numRecords = Data.Vector.length records
  chosenIndices <- 
    take sampleSize <$> shuffleM [0..(numRecords - 1)]
  return $ fromList $ map (records !) chosenIndices
```

一旦我们选择了每次运行使用的记录向量，我们还没有完成。我们需要获取这些记录，并将它们转换成`TensorData`,然后输入到我们的算法中。我们通过输入一个形状和一个一维向量值来创建`TensorData`的项目。首先，我们需要知道输入和输出的形状。这两者都取决于样本中的行数。“输入”将为我们的集合中的四个特性中的每一个都有一列。同时，输出将有一个单独的标签值列。

```
irisFeatures :: Int64
irisFeatures = 4irisLabels :: Int64
irisLabels = 3convertRecordsToTensorData :: Vector IrisRecord 
                           -> (TensorData Float, TensorData Int64)
convertRecordsToTensorData records = (input, output)
  where
    numRecords = Data.Vector.length records 
    input = encodeTensorData 
      [fromIntegral numRecords, irisFeatures] (undefined)
    output = encodeTensorData 
      [fromIntegral numRecords] (undefined)
```

现在我们需要做的就是把各种记录转化成一维向量进行编码。这是最后一个函数:

```
convertRecordsToTensorData :: Vector IrisRecord 
                           -> (TensorData Float, TensorData Int64)
convertRecordsToTensorData records = (input, output)
  where
    numRecords = Data.Vector.length records 
    input = encodeTensorData 
      [fromIntegral numRecords, irisFeatures]
      (fromList $ concatMap recordToInputs records)
    output = encodeTensorData 
      [fromIntegral numRecords] 
      (label <$> records)
    recordToInputs :: IrisRecord -> [Float]
    recordToInputs rec = 
      [field1 rec, field2 rec, field3 rec, field4 rec]
```

# 神经网络基础

现在我们已经解决了这个问题，我们可以开始编写我们的模型了。记住，我们想要用我们的模型执行两个不同的动作。首先，我们希望能够利用我们的训练输入来训练权重。其次，我们希望能够通过一个测试数据集，并确定错误率。我们可以将这两种不同的功能表示为一个单独的`Model`对象。记住`Session`单子，在那里我们运行所有的张量流活动。训练将运行一个改变变量的操作，但不返回任何内容。错误率计算将返回一个浮点值。

```
data Model = Model
  { train :: TensorData Float -- Training input
          -> TensorData Int64 -- Training output
          -> Session ()
  , errorRate :: TensorData Float -- Test input
              -> TensorData Int64 -- Test output
              -> Session Float
  }
```

现在我们要建立一个完全连接的神经网络。我们将有 4 个输入单元(每个不同的特性一个)，然后我们将有 3 个输出单元(我们试图表示的每个类一个)。在中间，我们将使用一个由 10 个单元组成的隐藏层。这意味着我们需要两套权重和偏好。我们将写一个函数，当给定维数时，它将给出每一层的变量张量。我们需要权重和偏差张量，加上层的结果张量。

```
buildNNLayer :: Int64 -> Int64 -> Tensor v Float
             -> Build (Variable Float, Variable Float, Tensor Build Float)
buildNNLayer inputSize outputSize input = do
  weights <- truncatedNormal (vector [inputSize, outputSize]) >>=
    initializedVariable
  bias <- truncatedNormal (vector [outputSize]) >>=
    initializedVariable
  let results = (input `matMul` readValue weights) `add` 
                readValue bias
  return (weights, bias, results)
```

我们在`Build`单子中这样做，单子允许我们构造变量，等等。为了简单起见，我们将对所有变量使用`truncatedNormal`分布。我们在一个`vector`张量中指定每个变量的大小，然后初始化它们。然后，我们将通过将输入乘以我们的权重并添加偏差来创建结果张量。

# 构建我们的模型

现在我们将开始构建我们的`Model`对象，同样是在`Build`单子内。我们首先指定输入和输出占位符，以及隐藏单元的数量。我们还将使用-1 的`batchSize`来说明我们想要可变数量的输入样本。

```
irisFeatures :: Int64
irisFeatures = 4irisLabels :: Int64
irisLabels = 3
-- ^^ From abovecreateModel :: Build Model
createModel = do
  let batchSize = -1 -- Allows variable sized batches
  let numHiddenUnits = 10
  inputs <- placeholder [batchSize, irisFeatures]
  outputs <- placeholder [batchSize]
```

然后我们将得到两层变量的节点，以及它们的结果。在这些层之间，我们将添加一个“整流器”激活函数`relu`:

```
(hiddenWeights, hiddenBiases, hiddenResults) <- 
  buildNNLayer irisFeatures numHiddenUnits inputs
let rectifiedHiddenResults = relu hiddenResults
(finalWeights, finalBiases, finalResults) <-
  buildNNLayer numHiddenUnits irisLabels rectifiedHiddenResults
```

现在我们必须得到每个输出的推断类。这意味着调用`argMax`取概率最高的类。我们也将`cast`向量，然后`render`它。这是一些 Haskell 张量流的特定术语，用于将张量转换成正确的类型。接下来，我们将它与输出占位符进行比较，看看有多少是正确的。然后我们将创建一个节点来计算这次运行的错误率。

```
actualOutput <- render $ cast $ 
  argMax finalResults (scalar (1 :: Int64))
let correctPredictions = equal actualOutput outputs
errorRate_ <- render $ 1 - (reduceMean (cast correctPredictions))
```

现在我们必须实际做训练的工作。首先，我们将为我们的预期输出制作`oneHot`向量。这意味着将标签`0`转换成向量`[1,0,0]`，等等。我们将这些值与我们的结果进行比较(在我们取最大值之前)，这给出了我们的损失函数。然后我们会列出我们想要训练的参数。`adam`优化器将在修改参数时最小化我们的损失函数。

```
let outputVectors = oneHot outputs (fromIntegral irisLabels) 1 0
let loss = reduceMean $ fst $ softmaxCrossEntropyWithLogits finalResults outputVectors
let params = [hiddenWeights, hiddenBiases, finalWeights, finalBiases]
train_ <- minimizeWith adam loss params
```

现在我们已经准备好了`errorRate_`和`train_`节点。这里还有最后一步。我们必须插入占位符值，并创建接收张量数据的函数。还记得上周[的`feed`模式](https://www.mmhaskell.com/blog/2017/8/14/starting-out-with-haskell-tensor-flow)吗？我们在这里再次使用它。最后，我们的模型完成了！

```
return $ Model
  { train = \inputFeed outputFeed -> 
      runWithFeeds
        [ feed inputs inputFeed
        , feed outputs outputFeed
        ]
        train_
  , errorRate = \inputFeed outputFeed -> unScalar <$>
      runWithFeeds
        [ feed inputs inputFeed
        , feed outputs outputFeed
        ]
        errorRate_
  }
```

# 把这一切联系在一起

现在我们将编写运行会话的主函数。它将有三个阶段。在准备阶段，我们将加载我们的数据，并使用`build`函数来获取我们的模型。然后，我们将通过选择样本并将我们的记录转换为数据来训练我们的模型 1000 步。每 100 步，我们将打印输出。最后，我们将通过使用测试数据来确定最终的错误率。

```
runIris :: FilePath -> FilePath -> IO ()
runIris trainingFile testingFile = runSession $ do
  -- Preparation
  trainingRecords <- liftIO $ readIrisFromFile trainingFile
  testRecords <- liftIO $ readIrisFromFile testingFile
  model <- build createModel -- Training
  forM_ ([0..1000] :: [Int]) $ \i -> do
    trainingSample <- liftIO $ chooseRandomRecords trainingRecords
    let (trainingInputs, trainingOutputs) =
          convertRecordsToTensorData trainingSample
    (train model) trainingInputs trainingOutputs
    when (i `mod` 100 == 0) $ do
      err <- (errorRate model) trainingInputs trainingOutputs
      liftIO $ putStrLn $ 
        "Current training error " ++ show (err * 100) liftIO $ putStrLn "" -- Testing
  let (testingInputs, testingOutputs) = 
        convertRecordsToTensorData testRecords
  testingError <- (errorRate model) testingInputs testingOutputs
  liftIO $ putStrLn $ "test error " ++ show (testingError * 100) return ()
```

# 结果

因此，当我们实际运行所有这些输出时，我们将在测试集上得到以下结果。

```
Current training error 60.000004
Current training error 30.000002
Current training error 39.999996
Current training error 19.999998
Current training error 10.000002
Current training error 10.000002
Current training error 19.999998
Current training error 19.999998
Current training error 10.000002
Current training error 10.000002
Current training error 0.0test error 3.333336
```

我们的测试样本大小是 30，所以这意味着我们这次得到了 29/30。虽然每次跑步的结果都不同(我显然使用了我找到的最好的结果)。由于我们的样本量如此之小，我们在这里有很高的熵(有时错误率像 40%)。一般来说，我们希望在更大的测试集上训练更长的时间，这样我们可以得到更一致的结果，但这是一个好的开始。

# 结论

在本文中，我们回顾了使用 Haskell 张量流库创建神经网络的基础知识。我们制作了一个完全连接的神经网络，并输入我们使用`Cassava`库解析的真实数据。这个网络能够学习一个函数来从 Iris 数据集中对花进行分类。考虑到数据量小，我们得到了一些好的结果。

下周再来，我们会看到如何给张量流图添加更多的总结信息。我们将使用张量板应用程序以可视格式查看我们的图表。

关于安装哈斯克尔张量流系统的更多细节，请查看我们的[深度张量流教程](https://www.mmhaskell.com/tensorflow)。它将引导您完成在自己的机器上运行代码的重要步骤。

也许你以前从未尝试过 Haskell，想看看它是什么样子的。也许我已经让你相信 Haskell 实际上是人工智能的未来。在这种情况下，你应该查看一下我们的[入门清单](https://www.mmhaskell.com/checklist)，以获得一些开始学习这门语言的工具。

# 附录:所有进口

Haskell 张量流的文档仍然是正在进行的主要工作。所以我想确保我明确地列出了我们在这里使用的所有不同函数需要导入的模块。

```
import Control.Monad (forM_, when)
import Control.Monad.IO.Class (liftIO)
import Data.ByteString.Lazy.Char8 (pack)
import Data.Csv (FromRecord, decode, HasHeader(..))
import Data.Int (Int64)
import Data.Vector (Vector, length, fromList, (!))
import GHC.Generics (Generic)
import System.Random.Shuffle (shuffleM)import TensorFlow.Core (TensorData, Session, Build, render, runWithFeeds, feed, unScalar, build,
                        Tensor, encodeTensorData)
import TensorFlow.Minimize (minimizeWith, adam)
import TensorFlow.Ops (placeholder, truncatedNormal, add, matMul, relu,
                      argMax, scalar, cast, oneHot, reduceMean, softmaxCrossEntropyWithLogits, 
                      equal, vector)
import TensorFlow.Session (runSession)
import TensorFlow.Variable (readValue, initializedVariable, Variable)
```