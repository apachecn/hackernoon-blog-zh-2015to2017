# 如何读写(用单子！)

> 原文：<https://medium.com/hackernoon/how-to-read-and-write-with-monads-7b24495754bc>

本文原载于 2017 年 2 月 20 日的[周一早间哈斯克尔博客](https://mmhaskell.com/blog/2017/2/20/how-to-read-and-write-with-monads)。查看博客获取更多 Haskell 内容！

上周我们讨论了单子是什么。这不是只有拥有范畴理论神秘知识的巫师才能理解的可怕事情。它只是一个**类型的类**，带有几个描述特定上下文的函数。这些函数，当正确使用时，可以极大地扩展我们的功能，同时保持代码的纯粹功能性。

我们还没有讨论这些函数需要遵循的所有“法则”。但是如果我们探索足够多的例子，我们会对应该发生什么有一个直观的理解。上次我们看到了一些简单的例子，有`Maybe`、`Either`和`IO`单子。在本文中，我们将看看`Reader`和`Writer`单子。

# 全局变量(或缺少全局变量)

在 Haskell 中，我们的代码通常是“纯”的，这意味着函数只能与传递给它们的参数进行交互。这实际上意味着我们不能有全局变量。我们可以有全局表达式，但这些表达式在编译时是固定的。如果用户行为可能改变它们，我们必须将它们包装在`IO`单子中，这意味着它们不能在纯代码中使用。

考虑这个例子，我们可能想要一个包含不同参数的`Environment`作为全局变量。然而，我们可能不得不从一个配置文件或命令行界面加载这些，这需要`IO` monad。

```
main :: IO ()
main = do
  env <- loadEnv
  let str = func1 env
  print strdata Environment = Environment
  { param1 :: String
  , param2 :: String
  , param3 :: String }loadEnv :: IO Environment
loadEnv = …func1 :: Environment -> String
func1 env = “Result: “ ++ (show (func2 env))func2 :: Environment -> Int
func2 env = 2 + floor (func3 env)func3 :: Environment -> Float
func3 env = … -- Some calculation based on the environment
```

实际使用环境的唯一函数**是`func3`。然而`func3`是一个不纯的函数。这意味着它不能直接调用`loadEnv`，一个不纯的函数。这意味着环境必须作为变量传递给其他函数，这样它们才能最终将它传递给`func3`。在有全局变量的语言中，我们可以将`env`保存为`main`中的全局值。然后`func3`可以直接访问它。就不需要将它作为`func1`和`func2`的参数。在较大的程序中，这些“传递”变量会导致很多令人头疼的问题。**

# 读者解决方案

阅读器 monad 解决了这个问题。它有效地创建了一个指定类型的全局只读值。monad 中的所有函数都可以“读取”该类型。让我们看看`Reader`单子如何改变我们代码的形状。我们的函数**不再需要**的`Environment`作为**的显式参数**，因为它们可以通过单子访问它。

```
main :: IO ()
main = do
  env <- loadEnv
  let str = runReader func1 env
  print strdata Environment = Environment
  { param1 :: String
  , param2 :: String
  , param3 :: String }loadEnv :: IO Environment
loadEnv = …func1 :: Reader Environment String
func1 = do
  res <- func2
  return (“Result: “ ++ (show res))func2 :: Reader Environment Int
func2 = do
  env <- ask
  let res3 = func3 env
  return (2 + (floor res3))func3 :: Environment -> Float
...
```

`ask`功能**打开环境**以便我们可以使用它。单子的绑定动作允许我们将不同的`Reader`动作粘在一起。为了从纯代码中调用阅读器动作，我们需要做的就是调用`runReader`函数并提供环境作为参数。动作中的所有函数都能够像对待一个全局变量一样对待它。

看起来我们似乎没有完成多少，但是我们的代码现在更加直观了。我们保持现状。将它描述为从一个`Environment`到一个值的函数是有意义的。然而，我们的其他两个函数不再将环境作为显式参数。它们只是存在于**上下文**中，其中**环境是一个全局变量**。

# 累积值

现在，为了激励作家莫纳德，我们来谈谈积累问题。假设我们有几个不同的函数。每一个都将执行一些我们指定了任意“成本”的字符串操作。我们希望跟踪运行完整计算的“代价”有多高。我们可以通过使用**累加器参数**来跟踪到目前为止我们看到的成本。然后我们继续传递累积的值。

```
-- Calls func2 if even length, func3 and func4 if odd
func1 :: String -> (Int, String)
func1 input = if length input `mod` 2 == 0
  then func2 (0, input)
  else (i1 + i2, str1 ++ str2)
    where
      (i1, str1) = func3 (0, tail input)
      (i2, str2) = func4 (0, take 1 input)-- Calls func4 on truncated version
func2 :: (Int, String) -> (Int, String)
func2 (prev, input) = if (length input) > 10
  then func4 (prev + 1, take 9 input)
  else (10, input)-- Calls func2 on expanded version if a multiple of 3
func3 :: (Int, String) -> (Int, String)
func3 (prev, input) = if (length input) `mod` 3 == 0
  then (prev + f2resI + 3, f2resStr)
  else (prev + 1, tail input)
  where
    (f2resI, f2resStr) = func2 (prev, input ++ "ab")func4 :: (Int, String) -> (Int, String)
func4 (prev, input) = if (length input) < 10
  then (prev + length input, input ++ input)
  else (prev + 5, take 5 input)
```

然而，`Int`并不是我们可以积累的唯一类型的值。相反，我们可以累积一个字符串列表，作为日志消息打印出来，这样我们就知道运行了什么计算。这种行为有一个**概括:`Monoid`类型类。**

# 幺半群类型类

在这个例子中，`Int`是幺半群的一个简单例子。让我们看看幺半群类型类的定义:

```
class Monoid a where
  mempty :: a
  mappend :: a -> a -> a
```

这实际上是一个**累积类**。它定义了两个功能。`mempty`函数是我们幺半群的初始值。然后用`mappend`，我们可以把这种类型的两个值合并成一个结果。如何为`Int`创建一个幺半群实例非常简单:

```
instance Monoid Int where
  memty = 0
  mappend a b = a + b
```

我们的累加器从 0 开始，通过相加来组合值。

# 使用 Writer 跟踪累加器

单子由某个单子类型参数化。它的主要工作是记录这种类型的累计值。因此，它的运营生活在拥有一个全局值的环境中，他们可以以这种特定的方式进行修改。我们可以修改上面的代码示例，使用如下的`Writer`单子:

```
func1 :: String -> (String, Int)
func1 input = if length input `mod` 2 == 0
  then runWriter (func2 input)
  else runWriter $ do
    str1 <- func3 input
    str2 <- func4 (take 1 input)
    return (str1 ++ str2)func2 :: String -> Writer Int String
func2 input = if (length input) > 10
  then do
    tell 1
    func4 (take 9 input)
  else do
    tell 10
    return inputfunc3 :: String -> Writer Int String
func3 input = if (length input) `mod` 3 == 0
  then do
    tell 3
    func2 (input ++ “ab”)
  else do
    tell 1
    return $ tail inputfunc4 :: String -> Writer Int String
func4 input = if (length input) < 10
  then do
    tell (length input)
    return (input ++ input)
  else do
    tell 5
    return (take 5 input)
```

请注意，我们不再需要实际上**明确跟踪累加器的**。它现在被`Writer`单子包裹着。我们可以通过调用“tell”在我们的任何函数中增加它。现在我们的代码更简单，类型更干净。

# 结论

读者和作者单子都提供**纯函数方式**来处理**常见副作用**。读取器允许你跟踪一个共享的全局状态。它允许您避免将该状态作为显式参数传递给并不真正使用它的函数。**作者** monad 允许你用一个幺半群来跟踪一个全局累计值。如果你想知道如何将这些想法结合在一起，那就去周一早上哈斯克尔的博客看看那里关于[州单子](https://www.mmhaskell.com/2017/2/27/the-monadic-state-of-mind)的最新文章吧！

希望这篇文章有助于让您相信单子(以及 Haskell)并不那么可怕！如果这启发了你拿起 Haskell 并开始写一些代码，检查一下我们免费的清单[来获得陈述！](https://www.mmhaskell.com/checklist)

还没有为单子做好准备，但想尝试一些不同的 Haskell 技能？查看我们的[递归工作簿](https://www.mmhaskell.com/workbook)。它包括 2 章关于递归和高阶函数的内容，以及 10 个测试练习题。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)