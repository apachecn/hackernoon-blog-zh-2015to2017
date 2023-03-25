# 各种形状和大小的数字

> 原文：<https://medium.com/hackernoon/numbers-of-every-shape-and-size-e277232d776b>

上周我们探索了 Haskell 中许多不同的字符串类型。但这不是我们似乎有大量相似类型的唯一情况。我们也可以在 Haskell 的**数字类型**中看到这一点。同样，我们也有这样的问题，我们经常想用稍微不同的方式来表示数字。但是 Haskell 的类型系统迫使我们为每种不同的情况使用不同的类型。这些类型之间的互操作可能非常痛苦。

这是 Haskell 的另一个特点，肯定会让初学者陷入困境。它甚至可以让他们说:“唉，这种语言太烂了，我要回到 Javascript”(在那里数字更容易)。但是有一些简单的规则你必须掌握，然后事情会变得更好。让我们从总结我们可以使用的不同数字类型开始。

# Int 类型

最熟悉的整数型是简单的`Int`型。这代表你的标准的机器可变的，有界的，有符号的整数。根据文件，它保证至少有从-2^29 到 2^29.的射程因此，如果您在 32 位机器上，那么您的`Int`可能有一组不同于 64 位机器上的界限。幸运的是，`Int`是`Bounded`类型类的成员。因此，在您的代码中，您总是可以查询最大和最小界限来检查溢出。

```
class Bounded a where
  minBound :: a
  maxBound :: a
```

现在，假设你不想被你能储存多大价值的机器所左右。有时候，你想知道你的 int 会占用多少内存。有几种不同的`Int`类型可以让你做到这一点。我们有`Int8`、`Int16`、`Int32`和`Int64`。它们允许你在数量上有更明确的界限，同时给你和`Int`一样的基本功能。显然`Int8`是 8 位，`Int16`是 16 位，以此类推。

现在，也有一些情况，你希望你的整数是无界的。在这种情况下，您需要使用`Integer`类型。这种类型对你的数量没有限制。它没有实现`Bounded` typeclass。自然，这是以性能损失为代价的。如果您的数字太大，无法放入内存中的单个寄存器，Haskell 将使用一个字节数组来表示该数字。对这个数字的运算会比较慢。硬件被设计成能以极快的速度对较小的值进行数学运算。在更大的数字上，你将无法获得这些加速。但是如果你需要更高的界限，那么你没有选择。

# 单词类型

一旦你理解了不同的`Int`类，接下来就是`Word`类型了。每种`Int`尺寸都有相应的`Word`型号。这些类型以同样的方式工作，除了它们是无符号的。因此，`Int8`从-128 到 127，而`Word8`可以包含从 0 到 255 的值。事实上，`Data.ByteString`有一个函数给你一个`Word8`值的列表，作为字节串的表示。

那么我们能从这些不同的积分类型中学到什么呢？我们如何选择使用什么类型？大多数情况下，`Int`可能没问题。根据域的不同，您可以限制需要使用的字节数。这就是不同大小的整数发挥作用的地方。如果你知道你的值将是正的而不是负的，那么一定要使用`Word`类型而不是`Int`类型。这将会给你更多的回旋余地。如果您正在处理可能超过 64 位界限的值，您别无选择，只能使用`Integer`。请注意，如果值这么大，您将面临性能损失。

# 整型类

你经常会遇到这样的情况，你想写一个关于数字的更通用的函数。你不知道你会和什么类型的人打交道。但是你知道它是这些整数类型中的一个。像一个优秀的 Haskell 开发人员一样，您希望您的代码尽可能多态。这就是`Integral` typeclass 的用途。它封装了一些不同的功能。

首先，它有助于不同积分类型之间的转换。它提供了一个`toInteger`函数，类型为:

```
toInteger :: Integral a => a -> Integer
```

这允许您使用无界的`Integer`类型作为整数类型之间的媒介。它允许您编写合理的多态代码。但是也有缺点。通过使用最小公分母，您可能会使您的程序性能更差。因此，对于性能关键型代码，您可能需要更系统地考虑应该使用哪种类型。

`Integral` typeclass 中的其他函数处理整数类型上的**除法。例如，有商和余数函数。这些允许你在不知道确切类型的时候进行整数除法。**

```
class Integral a where
  toInteger :: a -> Integer
  quot :: a -> a -> a 
  -- ^^ Integer division, e.g. 9 `quot` 2 = 4
  rem :: a -> a -> a
  -- ^^ Remainder, e.g. 9 `rem` 2 = 1
  div :: a -> a -> a
  mod :: a -> a -> a
```

值得注意的是，`div`和`mod`函数类似于`quot`和`rem`，但是向负无穷大而不是 0 舍入。不得不穿过这些圈圈可能会有点困难。尤其是当你的 javascript 朋友可以一直写`5 / 2`而不受惩罚的时候。但这是强类型系统的代价。

# 浮点数

当然，我们也有一些不同的类型来表示浮点数。Haskell 有两种主要的浮点类型:`Float`和`Double`。`Float`类型是单精度浮点数。当然，`Double`是双精度。它们代表相同的基本概念，但是`Double`允许更大范围的值和更精确的**。同时占用的内存是 a `Float`的两倍。使用以下函数可以很容易地在这两种类型之间进行转换:**

```
float2Double :: Float -> Double
double2Float :: Double -> Float
```

有一个封装了这些类型的 typeclass(以及几个更模糊的版本)。这是`Floating`类型类。它允许在这些类型上执行许多不同的操作。很多都是数学函数。

例如，其中一个功能就是`pi`。因此，如果您的函数采用`Floating` typeclass 中的任何类型，您仍然可以获得 pi 的可靠值。`Floating`也融合了其他数学概念，比如平方根、指数、三角函数等等。

# 其他数值类型类

还有一些其他的数字类型类。它们封装了浮点数和整数的行为。例如，我们有一个`Real` typeclass，它允许我们将任何东西转换成一个`Rational`数字。还有`Fractional`类型类。它允许我们对分数进行某些自然的运算。这些包括计算**倒数和执行真(非整数)除法**。然后我们可以将这两个类混合在一起得到`RealFrac`。这个 typeclass 允许我们将一个数表示为一个真分数(因此是两个整数的元组)。它还有其他几个有用的功能，如`ceiling`、`round`、`truncate`等等。

# 皈依狂热

我们已经讨论了相似类型之间的一些转换。但是很难跟踪所有不同的值之间的转换方式。要获得更详尽的列表，请查看[Haskell 简介](https://www.haskell.org/tutorial/numbers.html)。除了我们已经讨论过的，两种类型的转换最为突出。

第一，有`fromIntegral`。这允许您从任何整数类型转换为任何数字类型。当你**需要一个浮点数**但**有一些整数类型**时，它应该是你的首选。第二，有一个从浮点数到整数类型的过程。你通常会使用`round`、`floor`和`ceiling`中的一个，这取决于你想要的强制。最后，记住`toInteger`函数。这通常是在整型之间转换的答案。

```
fromIntegral :: (Integral a, Num b) => a -> b
round :: (Fractional a, Integral b) => a -> b
floor :: (Fractional a, Integral b) => a -> b
ceiling :: (Fractional a, Integral b) => a -> b
toInteger :: (Integral a) => a -> Integer
```

# 科学的

如果您从事任何级别的 web 编程，您可能会使用`Data.Aeson`库在 JSON 中编码。我们将在后面的文章中更深入地介绍这个库(它非常有用)。但是它使用了`Scientific`类型，用“科学”符号表示数字。在这种格式中，你有一个基数乘以 10 的某次方。您不会经常遇到这种类型，但是知道如何构造它是很有用的。特别是，您可能经常希望获得简单的整数或浮点值，并在它们之间来回转换。以下是一些代码示例:

```
-- Creates 70000
createScientificNum :: Scientific
createScientificNum = scientific 7 4-- Conversion to float (i.e. 70000.0)
convertToFloat :: Double
convertToFloat = toBoundedRealFloat createScientificNum-- Convert to integer, might fail if the scientific is not an integer
convertToInt :: Maybe Integer
convertToInt = toBoundedInteger createScientificNum
```

# Num Typeclass

所以在不同的浮点和整型类型类之上，我们还有`Num`类型类。这使他们都聚集在一个屋檐下。这个 typeclass 的必需元素是基本的数学运算符。

```
class Num a where
  (+), (*), (-) :: a -> a -> a
  negate :: a -> a
  abs :: a -> a
  signum :: a -> a
  fromInteger :: Integer -> a
```

这个类也有点类似于`IsString`类型类。`IsString`类让我们用一个字符串来表示代码中的不同类型。同样地，`Num` typeclass 允许我们使用数字文字来表示我们的值。我们甚至不需要它的编译器扩展！当您在一个数字周围创建“新类型”时，这尤其有用。不必总是为它编写构造函数，这很好。

```
newtype MyNumberType = MyNumberType Intinstance Num MyNumberType where
  -- Implement math functions using underlying int value
  ...myNumWithLiteral :: MyNumberType
myNumWithLiteral = 5
```

# 结论

在 Haskell 中，数字和字符串类型一样，非常容易混淆。很难让它们保持直线。当您试图在不同类型之间进行互操作时，这尤其困难。感觉上，既然数字类型如此相似，使用它们应该很容易。这一次，类型系统似乎妨碍了简单性。

幸运的是，多态性允许我们用许多不同的方法来解决这个问题。我们几乎总能让我们的函数适用于许多不同的数字类型。我们经常需要转换我们的类型，以使事情互操作。但是通常有一些很好的函数可以让我们轻松地做到这一点。

如果你以前从未用 Haskell 编程，并且想尝试一下，你应该看看我们的[入门清单](https://www.mmhaskell.com/checklist)。它将引导您在计算机上安装 Haskell。它还会给你指出一些帮助你学习语言的工具。

如果你已经做过一点实验，并且想要一些额外的练习，你应该下载我们的[递归练习册](https://www.mmhaskell.com/workbook)。它包含了两章的材料以及 10 道练习题！

最重要的是，请务必访问[周一早间 Haskell](https://www.mmhaskell.com/) 博客，获取更多精彩的 Haskell 内容，每周一都会有一篇新文章！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！