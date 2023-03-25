# 读者单子——第一部分

> 原文：<https://medium.com/hackernoon/the-reader-monad-part-1-1e4d947983a8>

这篇文章将涵盖[基础](https://hackernoon.com/tagged/foundations)。这将主要是一个练习，学习如何专门化类型、简化替换并提出唯一合理的实现。

# 动机

`Reader` monad，或者更一般地说是`MonadReader`接口，解决了将同一个配置线程化到多个函数的问题。

```
-- Imagine this is a directory
type Config = FilePathload :: Config -> String -> IO String
load config x = readFile (config ++ x)loadRevision :: Config -> Int -> IO String
loadRevision config x = load config ("history" ++ show x ++ ".txt")loadAll :: Config -> Int -> String -> IO (String, String)
loadAll config x y = do
    a <- load config y
    b <- loadRevision config x
    return (a, b)
```

如果你看一下`loadAll`，你会发现`config`没有被使用，而是被线程化到了子函数中。这是样板文件的常见来源，读者 monad 试图改善它。

因此，我们可以使用`MonadReader`重写这个函数，而不是将`config`线程化到每个函数，配置将被隐式传递。为了检索配置，我们调用`ask`:

```
-- Imagine this is a directory
type Config = FilePathload :: (MonadReader Config m, MonadIO m) => String -> m String
load x = do
    config <- ask
    liftIO $ readFile (config ++ x)loadRevision :: (MonadReader Config m, MonadIO m) => Int -> m String
loadRevision x = load ("history" ++ show x ++ ".txt")loadAll :: (MonadReader Config m, MonadIO m) => Int -> String -> m (String, String)
loadAll x y = do
    a <- load y
    b <- loadRevision x
    return (a, b)
```

如果你看看中间函数`loadRevision`和`loadAll`，我们不再需要接受和传递配置。然而“叶子”功能`load`变得更加复杂。我们稍后将扩展这个例子，使它可以跨具体配置重用，并将其与备选方案进行比较；但首先是一些基础知识。

# ((->) e)，Reader，ReaderT 和 MonadReader

当 Haskellers 提到“读者单子”时，他们可能指的是四个相关事物中的一个:

1.  具有相同第一个参数的函数的`Monad`实例，写为`((->) e)`(我认为是`(e ->)`)有点令人费解。
2.  `type Reader = ReaderT Identity`
3.  `ReaderT`型
4.  任何实现`MonadReader`类型类的东西。

理解所有这四个概念是值得的。

# 第一个参数相同的函数的单子是做什么的？

记住一个`Monad`也是一个`Functor`和一个`Applicative`。为了理解`((->) e)`的单子，我们将在把`((->) e)`代入类型签名后，通过查看类型来猜测`Functor`、`Applicative`和`Monad`实例的实现。

## 仿函数实例

首先是`[Functor](https://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#t:Functor)`实例。让我们写出`[fmap](https://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#v:fmap)`的类型。

```
Class Functor f where
  fmap :: (a -> b) -> f a -> f b
```

从这个类型签名看不出`((->) e)`的`Functor`实例会做什么。

理解`Functor`的实现应该是什么的一个简单方法是查看`base`中的实现。另一种方法是通过写出专门化的实例签名来推断它。这是一个有些乏味的过程，但是对于实现实例和理解它们必须如何工作来说，这是一个很好的实践。

这个过程从替换类型类中引入的类型变量开始，在这个例子中是`f`。

所以我们用`f = ((->) e)`代替:

```
fmap :: (a -> b) -> (((->) e) a) -> (((->) e) b)
```

然后我们简化

```
fmap :: (a -> b) -> (((->) e) a) -> (((->) e) b)
fmap :: (a -> b) -> ((->) e a) -> ((->) e b)
fmap :: (a -> b) -> (e -> a) -> (e -> b)
fmap :: (a -> b) -> (e -> a) -> e -> b
```

我将用下面的替换来重新标记变量，`e = a`、`a = b`和`b = c`(因为我已经知道要寻找什么；)).

```
fmap :: (b -> c) -> (a -> b) -> a -> c
```

现在我们可以看到`((->) e)`的`fmap`被合成`[.](https://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#v:.)`

```
fmap :: (b -> c) -> (a -> b) -> a -> c
fmap f g x = f (g x)
```

对于该类型签名，没有其他非邪恶的实现。

这就引出了一个有趣的恶作剧，通过把`fmap . fmap`写成`fmap fmap fmap`来引诱你的同事

```
> (fmap fmap fmap) (+1) [Just 1, Just 2, Nothing]
[Just 2, Just 3, Nothing]
```

## 应用实例

首先让我们写出`[pure](https://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#v:pure)`。

```
pure :: a -> f a
```

替换`f = ((->) e)`

```
pure :: a -> (((->) e) a)
```

简化

```
pure :: a -> e -> a
```

所以我们最终得到一个函数，它接受一个`a`和一些随机的其他参数`e`，并返回一个`a`。这必须适用于所有的`e`和`a`，并且没有办法组合未知的类型。因此，该函数唯一能做的就是返回给它的`a`。因此是`[const](https://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#v:const)`:

```
pure :: a -> e -> a
pure x _ = x
```

接下来我们有`[<*>](https://hackage.haskell.org/package/base-4.9.1.0/docs/Prelude.html#v:-60--42--62-)`或`[ap](https://hackage.haskell.org/package/base-4.9.1.0/docs/Control-Monad.html#v:ap)`。

```
(<*>) :: f (a -> b) -> f a -> f b
```

替换`f = ((->) e)`

```
(<*>) :: (((->) e) (a -> b)) -> (((->) e) a) -> (((->) e) b)
```

简化

```
(<*>) :: ((e -> (a -> b)) -> (e -> a) -> (e -> b)
(<*>) :: (e -> a -> b) -> (e -> a) -> (e -> b)
(<*>) :: (e -> a -> b) -> (e -> a) -> e -> b
```

因此,`<*>`接受两个函数，两个函数都以`e`作为第一个参数，并将它们链接在一起，形成一个新的函数，它接受一个`e`并给出链接的输出。

```
(<*>) :: (e -> a -> b) -> (e -> a) -> e -> b
f <*> g = \e -> f e (g e)
```

## 单子实例

我们已经讲过`[return](https://hackage.haskell.org/package/base-4.9.1.0/docs/Control-Monad.html#v:return)`:只是`pure`，只是`const`。

首先，绑定的类型:

```
(>>=) :: m a -> (a -> m b) -> m b
```

替换`m = ((->) e)`

```
(>>=) :: (((->) e) a) -> (a -> (((->) e) b)) -> (((->) e) b)
```

简化

```
(>>=) :: (e -> a) -> (a -> (e -> b)) -> (e -> b)
(>>=) :: (e -> a) -> (a -> e -> b) -> e -> b
```

Bind 基本上是一个翻转的`<*>`

```
(>>=) :: (e -> a) -> (a -> e -> b) -> e -> b
g >>= f = flip f <*> g
```

`[join](https://hackage.haskell.org/package/base-4.9.1.0/docs/Control-Monad.html#v:join)`比较有意思。`join`将两层单子展平为一层。

```
join :: Monad m => m (m a) -> m a
```

让我们替换`m = ((->) e)`

```
join :: (((->) e) (((->) e) a))) -> (((->) e) a)
```

简化

```
join :: (((->) e) ((->) e a))) -> ((->) e a)
join :: ((->) e) (e -> a)) -> (e -> a)
join :: (e -> (e -> a)) -> e -> a
join :: (e -> e -> a) -> e -> a
```

对于这种类型的签名，只有一个真正的非邪恶的实现，它等价于:

```
join :: (e -> e -> a) -> e -> a
join f x = f x x
```

`join`我们免费获得，但很高兴看到它是如何手工实现的。它有时用于创建第一个和第二个值相同的元组。

```
> join (,) 1
(1, 1)
```

# 什么是 Reader？

你可以把`[Reader](https://hackage.haskell.org/package/transformers-0.5.4.0/docs/Control-Monad-Trans-Reader.html#t:Reader)`想象成`(e -> a)`周围的`newtype`

```
newtype Reader e a = Reader { runReader :: e -> a }
```

然而，如今它被定义为`[ReaderT](https://hackage.haskell.org/package/transformers-0.5.4.0/docs/Control-Monad-Trans-Reader.html#t:ReaderT)`的专门版本。

```
type Reader = ReaderT Identity
```

实际上，它就像`Functor`、`Applicative`和`Monad`实例、`((->) e)`一样工作。如果`((->) e)`就够了，那就真的没有理由用了。

# 莫纳瑞德

`[MonadReader](https://hackage.haskell.org/package/mtl-2.2.1/docs/Control-Monad-Reader-Class.html#t:MonadReader)`是阅读器单子的通用接口。类型类本质上如下所示:

```
class Monad m => MonadReader r m | m -> r where
  ask :: m r
  local :: (r -> r) -> m a -> m a
```

让我们通过替换`m = ((->) e)`和`r = e`来看看`((->) e)`的实现必须是什么:

```
instance MonadReader e ((->) e) where
  ask :: e -> e
  ask = ?

  local :: (e -> e) -> (e -> a) -> e -> a
  local = ?
```

`[ask](https://hackage.haskell.org/package/mtl-2.2.1/docs/Control-Monad-Reader-Class.html#v:ask)`只能真正成为一件事:

```
ask :: e -> e
ask = id
```

`[local](https://hackage.haskell.org/package/mtl-2.2.1/docs/Control-Monad-Reader-Class.html#v:local)`有点棘手。并不是完全由类型决定的。文档说它接受一个修改环境的函数`e -> e`和一个使用修改后的环境的函数`e -> a`。

我们开始吧:

```
local :: (e -> e) -> (e -> a) -> e -> a
local f action = action . f
```

# 里德特

`[ReaderT](https://hackage.haskell.org/package/transformers-0.5.4.0/docs/Control-Monad-Trans-Reader.html#t:ReaderT)`是`Reader`的变形金刚版本。它允许你用另一个`Monad`添加“阅读器”的“第一参数线程化”能力。常见的选择是`ReaderT e IO`。我们在文章开头的例子可以用`ReaderT e IO`代替`MonadReader`来重写，但是直接指定转换器栈并没有什么好处。使用阅读器 monad 接口`MonadReader`编写函数更加灵活。

直接使用`ReaderT`的一个好处是我们可以利用`local`的一个更有表现力的版本，主要是`[withReaderT](https://hackage.haskell.org/package/mtl-2.2.1/docs/Control-Monad-Reader.html#v:withReaderT)`，它有以下类型:

```
withReaderT :: (r' -> r) -> ReaderT r m a -> ReaderT r' m a
```

与`local`不同的是`withReaderT`可以将环境的类型从`r`更改为`r'`。

# 接下来

目前就这些。在以后的帖子中，我将讨论一些增强功能，并将 Reader Monad 与一些替代方案进行比较。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！