# 深度学习和深度类型:张量流和依赖类型

> 原文：<https://medium.com/hackernoon/deep-learning-and-deep-types-tensor-flow-and-dependent-types-7b3ddf10c5a3>

在本系列的[介绍中，我提出的一个要点是 Haskell 是一种安全的语言。我们将在编译时而不是运行时捕捉许多错误。运行时错误通常对系统来说是灾难性的，因此能够减少这些错误是至关重要的。在对自动驾驶汽车或无人机进行编程时尤其如此。这些物体将出现在现实世界中，如果它们发生故障，可能会伤害到人。](https://mmhaskell.com/blog/2017/8/7/the-future-is-functional-haskell-and-the-ai-native-world)

因此，让我们回顾一下我们在过去 3 或 4 周内编写的一些代码。它真的更安全吗？我们会发现答案是，嗯，没那么多。很难验证代码的某些属性。但是 Haskell 中确实存在使代码更安全的工具！在接下来的两篇文章中，我们将对依赖类型进行一些严肃的分析。我们将能够在编译时证明人工智能程序的一些困难的特性！

接下来的三篇文章将关注依赖类型编程。这是一个很难的主题，所以如果您不能完全理解所有的代码示例，也不用担心。让我们的机器学习代码更安全的主要想法才是重要的！所以，事不宜迟，让我们从头开始，看看运行时问题会出现在哪里。

如果你想自己玩这个代码，查看我的 Github 库上的[依赖形状分支](https://github.com/jhb563/HTensor/tree/dependent-shapes)！本文所有代码在 [DepShape.hs](https://github.com/jhb563/HTensor/blob/dependent-shapes/src/DepShape.hs) 中。虽然如果你想让代码运行，你可能还需要让 Haskell 张量流工作。下载我们的 [Haskell 张量流指南](https://www.mmhaskell.com/tensorflow)获取相关说明！

# Python 的问题

Python 作为一种解释型语言，肯定会有运行时错误。当我第一次学习张量流的时候，我碰到了很多这种很常见的东西。对我来说最突出的两个是占位符故障和尺寸不匹配。例如，让我们回想一下第一个例子。我们的代码将有几个占位符，我们在运行会话时会提交这些占位符的值:

```
node1 = tf.placeholder(tf.float32)
node2 = tf.placeholder(tf.float32)
adderNode = tf.add(node1, node2)
sess = tf.Session()
result1 = sess.run(adderNode, {node1: 3, node2: 4.5 })
```

但是没有什么可以阻止我们在不提交值的情况下运行会话。这将导致运行时崩溃:

```
...
sess = tf.Session()
result1 = sess.run(adderNode)
print(result1)
…Terminal Output:InvalidArgumentError (see above for traceback): You must feed a value for placeholder tensor 'Placeholder' with dtype float
   [[Node: Placeholder = Placeholder[dtype=DT_FLOAT, shape=[], _device="/job:localhost/replica:0/task:0/cpu:0"]()]]
```

另一个不时出现的问题是尺寸不匹配。某些运算需要张量维数之间的某种关系。例如，不能将两个不同长度的向量相加:

```
node1 = tf.constant([3.0, 4.0, 5.0], dtype=tf.float32)
node2 = tf.constant([4.0, 16.0], dtype=tf.float32)
additionNode = tf.add(node1, node2)sess = tf.Session()
result = sess.run(additionNode)
print(result)…Terminal Output:ValueError: Dimensions must be equal, but are 3 and 2 for 'Add' (op: 'Add') with input shapes: [3], [2].
```

同样，我们会遇到运行时崩溃。这些似乎是我们可以在编译时解决的问题。

# Haskell 解决了这些问题吗？

但是任何一个仔细阅读我到目前为止所写的 Haskell 代码的人都会发现它并没有解决这些问题！下面回顾一下我们的基本占位符示例:

```
runPlaceholder :: Vector Float -> Vector Float -> IO (Vector Float)
runPlaceholder input1 input2 = runSession $ do
  (node1 :: Tensor Value Float) <- placeholder [1]
  (node2 :: Tensor Value Float) <- placeholder [1]
  let adderNode = node1 `add` node2
  let runStep = \node1Feed node2Feed -> runWithFeeds 
        [ feed node1 node1Feed
        , feed node2 node2Feed
        ] 
        adderNode
  runStep (encodeTensorData [1] input1) (encodeTensorData [1] input2)
```

注意`runWithFeeds`函数是如何获取一系列`Feed`对象的。如果我们提供空列表，代码仍然可以很好地编译。那么它将面临和我们的 Python 代码一样的命运:

```
…
let runStep = \node1Feed node2Feed -> runWithFeeds [] adderNode
…Terminal Output:TensorFlowException TF_INVALID_ARGUMENT "You must feed a value for placeholder tensor 'Placeholder_1' with dtype float and shape [1]\n\t [[Node: Placeholder_1 = Placeholder[dtype=DT_FLOAT, shape=[1], _device=\"/job:localhost/replica:0/task:0/cpu:0\"]()]]"
```

对于维数的第二个例子，我们也可以在 Haskell 中犯这个错误。以下代码编译后将在运行时崩溃:

```
runSimple :: IO (Vector Float)
runSimple = runSession $ do
  let node1 = constant [3] [3 :: Float, 4, 5]
  let node2 = constant [2] [4 :: Float, 5]
  let additionNode = node1 `add` node2
  run additionNode
…Terminal Output:
TensorFlowException TF_INVALID_ARGUMENT "Incompatible shapes: [3] vs. [2]\n\t [[Node: Add_2 = Add[T=DT_FLOAT, _device=\"/job:localhost/replica:0/task:0/cpu:0\"](Const_0, Const_1)]]"
```

在更基本的层面上，我们甚至不需要说出矢量形状的真相！我们可以给一个假的 shape 值，它仍然可以编译！

```
let node1 = constant [3, 2, 3] [3 :: Float, 4, 5]
…Terminal Output:
invalid tensor length: expected 18 got 3
CallStack (from HasCallStack):
  error, called at src/TensorFlow/Ops.hs:299:23 in tensorflow-ops-0.1.0.0-EWsy8DQdciaL8o6yb2fUKR:TensorFlow.Ops
```

# 我们能做得更好吗？

现在，我们做了一些正确的事情。让我们回想一下我们制造神经网络时的`Model`类型。

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

我们将我们的训练步骤公开为一个函数。这个函数强迫用户为占位符提供两个张量。这很好，但是不能保护我们免受尺寸问题的影响。

当试图解决这些问题时，我们可以围绕每个操作编写包装器。像`add`和`matMul`这样的函数可以返回`Maybe`值。但这将是笨重的。我们可以在 Python 中采取同样的步骤。当然，单子允许 Haskell 版本更好地组合。但是如果我们能提前一次检查我们的错误，那就更好了。

如果我们愿意深入挖掘，我们可以解决这些问题！在这篇文章的剩余部分，我们将探索使用依赖类型来确保维度总是正确的。然而，获得正确的占位符要稍微复杂一些！所以我们将把它留到下周的文章中。

# 检查尺寸

目前，我们处理的张量类型在维度上没有类型安全。张量流在与 C 库交互时不提供这些信息。所以不可能在低层次强制执行。但是这并不妨碍我们编写包装器来解决这个问题。

要编写这些包装器，我们需要深入研究依赖类型。我将对正在发生的事情进行高度概括。但是对于一些基础的细节，你应该看看这个教程。我也要对[伦佐·卡博纳拉](https://github.com/k0001)、[exist 库](https://hackage.haskell.org/package/exinst)和其他伟大的 Haskell 作品的作者表示感谢。他帮助我跨越了实现依赖类型的两大知识鸿沟。

# 从属类型介绍:大小向量

引入依赖类型的最简单的例子是大小向量的概念。如果你读了上面的教程，你会看到它们是如何从头开始实现的。一个正常的向量有一个单一的类型参数，指的是向量包含什么类型的项目。有大小的向量有一个额外的类型参数，这个类型指的是向量的大小。例如，以下是有效的大小向量类型:

```
import Data.Vector.Sized (Vector, fromList)vectorWith2 :: Vector 2 Int64
...
vectorWith6 :: Vector 6 Float
...
```

在第一类签名中，`2`不指术语 2。指的是*型* 2。也就是说，我们已经取得了这个术语，并且*将它提升*为只有一个值的类型。这种工作机制令人困惑，但结果是这样的。我们可以尝试将法向量转换成大小向量。但是如果我们的尺寸不匹配，手术就会失败。

```
import Data.Vector.Sized (Vector, fromList)
import GHC.TypeLits (KnownNat)-- fromList :: (KnownNat n) => [a] -> Maybe (Vector n a)-- This results in a “Just” value!
success :: Maybe (Vector 2 Int64)
success = fromList [5,6]-- The sizes don’t match, so we’ll get “Nothing”!
failure :: Maybe (Vector 2 Int64)
failure = fromList [3,1,5]
```

`KnownNat`约束允许我们指定类型`n`引用一个自然数。所以现在我们可以分配一个封装列表大小的类型签名。

# “安全”形状类型

现在我们对依赖类型有了一个非常基本的了解，让我们为张量流想出一个游戏计划。第一步是创建一个新的类型，将形状放入类型签名中。我们将制作一个`SafeShape`类型，模拟大小向量类型。它将存储维度的完整列表，而不是存储单个数字作为类型。我们想创建一个类似这样的 API:

```
-- fromShape :: Shape -> Maybe (SafeShape s)-- Results in a “Just” value
goodShape :: Maybe (SafeShape ‘[2, 2])
goodShape = fromShape (Shape [2,2])-- Results in Nothing
badShape :: Maybe (SafeShape ‘[2,2])
badShape = fromShape (Shape [3,3,2])
```

为此，我们首先定义`SafeShape`类型。这遵循了大小向量的例子。参见下面的**附录**了解本文中使用的编译器扩展和导入。特别是，您需要 GADTs 和数据类型。

```
data SafeShape (s :: [Nat]) where
  NilShape :: SafeShape '[]
  (:--) :: KnownNat m => Proxy m -> SafeShape s -> SafeShape (m ': s)infixr 5 :--
```

现在我们可以定义`toShape`函数了。这将使用代理把我们的`SafeShape`变成一个普通的`Shape`。

```
toShape :: SafeShape s -> Shape
toShape NilShape = Shape []
toShape ((pm :: Proxy m) :-- s) = Shape (fromInteger (natVal pm) : s')
  where
    (Shape s') = toShape s
```

现在对于反方向，我们首先要做一个类`MkSafeShape`。这个类封装了我们可以转换成`SafeShape`类型的所有类型。我们将为所有自然列表定义这个类的实例。

```
class MkSafeShape (s :: [Nat]) where
  mkSafeShape :: SafeShape s
instance MkSafeShape '[] where
  mkSafeShape = NilShape
instance (MkSafeShape s, KnownNat m) => MkSafeShape (m ': s) where
  mkSafeShape = Proxy :-- mkSafeShape
```

现在我们可以使用`MkSafeShape`类定义我们的`fromShape`函数。为了检查它是否有效，我们将比较结果形状和输入形状，确保它们相等。注意，这需要我们定义一个简单的`Eq Shape`实例。

```
instance Eq Shape where
  (==) (Shape s) (Shape r) = s == rfromShape :: forall s. MkSafeShape s => Shape -> Maybe (SafeShape s)
fromShape shape = if toShape myShape == shape
  then Just myShape
  else Nothing
  where
    myShape = mkSafeShape :: SafeShape s
```

既然我们已经为 shape 做了这些，我们可以为`Tensor`创建一个类似的类型，将 Shape 存储为类型参数。

```
data SafeTensor v a (s :: [Nat]) where
  SafeTensor :: (TensorType a) => Tensor v a -> SafeTensor v a s
```

# 使用我们的安全类型

那么这一切给我们带来了什么？我们的下一个目标是创建一个`safeConstant`函数。这将让我们创建一个`SafeTensor`包装一个常数张量并存储形状。请记住，`constant`接受一个形状和一个向量，但不确保它们之间的相关性。我们想要这样的东西:

```
safeConstant :: (TensorType a) => Vector n a -> SafeShape s -> SafeTensor Build a s
safeConstant elems shp = SafeTensor $ constant (toShape shp) (toList elems)
```

这将给定的形状附加到张量上。但是少了一件。我们还想在输入元素的数量和形状之间建立联系。所以形状为`[3,3,2]`的东西应该强迫你输入一个长度为 18 的向量。现在，`n`和`s`之间没有约束。

我们将添加一个名为`ShapeProduct`的类型族。这些实例将说明给定自然列表的正确自然类型是它们的乘积。我们用递归定义第二个实例，所以我们需要`UndecidableInstances`。

```
type family ShapeProduct (s :: [Nat]) :: Nat
type instance ShapeProduct '[] = 1
type instance ShapeProduct (m ': s) = m * ShapeProduct s
```

现在我们几乎完成了这一部分！我们可以通过在`s`和`n`之间的`ShapeProduct`上添加一个约束来修复我们的`safeConstant`函数。

```
safeConstant :: (TensorType a, ShapeProduct s ~ n) => Vector n a -> SafeShape s -> SafeTensor Build a s
safeConstant elems shp = SafeTensor $ constant (toShape shp) (toList elems)
```

现在我们可以写出`safeConstant`函数的简单用法，如下所示:

```
main :: IO (VN.Vector Int64)
main = runSession $ do
  let (shape1 :: SafeShape '[2,2]) = fromJust $ fromShape (Shape [2,2])
  let (elems1 :: Vector 4 Int64) = fromJust $ fromList [1,2,3,4]
  let (constant1 :: SafeTensor Build Int64 '[2,2]) = safeConstant elems1 shape1
  let (SafeTensor t) = constant1
  run t
```

我们在这里使用`fromJust`作为快捷方式。但是在一个真实的程序中，你会读入你的初始张量，并把它们作为`Maybe`值进行检查。运行时失败的可能性仍然存在。但是这个系统有几个优点。第一，不会死机。我们将有机会优雅地处理它。第二，我们预先做所有的错误检查。一旦我们为每件事情分配了类型，所有的失败案例都应该被包含了。

回到上一个例子，我们来改变一些东西。例如，我们可以让向量的长度为 3，而不是 4。我们现在会得到一个编译错误！

```
main :: IO (VN.Vector Int64)
main = runSession $ do
  let (shape1 :: SafeShape '[2,2]) = fromJust $ fromShape (Shape [2,2])
  let (elems1 :: Vector 3 Int64) = fromJust $ fromList [1,2,3]
  let (constant1 :: SafeTensor Build Int64 '[2,2]) = safeConstant elems1 shape1
  let (SafeTensor t) = constant1
  run t… • Couldn't match type ‘4’ with ‘3’
        arising from a use of ‘safeConstant’
    • In the expression: safeConstant elems1 shape1
      In a pattern binding:
        (constant1 :: SafeTensor Build Int64 '[2, 2])
          = safeConstant elems1 shape1
```

# 添加类型安全操作

现在我们已经将形状信息附加到张量上，我们可以定义更安全的数学运算了。很容易编写一个安全的加法函数，确保张量具有相同的形状:

```
safeAdd :: (TensorType a, a /= Bool) => SafeTensor Build a s -> SafeTensor Build a s -> SafeTensor Build a s
safeAdd (SafeTensor t1) (SafeTensor t2) = SafeTensor (t1 `add` t2)
```

这里有一个类似的矩阵乘法函数。它确保我们有二维的形状和尺寸。注意这两个张量共享`n`维度。必须是第一个张量的列维度和第二个张量的行维度:

```
safeMatMul :: (TensorType a, a /= Bool, a /= Int8, a /= Int16, a /= Int64, a /= Word8, a /= ByteString)
   => SafeTensor Build a '[i,n] -> SafeTensor Build a '[n,o] -> SafeTensor Build a '[i,o]
safeMatMul (SafeTensor t1) (SafeTensor t2) = SafeTensor (t1 `matMul` t2)
```

以下是这些功能的实际应用:

```
main2 :: IO (VN.Vector Float)
main2 = runSession $ do
  let (shape1 :: SafeShape '[4,3]) = fromJust $ fromShape (Shape [4,3])
  let (shape2 :: SafeShape '[3,2]) = fromJust $ fromShape (Shape [3,2])
  let (shape3 :: SafeShape '[4,2]) = fromJust $ fromShape (Shape [4,2])
  let (elems1 :: Vector 12 Float) = fromJust $ fromList [1,2,3,4,1,2,3,4,1,2,3,4]
  let (elems2 :: Vector 6 Float) = fromJust $ fromList [5,6,7,8,9,10]
  let (elems3 :: Vector 8 Float) = fromJust $ fromList [11,12,13,14,15,16,17,18]
  let (constant1 :: SafeTensor Build Float '[4,3]) = safeConstant elems1 shape1
  let (constant2 :: SafeTensor Build Float '[3,2]) = safeConstant elems2 shape2
  let (constant3 :: SafeTensor Build Float '[4,2]) = safeConstant elems3 shape3
  let (multTensor :: SafeTensor Build Float '[4,2]) = constant1 `safeMatMul` constant2
  let (addTensor :: SafeTensor Build Float '[4,2]) = multTensor `safeAdd` constant3
  let (SafeTensor finalTensor) = addTensor
  run finalTensor
```

当然，如果我们在任何地方使用了不正确的维度，我们都会得到编译错误。假设我们将`multTensor`改为使用`[4,3]`作为它的类型:

```
• Couldn't match type ‘2’ with ‘3’
      Expected type: SafeTensor Build Float '[4, 3]
        Actual type: SafeTensor Build Float '[4, 2]
    • In the expression: constant1 `safeMatMul` constant2
…
 • Couldn't match type ‘3’ with ‘2’
      Expected type: SafeTensor Build Float '[4, 2]
        Actual type: SafeTensor Build Float '[4, 3]
    • In the expression: multTensor `safeAdd` constant3
…
• Couldn't match type ‘2’ with ‘3’
      Expected type: SafeTensor Build Float '[4, 3]
        Actual type: SafeTensor Build Float '[4, 2]
    • In the second argument of ‘safeAdd’, namely ‘constant3’
```

# 结论

在这个练习中，我们深入了解了 Haskell 中最难学习的主题之一。依赖型一开始会让你晕头转向。但是我们看到了一个具体的例子，说明它们如何允许我们在编译时检测有问题的代码。它们是一种文档形式，也使我们能够验证我们的代码在某些方面是正确的。

类型不能代替测试(尤其是行为测试)。但是在这个例子中，至少有一些不同的测试用例，我们不需要太担心。下周，我们将看到如何应用这些原则来验证占位符。

如果你想了解更多关于使用 Haskell 张量流的细节，你应该看看我们的[张量流指南](https://www.mmhaskell.com/tensorflow)。它将引导你完成向一个简单的堆栈项目添加张量流的基础知识。

也许你以前从未使用过 Haskell，但我已经让你相信依赖类型是未来。如果你想尝试一下，下载我们的[入门清单](https://www.mmhaskell.com/checklist)。您还可以学习如何使用 Stack 创建和组织 Haskell 项目！查看我们的 [Stack 迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)！

# 附录:扩展和导入

```
{-# LANGUAGE GADTs                #-}
{-# LANGUAGE DataKinds            #-}
{-# LANGUAGE KindSignatures       #-}
{-# LANGUAGE TypeOperators        #-}
{-# LANGUAGE ScopedTypeVariables  #-}
{-# LANGUAGE TypeFamilies         #-}
{-# LANGUAGE UndecidableInstances #-}import           Data.ByteString (ByteString)
import           Data.Constraint (Constraint)
import           Data.Int (Int64, Int8, Int16)
import           Data.Maybe (fromJust)
import           Data.Proxy (Proxy(..))
import qualified Data.Vector as VN
import           Data.Vector.Sized (Vector(..), toList, fromList)
import           Data.Word (Word8)
import           GHC.TypeLits (Nat, KnownNat, natVal)
import           GHC.TypeLitsimport           TensorFlow.Core
import           TensorFlow.Core (Shape(..), TensorType, Tensor, Build)
import           TensorFlow.Ops (constant, add, matMul)
import           TensorFlow.Session (runSession, run)
```