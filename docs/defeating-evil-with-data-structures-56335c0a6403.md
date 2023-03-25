# 用数据结构战胜邪恶！

> 原文：<https://medium.com/hackernoon/defeating-evil-with-data-structures-56335c0a6403>

在上周的文章的[中，我们使用基准测试来确定我们的代码在某些输入上的表现。首先，我们使用标准库来获取代码的一些度量。然后，我们能够在一些漂亮的输出中查看这些测量。我们还分析了我们的代码，试图确定是哪部分代码降低了我们的速度。](https://www.mmhaskell.com/blog/2017/6/19/profiling-in-haskell)

分析输出突出显示了两个耗费大量时间的函数。当我们分析它们时，我们发现它们效率很低。在本文中，我们将解决这些问题，并以几种不同的方式改进我们的代码。首先，我们将使用一个数组而不是一个列表来使我们的值访问更快。然后，我们将添加一个很酷的数据结构，称为段树。这将帮助我们快速获得特定间隔内的最小高度值。

本系列文章中的代码示例很好地利用了堆栈工具。如果您以前从未使用过 Stack，您应该看看我们新推出的免费 [Stack 迷你课程](http://academy.mondaymorninghaskell/p/your-first-haskell-project)。它将带您了解组织代码、获取依赖关系和运行命令的基本知识。

# 哪里出了问题？

所以首先让我们花点时间提醒自己为什么这些功能会让我们慢下来。我们的最小高度函数和索引函数的值都在`O(n)`时间内运行。这意味着在最坏的情况下，他们每个人都可以扫描整个列表。接下来，我们观察到这两个函数都将被调用`O(n)`次。这样我们的总算法将是`O(n^2)`时间。我们采用的时间基准支持了这一理论。

我们上面提到的数据结构将帮助我们在不进行全面扫描的情况下获得我们需要的值。我们将从用一个数组替换我们的列表开始，因为这要容易得多。

# 数组

当我们解决函数式编程问题时，链表是非常常见的。它们有一些很好的属性，并且非常适合递归。但是，它们不允许通过索引进行快速访问。对于这些情况，我们需要使用数组。数组在 Haskell 中不像其他语言那样常见，也有一些不同。

首先，Haskell 数组有两个类型参数。当你在 Java 中创建一个数组时，你可以说它是一个 int 数组(`int[]`)还是一个 string 数组(`String[]`)，或者其他任何类型。所以这只是一个单一的参数。每当我们想对数组进行索引时，我们总是使用整数。

在 Haskell 中，我们可以选择数组存储的类型和索引数组的类型。现在，索引类型必须属于 index ( `Ix` ) typeclass。在这种情况下，我们将使用`Int`。但是知道你有更多的灵活性是很酷的。例如，考虑表示一个矩阵。在 Java 中，我们必须使用“数组的数组”。这涉及到许多笨拙的语法。在 Haskell 中，我们可以使用由整数元组索引的单个数组！如果我们愿意，我们也可以从 1 开始而不是从 0 开始索引。

所以对于我们的问题，我们将使用`Array Int Int`作为我们的内部栅栏值，而不是一个普通的列表。不过，我们只需要对代码做一些修改！首先，我们将导入几个模块，并更改我们的类型以使用数组:

```
import Data.Array
import Data.Ix (range)...newtype FenceValues = FenceValues { unFenceValues :: Array Int Int }
```

接下来，我们将使用专门的数组索引`(!)`操作符来访问它们，而不是使用`(!!)`来按索引访问。

```
valueAtIndex :: FenceValues -> FenceIndex -> Int
valueAtIndex values index = (unFenceValues values) ! (unFenceIndex index)
```

最后，我们来改进一下我们的`minimumHeight`功能。我们现在将在数组上使用`range`函数，而不是求助于`drop`和`take`。注意，我们现在使用`right - 1`，因为我们想排除区间的右端点。

```
where
    valsInInterval :: [(FenceIndex, Int)]
    valsInInterval = zip 
      (FenceIndex <$> intervalRange) 
      (map ((unFenceValues values) !) intervalRange)
      where
        intervalRange = range (left, right - 1)
```

我们还必须改变我们的基准测试代码，以产生数组而不是列表:

```
import Data.Array(listArray)…randomList :: Int -> IO FenceValues
randomList n = FenceValues . mkListArray <$> 
  (sequence $ replicate n (randomRIO (1, 10000 :: Int)))
  where
    mkListArray vals = listArray (0, (length vals) - 1) vals
```

我们的库和我们的基准现在都需要在 Cabal 文件的`build-depends`部分使用`array`。我们需要确保添加这个！一旦我们做到了，我们可以再次对我们的代码进行基准测试，我们会发现它已经加快了不少！

```
>> stack bench --profile
Running 1 benchmarks...
Benchmark fences-benchmarks: RUNNING...
benchmarking fences tests/Size 1 Test
time                 49.33 ns   (48.98 ns .. 49.71 ns)
                     1.000 R²   (0.999 R² .. 1.000 R²)
mean                 49.46 ns   (49.16 ns .. 49.86 ns)
std dev              1.105 ns   (861.0 ps .. 1.638 ns)
variance introduced by outliers: 33% (moderately inflated)benchmarking fences tests/Size 10 Test
time                 4.541 μs   (4.484 μs .. 4.594 μs)
                     0.999 R²   (0.998 R² .. 1.000 R²)
mean                 4.496 μs   (4.456 μs .. 4.531 μs)
std dev              132.0 ns   (109.6 ns .. 164.3 ns)
variance introduced by outliers: 36% (moderately inflated)benchmarking fences tests/Size 100 Test
time                 79.81 μs   (79.21 μs .. 80.45 μs)
                     0.999 R²   (0.999 R² .. 1.000 R²)
mean                 79.51 μs   (78.93 μs .. 80.39 μs)
std dev              2.396 μs   (1.853 μs .. 3.449 μs)
variance introduced by outliers: 29% (moderately inflated)benchmarking fences tests/Size 1000 Test
time                 1.187 ms   (1.158 ms .. 1.224 ms)
                     0.995 R²   (0.992 R² .. 0.998 R²)
mean                 1.170 ms   (1.155 ms .. 1.191 ms)
std dev              56.61 μs   (48.02 μs .. 70.28 μs)
variance introduced by outliers: 37% (moderately inflated)benchmarking fences tests/Size 10000 Test
time                 15.03 ms   (14.71 ms .. 15.32 ms)
                     0.997 R²   (0.994 R² .. 0.999 R²)
mean                 15.71 ms   (15.44 ms .. 16.03 ms)
std dev              729.7 μs   (569.3 μs .. 965.4 μs)
variance introduced by outliers: 16% (moderately inflated)benchmarking fences tests/Size 100000 Test
time                 191.4 ms   (189.2 ms .. 193.9 ms)
                     1.000 R²   (1.000 R² .. 1.000 R²)
mean                 189.3 ms   (188.2 ms .. 190.5 ms)
std dev              1.471 ms   (828.0 μs .. 1.931 ms)
variance introduced by outliers: 14% (moderately inflated)Benchmark fences-benchmarks: FINISH
```

以下是乘法因子:

```
Size 1: 49.33 ns
Size 10: 4.451 μs (increased ~90x)
Size 100: 79.81 μs (increased ~18x)
Size 1000: 1.187 ms (increased ~15x)
Size 10000: 15.03 ms (increased ~13x)
Size 100000: 191.4 ms (increased ~13x)
```

对于后一种情况，将大小增加 10 倍似乎只会将时间增加 13-15 倍。我们认为我们已经达到了`O(n log n)`时间，这是情有可原的！

# 更好的测试案例

但是有些事情还是不对劲。我们必须记住，这个理论并不能完全证明我们在这里的兴奋。事实上，我们的旧代码非常糟糕，正常情况是`O(n^2)`。现在看起来我们可能已经得到了一般情况下的`O(n log n)`。但是我们想尽可能为最坏的情况做准备。想象我们的代码被我们邪恶的对手使用:

![](img/5a4513b48c674730e9f184d938305600.png)

[Photo credit](http://imgur.com/gallery/U0F0vK4)

他会找到最糟糕的情况！在这种情况下，当输入高度列表被排序时，我们的代码将不会如此高效！

```
main :: IO ()
main = do
  [l1, l2, l3, l4, l5, l6] <- mapM 
    randomList [1, 10, 100, 1000, 10000, 100000]
  let l7 = sortedList
  defaultMain
    [ bgroup "fences tests" 
        ...
       , bench "Size 100000 Test" $ whnf largestRectangle l6
      , bench "Size 100000 Test (sorted)" $ whnf largestRectangle l7
      ]
    ]...sortedList :: FenceValues
sortedList = FenceValues $ listArray (0, 99999) [1..100000]
```

我们将再次发现，最后一种情况需要花费很长时间，并且我们将看到运行时间的大幅增加。

```
>> stack bench --profile
Running 1 benchmarks...
Benchmark fences-benchmarks: RUNNING...…benchmarking fences tests/Size 100000 Test (sorted)
time                 378.1 s    (355.0 s .. 388.3 s)
                     1.000 R²   (0.999 R² .. 1.000 R²)
mean                 384.5 s    (379.3 s .. 387.2 s)
std dev              4.532 s    (0.0 s .. 4.670 s)
variance introduced by outliers: 19% (moderately inflated)Benchmark fences-benchmarks: FINISH
```

平均每例 6 分多钟！但是这一次，我们将看到分析输出发生了变化。它只调出`minimumHeightIndexValue`的各个部分！我们不再花很多时间在`valueAtIndex`上。

```
COST CENTRE                                          %time %allocminimumHeightIndexValue.valsInInterval               65.0   67.7
minimumHeightIndexValue                              22.4    0.0
minimumHeightIndexValue.valsInInterval.intervalRange 12.4   32.2
```

所以现在我们必须通过改进最小值的计算来解决这个新问题。

# 分割树

我们当前的方法仍然需要我们查看区间中的每个元素。即使我们的一些间隔会很小，也会有很多这样的更小的调用，所以总时间仍然是`O(n^2)`。我们需要一种方法在给定的时间间隔内找到最小的项目和值，而不需要求助于线性扫描。

一个想法是从一开始就列出这个问题所有答案的详尽清单。我们可以从所有可能的区间映射到区间中的最小索引和值。但这最终对我们没有帮助。还有`n^2`个可能的区间。所以创建这个数据结构仍然意味着我们的代码需要花费`O(n^2)`时间。

但是我们的想法是正确的，即提前做一些工作。我们将不得不使用一个数据结构，虽然它不是一个详尽的列表。输入细分树。

细分树的结构与二叉查找树相同。每个节点对应一个区间，而不是存储一个值。每个节点将存储其时间间隔、该时间间隔内的最小值以及该值的索引。

树上的顶部节点将指整个阵列的间隔。它将存储最小值和索引的对。那么它将有两个子节点。左边的将在树的前半部分有最小对，右边的将有后半部分。下一层将把它分成四份，以此类推。

例如，让我们考虑如何确定从第一个四分之一点开始到第三个四分之一点结束的最小对。我们将使用递归来实现。首先，我们将向左子树请求从四分之一点到半点的区间上的最小对。然后，我们将查询从半点到四分之三点的最小对的右树。然后我们可以把最小的那个拿回来。我不会在这里深入所有的理论，但事实证明，即使在最坏的情况下，这个操作也需要`O(log n)`时间。

# 设计我们的细分树

在 [hackage](https://hackage.haskell.org/package/SegmentTree-0.3/docs/Data-SegmentTree.html) 上有一个叫`Data.SegmentTree`的图书馆。但是我认为从头开始实现这个数据结构会很有趣。我们将从`SegmentTreeNodes`开始构建我们的树。每个节点要么是空的，要么包含六个字段。前两个是指节点跨越的时间间隔。下一个将是最小值和该值在区间上的索引。然后这个节点的每个子节点都有字段:

```
data SegmentTreeNode = ValueNode
  { fromIndex :: FenceIndex
  , toIndex :: FenceIndex
  , value :: Int
  , minIndex :: FenceIndex
  , leftChild :: SegmentTreeNode
  , rightChild :: SegmentTreeNode
  }
  | EmptyNode
```

我们可以使这种细分树类型更加通用，这样它就不会局限于我们的栅栏问题。我鼓励你把这段代码当作一个练习来尝试！

# 构建细分树

现在，我们将添加预处理步骤，实际构建树本身。这将使用我们之前看到的相同的间隔/尾部模式。在基本情况下，间隔的跨度仅为 1，因此我们创建一个包含该值的节点，其中包含空的子节点。我们还将添加一个返回`EmptyNode`的 catchall:

```
buildSegmentTree :: Array Int Int -> SegmentTreeNode
buildSegmentTree ints = buildSegmentTreeTail 
  ints 
  (FenceInterval ((FenceIndex 0), (FenceIndex (length (elems ints)))))buildSegmentTreeTail :: Array Int Int -> FenceInterval -> SegmentTreeNode
buildSegmentTreeTail array
  (FenceInterval (wrappedFromIndex@(FenceIndex fromIndex), wrappedToIndex@(FenceIndex toIndex)))
  | fromIndex + 1 == toIndex = ValueNode 
      { fromIndex = wrappedFromIndex
      , toIndex = wrappedToIndex
      , value = array ! fromIndex
      , minIndex = wrappedFromIndex
      , leftChild = EmptyNode
      , rightChild = EmptyNode
      }
  | … missing case
  | otherwise = EmptyNode
```

现在我们的中间案例将成为标准案例。首先，我们将把区间分成两半，进行两次递归调用。

```
where 
  average = (fromIndex + toIndex) `quot` 2
  -- Recursive Calls
  leftChild = buildSegmentTreeTail 
    array (FenceInterval (wrappedFromIndex, (FenceIndex average)))
  rightChild = buildSegmentTreeTail 
    array (FenceInterval ((FenceIndex average), wrappedToIndex))
```

接下来，我们将编写一个函数来提取最小值和索引，但处理空节点的情况:

```
-- Get minimum val and index, but account for empty case.
valFromNode :: SegmentTreeNode -> (Int, FenceIndex)
valFromNode EmptyNode = (maxBound :: Int, FenceIndex (-1))
valFromNode n@ValueNode{} = (value n, minIndex n)
```

现在我们将比较这三种情况的最小值。很可能是左边或右边的值。否则就是当前值。

```
leftCase = valFromNode leftChild
rightCase = valFromNode rightChild
currentCase = (array ! fromIndex, wrappedFromIndex)
(newValue, newIndex) = min (min leftCase rightCase) currentCase
```

最后，我们将通过填充中间/正常情况下缺失的变量来完成我们的定义。下面是完整的功能:

```
buildSegmentTreeTail :: Array Int Int -> FenceInterval -> SegmentTreeNode
buildSegmentTreeTail array
  (FenceInterval (wrappedFromIndex@(FenceIndex fromIndex), wrappedToIndex@(FenceIndex toIndex)))
  | fromIndex + 1 == toIndex = ValueNode 
      { fromIndex = wrappedFromIndex
      , toIndex = wrappedToIndex
      , value = array ! fromIndex
      , minIndex = wrappedFromIndex
      , leftChild = EmptyNode
      , rightChild = EmptyNode
      }
  | fromIndex < toIndex = ValueNode 
    { fromIndex = wrappedFromIndex
    , toIndex = wrappedToIndex
    , value = newValue
    , minIndex = newIndex
    , leftChild = leftChild
    , rightChild = rightChild
    }
  | otherwise = EmptyNode
    where 
      average = (fromIndex + toIndex) `quot` 2
      -- Recursive Calls
      leftChild = buildSegmentTreeTail 
        array (FenceInterval (wrappedFromIndex, (FenceIndex average)))
      rightChild = buildSegmentTreeTail 
        array (FenceInterval ((FenceIndex average), wrappedToIndex)) -- Get minimum val and index, but account for empty case.
      valFromNode :: SegmentTreeNode -> (Int, FenceIndex)
      valFromNode EmptyNode = (maxBound :: Int, FenceIndex (-1))
      valFromNode n@ValueNode{} = (value n, minIndex n) leftCase = valFromNode leftChild
      rightCase = valFromNode rightChild
      currentCase = (array ! fromIndex, wrappedFromIndex)
      (newValue, newIndex) = min (min leftCase rightCase) currentCase
```

# 寻找最小值

现在让我们写出在给定区间内求最小值的临界函数。我们将添加我们的树作为另一个参数。然后，我们将处理`EmptyNode`案例，然后展开我们在完整案例中的值:

```
minimumHeightIndexValue :: FenceValues -> SegmentTreeNode -> FenceInterval -> (FenceIndex, Int)
minimumHeightIndexValue values tree 
  originalInterval@(FenceInterval (FenceIndex left, FenceIndex right)) =
  case tree of
    EmptyNode -> (maxBound :: Int, -1)
    ValueNode
      { fromIndex = FenceIndex nFromIndex
      , toIndex = FenceIndex nToIndex
      , value = nValue
      , minIndex = nMinIndex
      , leftChild = nLeftChild
      , rightChild = nRightChild} ->
```

接下来，我们将处理基本情况，即我们位于正确的节点:

```
| left == nFromIndex && right == nToIndex = (nValue, nMinIndex)
```

接下来，我们将观察只需要一次递归调用的两种情况。如果右边的索引低于中间点，我们递归调用左边的子节点。如果左边的指数高于中间点，我们将调用右边的指数(稍后我们将计算平均值)。

```
| otherwise = if right < average 
  then minimumHeightIndexValue values nLeftChild originalInterval
  else if left >= average
    then minimumHeightIndexValue values nRightChild originalInterval
```

最后，我们有一个棘手的部分。如果区间越过了中间标记，我们将不得不把它分成两个子区间。然后我们将进行两个递归调用，并得到它们的解。最后，我们将比较这两种解决方案，选择较小的一个。

```
else minTuple leftResult rightResult
  where
    average = (nFromIndex + nToIndex) `quot` 2
    leftResult = minimumHeightIndexValue values nLeftChild
      (FenceInterval (FenceIndex left, FenceIndex average))
    rightResult = minimumHeightIndexValue values nRightChild
      (FenceInterval (FenceIndex average, FenceIndex right))
    minTuple :: (FenceIndex, Int) -> (FenceIndex, Int) -> (FenceIndex, Int)
    minTuple old@(_, heightOld) new@(_, heightNew) =
      if heightNew < heightOld then new else old
```

为了清楚起见，下面是完整的函数:

```
minimumHeightIndexValue :: FenceValues -> SegmentTreeNode -> FenceInterval -> (FenceIndex, Int)
minimumHeightIndexValue values tree 
  originalInterval@(FenceInterval (FenceIndex left, FenceIndex right)) =
  case tree of
    EmptyNode -> (maxBound :: Int, -1)
    ValueNode
      { fromIndex = FenceIndex nFromIndex
      , toIndex = FenceIndex nToIndex
      , value = nValue
      , minIndex = nMinIndex
      , leftChild = nLeftChild
      , rightChild = nRightChild} ->
        | left == nFromIndex && right == nToIndex = (nValue, nMinIndex)
        | otherwise = if right < average 
          then minimumHeightIndexValue values nLeftChild originalInterval
          else if left >= average
            then minimumHeightIndexValue values nRightChild originalInterval
            else minTuple leftResult rightResult
          where
            average = (nFromIndex + nToIndex) `quot` 2
            leftResult = minimumHeightIndexValue values nLeftChild
              (FenceInterval (FenceIndex left, FenceIndex average))
            rightResult = minimumHeightIndexValue values nRightChild
              (FenceInterval (FenceIndex average, FenceIndex right))
          minTuple :: (FenceIndex, Int) -> (FenceIndex, Int) -> (FenceIndex, Int)
          minTuple old@(_, heightOld) new@(_, heightNew) =
            if heightNew < heightOld then new else old
```

# 润色其余部分

一旦我们完成了这个，剩下的就很简单了。首先，我们将在开始时构建分段树，并将其作为参数传递给我们的函数。然后我们将插入新的最小值函数来代替旧的函数。我们将确保把树添加到每个递归调用中。

```
largestRectangle :: FenceValues -> FenceSolution
largestRectangle values = largestRectangleAtIndices values 
  (buildSegmentTree (unFenceValues values)
  (FenceInterval (FenceIndex 0, FenceIndex (length (unFenceValues values))))…
-- Notice the extra parameter
largestRectangleAtIndices :: FenceValues -> SegmentTreeNode -> FenceInterval -> FenceSolution
largestRectangleAtIndices
  values
  tree
…
      where
      …
      -- And down here add it to each call
      (minIndex, minValue) = minimumHeightIndexValue values tree interval
      leftCase = largestRectangleAtIndices values tree (FenceInterval (leftIndex, minIndex))
      rightCase = if minIndex + 1 == rightIndex
        then FenceSolution (maxBound :: Int)
        else largestRectangleAtIndices values tree (FenceInterval (minIndex + 1, rightIndex))
```

现在我们可以再次运行我们的基准了。这一次，我们将看到我们的代码在这两种大型情况下都运行得更快了！成功！

```
benchmarking fences tests/Size 100000 Test
time                 179.1 ms   (173.5 ms .. 185.9 ms)
                     0.999 R²   (0.998 R² .. 1.000 R²)
mean                 184.1 ms   (182.7 ms .. 186.1 ms)
std dev              2.218 ms   (1.197 ms .. 3.342 ms)
variance introduced by outliers: 14% (moderately inflated)benchmarking fences tests/Size 100000 Test (sorted)
time                 238.4 ms   (227.2 ms .. 265.1 ms)
                     0.998 R²   (0.989 R² .. 1.000 R²)
mean                 243.5 ms   (237.0 ms .. 251.8 ms)
std dev              8.691 ms   (2.681 ms .. 11.83 ms)
variance introduced by outliers: 16% (moderately inflated)
```

# 结论

所以在过去的两篇文章中，我们学到了很多。我们首先介绍了如何使用 Cabal/Stack 为我们的代码创建基准。当我们运行这些基准测试时，我们发现结果花费的时间比我们希望的要长。然后，我们使用概要分析来确定有问题的函数是什么。然后我们一头扎进一些数据结构知识。我们亲眼目睹了改变程序的底层数据结构如何提高我们的性能。我们还学习了数组，这在 Haskell 中被忽略了。然后，我们从头开始构建了一个细分树，并使用它的 API 来实现我们程序的改进。

在下周的[周一早间 Haskell 博客](https://www.mmhaskell.com/blog)上，我们将通过学习图形来继续探索数据结构！我们将看看函数图形库，看看它如何使解决某些问题变得容易！

这个问题涉及到递归的许多不同用法。如果你想成为一名更好的函数式程序员，你最好学习递归。如果你想更好地掌握这个基本概念，你应该看看我们免费的[递归练习册](https://www.mmhaskell.com/workbook)。它有两章有用的信息以及 10 个练习题！

如果您以前从未编写过 Haskell，但对本文中看到的可能性很感兴趣，那么您应该尝试一下！下载我们的[入门清单](https://www.mmhaskell.com/checklist)！它将引导您完成语言的安装。它还会为您提供一些开始 Haskell 教育的很酷的资源。

最后，请务必查看我们的[堆栈迷你课程](http://academy.mondaymorninghaskell/p/your-first-haskell-project)。一旦你掌握了 Stack 工具，你就可以像专业人士一样制作 Haskell 项目了！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！