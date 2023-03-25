# 关于类型类实例选择

> 原文：<https://medium.com/hackernoon/typeclass-instance-selection-fea1068920e6>

在我能够创建类型类来定期解决问题之前，我的 Haskell 职业生涯已经进行了很长时间。

回想起来，我并没有很好地理解实例选择是如何工作的。我不知道什么时候我会走上一条会导致重叠实例的道路，或者什么时候我是安全的。

# 简单重叠

我会经常看到 Haskellers 像我以前一样写代码:

```
class Combine a where
 combine :: a -> a -> ainstance Combine String where
 combine = (++)instance Num a => Combine a where
 combine = (+)
```

并且在编译失败时感到困惑。

那么为什么下面的代码会重叠呢？

`String`不是`Num`的实例，所以看起来编译器可以将这些实例视为非重叠的。

原来类型检查被分成几个步骤。首先进行实例选择，然后类型检查器确保满足像`Num a`这样的约束。

为了判断两个实例是否重叠，必须忽略实例声明的上下文部分。

让我们把这两个实例排列起来，看看它们是否重叠。

```
Combine String 
Combine a
```

他们也是吗？我还没有真正定义实例重叠的含义。

实例选择就像一种模式匹配。每个实例都类似于执行匹配的不同等式，匹配使用类型构造函数和类型变量，而不是匹配数据构造函数。

# 模式匹配审查

这是一个带有一些正常模式匹配的函数。

```
asInt True = 1
asInt a    = 0
```

匹配有重叠吗？是的。第二种模式是通配符，匹配所有内容。这里没有什么不明确的地方，因为模式匹配能够使用匹配的顺序来确定如何处理重叠。

如果你改变顺序，编译器会抱怨，因为更具体的匹配永远不会被命中。

```
asInt a    = 0
asInt True = 1
```

实例选择的工作方式类似于模式匹配，只是在类型级别上不同。但是，没有办法指定顺序，因此重叠会无条件地导致编译器错误。

这导致了使用类型类的启发。如果你曾经写了一个像`instance Combine a`这样的实例，你是在说类型类只能有一个实例。这回避了为什么要使用类型类的问题。答案很可能是“不要使用类型类。”

# 一切正常，但你还是失败了

让我们继续使用我们错误的实例来看看实例选择是如何成功的，但是编译会失败。

让我们写出实例签名并删除重叠的实例。

```
class Combine a where
 combine :: a -> a -> ainstance Num a => Combine a where
 combine :: Num a => a -> a -> a
 combine = (+)
```

我们现在可以使用`combine`

```
main = putStrLn $ combine “hello “ “world”
```

在幕后，编译器基于我们的实例声明[和](https://hackernoon.com/tagged/declaration)制作了一个`combine`版本，我们称之为`combine_num`(关于编译器做什么的更详细的解释，请看这篇[帖子](https://www.schoolofhaskell.com/user/jfischoff/instances-and-dictionaries))。

编译器在`main`中发现了`combine`的用法，并且必须满足约束`Combine a`。它推断在这种情况下`a`是类型`String`。它现在必须满足专门的约束`Combine String`。编译器查看它的实例，试图找到匹配的东西。

编译器没有`Combine String`，但是它有`Combine a`。`a`将匹配`String`，因为它是匹配所有内容的通配符。

编译器在`combine_num`中进行替换。

```
main = putStrLn $ combine_num “hello “ “world”
```

`combine_num`带来了约束`Num a`，这里专门针对`Num String`。这个约束导致再次搜索`Num String`的实例，但是这次没有匹配，编译失败。

使用类型类方法会添加编译器必须解决的类约束。解决约束要求找到合适的实例，这可能导致编译器不得不解决更多的约束。为了编译成功，必须满足所有的约束，但是它们不能同时被解决。

# 更棘手的重叠

假设你想写一个类型类来计算一个函数有多少个参数:

```
class ArgCount a where
 argCount :: a -> Int

instance ArgCount (a -> b) where
 argCount _ = 1

instance ArgCount (x -> y -> z) where
 argCount _ = 2
```

让我们像以前一样排列我们的声明:

```
ArgCount (a -> b)
ArgCount (x -> y -> z)
```

它们重叠吗？确实如此(否则我不会问这个问题:p)。加括号会让原因更清楚。

```
ArgCount (a -> b)
ArgCount (x -> (y -> z))
```

第一个实例中的`b` 可以是任何东西，甚至是`y -> z`,所以它将匹配第二个实例，我们确实有重叠。

如果您仍然对这个例子感到困惑，这可能是因为中缀符号。与构造函数的模式匹配通常采用前缀表示法(但这不是必须的)。以下是以前缀形式写出的相同示例:

```
ArgCount (-> a b       )
ArgCount (-> x (y -> z))
```

让我们试着用递归来解决这个问题。

```
-- I upgraded to using proxies so I would not have to use undefined
class ArgCount a where
 argCount :: p a -> Int

instance ArgCount b => ArgCount (a -> b) where
 argCount _ = 1 + argCount (Proxy :: Proxy b)

instance ArgCount (m a) where
 argCount _ = 0
```

这个想法是，我将能够计算最终结果类似于`State Int`或`IO a`的一元函数。不幸的是，这也会重叠。

```
ArgCount (m a)
ArgCount (a -> b)
```

同样，当以前缀形式书写时，这一点更清楚。

```
ArgCount (m a)
ArgCount ((-> a) b)
```

我们可以看到`m`可能是`-> a`，所以我们有重叠。

下面的代码将计算`IO`函数的参数个数。

```
class ArgCount a where
 argCount :: p a -> Int

instance ArgCount b => ArgCount (a -> b) where
 argCount _ = 1 + argCount (Proxy :: Proxy b)

instance ArgCount (IO a) where
 argCount _ = 0
```

`[Printf](http://hackage.haskell.org/package/base-4.9.1.0/docs/Text-Printf.html#t:PrintfType)`也采用了类似的模式。

所以有几件事要注意。

一个实例的多态越少，重叠的可能性就越小。有时您可能认为您的实例是相对特定的(`a -> b`仅用于一个参数的函数)，但是由于可能的替换，它实际上是更通用的(`a -> b`实际上是所有函数的实例)。

# 当更多的多态性意味着更好的推断时

让我们完成最后一个实例选择示例:获得更好的类型推断的技巧。该代码取自`mono-traversable` `[Data.Sequences](https://hackage.haskell.org/package/mono-traversable-1.0.2/docs/src/Data-Sequences.html#line-1488)`但也存在于`[Printf](http://hackage.haskell.org/package/base-4.9.1.0/docs/Text-Printf.html#t:PrintfType)`中

```
instance (c ~ Char) => Textual [c] where
 words = List.words
```

如果我们忽略上下文，我们会看到这是所有列表类型的`Textual`的一个实例，与元素类型无关。然而，实际上只有当列表的类型是`[Char]`或`String`时，代码才会被编译。

在有些情况下，类型检查器可能能够推断出它需要一个`[c]`的实例，并且在替换了`words`之后，在稍后的某个阶段确定该列表确实是一个`String`。如果我们只为`String,` 编写实例，那么在没有类型注释的情况下，实例选择可能会失败。

虽然这不是典型的情况，但是如果你只需要一个多态类型的专用实例，这是一个比使用`FlexibleInstance` …更好的方法，至少从类型推理的角度来看是这样的。

# 打字课太棒了

许多 Haskellers 对成功使用类型类的失败尝试有着不好的记忆。我希望通过揭示类型类实例选择是如何工作的，我们可以避免不必要的痛苦。

我只讲述了简单的普通类型类，没有任何特定的类型类扩展。人们很容易认为重叠问题可以通过启用`OverlappingInstances`(以及更现代的`Overlapping`和`Overlaps`杂注)来解决，但这绝不是灵丹妙药。介绍有用的类型类扩展能完成什么至少是一篇独立的博客文章。

在我的下一篇博文中，我将讨论一些简单的编写类型类的模式，你可以从中获得乐趣和收益。

PS。感谢 Sukant Hajra、Alan Zimmerman 和 Sudhir Kumar 的早期审查和初步[反馈](https://hackernoon.com/tagged/feedback)。

> [黑客中午](http://bit.ly/Hackernoon)是黑客们下午的开始。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！