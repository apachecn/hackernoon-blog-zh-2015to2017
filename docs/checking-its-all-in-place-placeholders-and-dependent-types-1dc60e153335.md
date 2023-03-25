# 检查是否全部到位:占位符和依赖类型

> 原文：<https://medium.com/hackernoon/checking-its-all-in-place-placeholders-and-dependent-types-1dc60e153335>

上周，我们进入了依赖型的世界。我们把张量和它们在字体层次上的形状联系起来。这给了我们的程序一些额外的类型安全，并允许我们避免某些运行时错误。

本周，我们将解决另一个运行时难题:缺少占位符。我们将添加一些更依赖类型的机器，以确保我们已经插入了所有必要的占位符！但是我们会看到这不像形状那么简单。

为了理解本文中的代码，请看一下我的 Haskell Tensor Flow Github 存储库中的[这个分支](https://github.com/jhb563/HTensor/tree/dependent-placeholders)！本文所有代码都在 [DepShape.hs](https://github.com/jhb563/HTensor/blob/dependent-placeholders/src/DepShape.hs) 中。像往常一样，我在文章的底部列出了必要的编译器扩展和导入。如果你想自己运行代码，你必须先运行 Haskell 和 Tensor Flow。看看我们的 [Haskell 张量流](https://www.mmhaskell.com/tensorflow)指南！

现在开始，让我们提醒自己张量流中的占位符是什么，以及我们如何使用它们。

# 占位符审核

占位符表示在不同的应用程序运行中可能具有不同值的张量。当我们对不同的数据样本进行训练时，经常会出现这种情况。这是我们用 Python 写的一个非常简单的例子。我们将创建一对占位符张量，只提供它们的形状，不提供值。然后当我们实际运行这个会话时，我们将为这些张量中的每一个提供一个值。

```
node1 = tf.placeholder(tf.float32)
node2 = tf.placeholder(tf.float32)
adderNode = tf.add(node1, node2)
sess = tf.Session()
result1 = sess.run(adderNode, {node1: 3, node2: 4.5 })
```

这里的缺点是没有任何东西强迫我们为那些张量提供值！我们可以尝试在没有它们的情况下运行我们的程序，但我们会遇到运行时崩溃:

```
...
sess = tf.Session()
result1 = sess.run(adderNode)
print(result1)
…Terminal Output:InvalidArgumentError (see above for traceback): You must feed a value for placeholder tensor 'Placeholder' with dtype float
   [[Node: Placeholder = Placeholder[dtype=DT_FLOAT, shape=[], _device="/job:localhost/replica:0/task:0/cpu:0"]()]]
```

不幸的是，Haskell 张量流库在这里实际上并没有做得更好。当我们想要填充占位符时，我们提供一个“提要”列表。但是我们的程序仍然会编译，即使我们传递一个空列表！我们会遇到类似的运行时错误:

```
(node1 :: Tensor Value Float) <- placeholder [1]
(node2 :: Tensor Value Float) <- placeholder [1]
let adderNode = node1 `add` node2
let runStep = \node1Feed node2Feed -> runWithFeeds [] adderNode
runStep (encodeTensorData [1] input1) (encodeTensorData [1] input2)
…Terminal Output:TensorFlowException TF_INVALID_ARGUMENT "You must feed a value for placeholder tensor 'Placeholder_1' with dtype float and shape [1]\n\t [[Node: Placeholder_1 = Placeholder[dtype=DT_FLOAT, shape=[1], _device=\"/job:localhost/replica:0/task:0/cpu:0\"]()]]"
```

在 [Iris](https://mmhaskell.com/blog/2017/8/21/digging-in-deep-solving-a-real-problem-with-haskell-tensor-flow) 和 [MNIST](https://mmhaskell.com/blog/2017/9/4/deeper-still-convolutional-neural-networks) 的例子中，我们将对`runWithFeeds`的调用隐藏在我们的神经网络 API 中。我们只提供一个`Model`对象。这个模型对象迫使我们提供预期的输入和输出张量。所以任何使用我们模型的人都不会进行人工`runWithFeeds`呼叫。

```
data Model = Model
  { train :: TensorData Float
          -> TensorData Int64
          -> Session ()
  , errorRate :: TensorData Float
              -> TensorData Int64
              -> SummaryTensor
              -> Session (Float, ByteString)
  }
```

这是一个不错的解决方案！但是有趣的是，我们可以用依赖类型来挑战极限，所以让我们试试吧！

# 添加更多“安全”类型

我们要做的第一步是增加张量流的`TensorData`类型。我们希望它有像`SafeTensor`和`SafeShape`的形状信息。但是我们还会给每条数据附加一个名称。这将允许我们确定用哪个张量来代替数据。在类型级别，我们将这个名称称为`Symbol`。

```
data SafeTensorData a (n :: Symbol) (s :: [Nat]) where
  SafeTensorData :: (TensorType a) => TensorData a -> SafeTensorData a n s
```

接下来，我们需要对我们的`SafeTensor`类型做一些改变。首先，每个`SafeTensor`将获得一个新的类型参数。此参数指的是名称(符号)到形状(仍然是自然列表)的映射。我们称之为占位符列表。所以每个张量都有它所依赖的占位符的类型级信息。每个不同的占位符都有一个名称和一个形状。

```
data SafeTensor v a (s :: [Nat]) (p :: [(Symbol, [Nat])]) where
  SafeTensor :: (TensorType a) => Tensor v a -> SafeTensor v a s p
```

现在，回想一下当我们替换占位符时，我们使用了提要列表。但是这个列表没有关于其提要的名称或维度的信息。让我们创建一个新的类型，包含提要所需的不同元素。它还应该包含关于占位符列表的正确类型信息。定义类型的第一步，这样它就有了它所包含的占位符列表，比如`SafeTensor`。

```
data FeedList (pl :: [(Symbol, [Nat])]) where
```

这个结构看起来像一个链表，就像我们的`SafeShape`。因此，我们将从定义一个“空”构造函数开始:

```
data FeedList (pl :: [(Symbol, [Nat])]) where
  EmptyFeedList :: FeedList '[]
```

现在我们将通过创建另一个类型操作符`:--:`来添加一个类似“Cons”的构造函数。我们的链表的每个“片段”将包含两个不同的条目。首先，我们代入的张量。接下来，它将包含我们将用于替换的数据。我们可以使用类型参数来强制它们的形状和数据类型匹配。然后我们需要得到的占位符类型。我们必须将包含符号和形状的类型元组添加到前面的列表中。这就完成了我们的定义。

```
data FeedList (pl :: [(Symbol, [Nat])]) where
  EmptyFeedList :: FeedList '[]
  (:--:) :: (KnownSymbol n)
    => (SafeTensor Value a s p, SafeTensorData a n s) 
    -> FeedList pl
    -> FeedList ( '(n, s) ': pl)infixr 5 :--:
```

注意，我们强制张量是一个`Value`张量。我们只能用数据代替渲染的张量，因此有这个限制。让我们添加一个快速的`safeRender`，这样我们就可以渲染我们的`SafeTensor`项目。

```
safeRender :: (MonadBuild m) => SafeTensor Build a s pl -> m (SafeTensor Value a s pl)
safeRender (SafeTensor t1) = do
  t2 <- render t1
  return $ SafeTensor t2
```

# 制作占位符

现在我们可以编写我们的`safePlaceholder`函数了。我们将添加一个`KnownSymbol`作为类型约束。然后我们用一个`SafeShape`给我们自己形状的类型信息。结果是一个新的张量，它映射占位符列表中的符号和形状。

```
safePlaceholder :: (MonadBuild m, TensorType a, KnownSymbol sym) => 
  SafeShape s -> m (SafeTensor Value a s '[ '(sym, s)])
safePlaceholder shp = do
  pl <- placeholder (toShape shp)
  return $ SafeTensor pl
```

这看起来有点疯狂，而且有点疯狂！但是我们现在已经创建了一个张量，它在类型级别存储自己的占位符信息！

# 更新旧代码

现在我们已经这样做了，我们还必须更新一些旧的代码。第一部分非常简单。我们需要更改`safeConstant`使其具有类型信息。它将有一个空的占位符列表。

```
safeConstant :: (TensorType a, ShapeProduct s ~ n) => 
  Vector n a -> SafeShape s -> SafeTensor Build a s '[]
safeConstant elems shp = SafeTensor (constant (toShape shp) (toList elems))
```

不过，我们的数学运算将会有点棘手。考虑添加两个任意的张量。它们可能共享占位符依赖关系，但可能不共享。得到的张量的占位符类型应该是什么？显然是输入张量的两个占位符映射的结合！幸运的是，我们可以使用`type-list`库中的`Union`来表示这个概念。

```
safeAdd :: (TensorType a, a /= Bool, TensorKind v)
  => SafeTensor v a s p1
  -> SafeTensor v a s p2
  -> SafeTensor Build a s (Union p1 p2)
safeAdd (SafeTensor t1) (SafeTensor t2) = SafeTensor (t1 `add` t2)
```

我们将使用矩阵乘法进行同样的更新:

```
safeMatMul :: (TensorType a, a /= Bool, a /= Int8, a /= Int16,
               a /= Int64, a /= Word8, a /= ByteString, TensorKind v)
   => SafeTensor v a '[i,n] p1 -> SafeTensor v a '[n,o] p2 -> SafeTensor Build a '[i,o] (Union p1 p2)
safeMatMul (SafeTensor t1) (SafeTensor t2) = SafeTensor (t1 `matMul` t2)
```

# 使用占位符运行

现在我们已经有了编写`safeRun`函数所需的所有信息。这将采用`SafeTensor`，也将采用相同占位符类型的`FeedList`。记住，一个`FeedList`包含一系列`SafeTensorData`项。它们必须将符号对符号和形状对形状与`SafeTensor`中的占位符相匹配。让我们看看类型签名:

```
safeRun :: (TensorType a, Fetchable (Tensor v a) r) =>
  FeedList pl -> SafeTensor v a s pl -> Session r
```

`Fetchable`约束强制我们实际上可以从张量中得到“结果”`r`。例如，我们可以从使用`Float`作为其基础值的张量中“提取”一个浮点向量。

接下来，我们将定义一个尾部递归帮助器函数来构建我们的`FeedList`的普通“提要列表”。通过模式匹配，我们可以选择要替换的张量和我们正在使用的数据。我们可以将这些合并到一个提要中，并添加到不断增长的列表中:

```
safeRun = ...
  where
    buildFeedList :: FeedList ss -> [Feed] -> [Feed]
    buildFeedList EmptyFeedList accum = accum
    buildFeedList ((SafeTensor tensor_, SafeTensorData data_) :--: rest) accum = 
      buildFeedList rest ((feed tensor_ data_) : accum)
```

现在我们要做的就是用我们创建的列表调用正常的`runWithFeeds`函数！

```
safeRun :: (TensorType a, Fetchable (Tensor v a) r) =>
  FeedList pl -> SafeTensor v a s pl -> Session r
safeRun feeds (SafeTensor finalTensor) = runWithFeeds (buildFeedList feeds []) finalTensor
  where
  ...
```

以下是在我们的简单示例中实际使用它的情况。请注意，类型签名确实有点麻烦。我们在初始占位符张量上的签名是必要的。否则编译器不会知道我们给了他们什么标签！包含类型联合的签名是不必要的。如果我们愿意，我们可以删除它，让类型推理来完成它的工作。

```
main3 :: IO (VN.Vector Float)
main3 = runSession $ do
  let (shape1 :: SafeShape '[2,2]) = fromJust $ fromShape (Shape [2,2])
  (a :: SafeTensor Value Float '[2,2] '[ '("a", '[2,2])]) <- safePlaceholder shape1
  (b :: SafeTensor Value Float '[2,2] '[ '("b", '[2,2])] ) <- safePlaceholder shape1
  let result = a `safeAdd` b
  (result_ :: SafeTensor Value Float '[2,2] '[ '("b", '[2,2]), '("a", '[2,2])]) <- safeRender result
  let (feedA :: Vector 4 Float) = fromJust $ fromList [1,2,3,4]
  let (feedB :: Vector 4 Float) = fromJust $ fromList [5,6,7,8]
  let fullFeedList = (b, safeEncodeTensorData shape1 feedB) :--:
                     (a, safeEncodeTensorData shape1 feedA) :--:
                     EmptyFeedList
  safeRun fullFeedList result_{- It runs!
[6.0,8.0,10.0,12.0]
-}
```

现在假设我们的类型犯了一些错误。在这里，我们将从提要列表中删除“A”提要:

```
-- Let’s take out Feed A!
main = …
  let fullFeedList = (b, safeEncodeTensorData shape1 feedB) :--:
                     EmptyFeedList
  safeRun fullFeedList result_{- Compiler Error!
• Couldn't match type ‘'['("a", '[2, 2])]’ with ‘'[]’
      Expected type: SafeTensor Value Float '[2, 2] '['("b", '[2, 2])]
        Actual type: SafeTensor
                       Value Float '[2, 2] '['("b", '[2, 2]), '("a", '[2, 2])]
-}
```

以下是当我们试图用错误的大小替换向量时会发生的情况。它会发现我们的元素数量不对！

```
main = …
  -- Wrong Size!
  let (feedA :: Vector 8 Float) = fromJust $ fromList [1,2,3,4,5,6,7,8]
  let (feedB :: Vector 4 Float) = fromJust $ fromList [5,6,7,8]
  let fullFeedList = (b, safeEncodeTensorData shape1 feedB) :--:
                     (a, safeEncodeTensorData shape1 feedA) :--:
                     EmptyFeedList
  safeRun fullFeedList result_{- Compiler Error!
Couldn't match type ‘4’ with ‘8’
        arising from a use of ‘safeEncodeTensorData’
-}
```

# 结论:赞成和反对

让我们后退一步，看看我们在这里构建了什么。我们已经设法为自己提供了一些非常酷的编译时保证。我们还在代码中添加了事实上的文档。任何熟悉代码库的人一眼就能看出每个张量需要什么占位符。现在编写不正确的代码要困难得多。当然，仍然存在错误条件。但是如果我们聪明的话，我们可以编写代码来预先处理这些问题。这样我们可以优雅地失败，而不是在某个地方抛出一个随机的运行时崩溃。

但是也有缺点。想象一下，作为一个 Haskell 新手，走进这个代码库。你不会真正知道发生了什么(两个月前我不会知道)。过一段时间后，这些类型会变得非常麻烦，所以继续写下来会变得非常乏味。尽管我提到过，类型推理可以处理很多这样的问题。但是如果你不跟踪它们，类型联合可能会对占位符的顺序非常挑剔。不过我们可以用另一种类型的家庭来解决这个问题。

所有这些因素都可能真正阻碍发展。但是话说回来，跟踪运行时错误也可以做到这一点。张量流的错误信息仍然有点含糊不清。这使得很难找到根本原因。

由于我仍然是依赖类型的新手，这段代码有点乱。下周我们将会看到一个更好的库，它使用了神经网络的依赖类型。我们将看到[手榴弹](https://github.com/HuwCampbell/grenade)库如何让我们在短短几行代码中指定一个学习系统！

如果您是 Haskell 的新手，我希望这种依赖型的疯狂不会吓到您！这门语言比最近几篇帖子看起来要简单得多！尝试一下，并下载我们的[入门清单](https://www.mmhaskell.com/checklist)。它会给你一些指导和工具来帮助你学习！

如果你是一个有经验的 Haskeller 并且想尝试张量流，下载我们的[张量流指南](https://www.mmhaskell.com/tensorflow)！它将引导您将这个库合并到一个堆栈项目中！

# 附录:编译器扩展和导入

```
{-# LANGUAGE GADTs                #-}
{-# LANGUAGE DataKinds            #-}
{-# LANGUAGE KindSignatures       #-}
{-# LANGUAGE TypeOperators        #-}
{-# LANGUAGE ScopedTypeVariables  #-}
{-# LANGUAGE TypeFamilies         #-}
{-# LANGUAGE FlexibleContexts     #-}
{-# LANGUAGE UndecidableInstances #-}import           Data.ByteString (ByteString)
import           Data.Int (Int64, Int8, Int16)
import           Data.Maybe (fromJust)
import           Data.Proxy (Proxy(..))
import           Data.Type.List (Union)
import qualified Data.Vector as VN
import           Data.Vector.Sized (Vector, toList, fromList)
import           Data.Word (Word8)
import           GHC.TypeLits (Nat, KnownNat, natVal)
import           GHC.TypeLitsimport           TensorFlow.Core
import           TensorFlow.Core (Shape(..), TensorType, Tensor, Build)
import           TensorFlow.Ops (constant, add, matMul, placeholder)
import           TensorFlow.Session (runSession, run)
import           TensorFlow.Tensor (TensorKind)
```