# 具有类型类模式

> 原文：<https://medium.com/hackernoon/the-has-type-class-pattern-ca12adab70ae>

类型类模式很简单，但是令人惊讶的有用。我将介绍一些例子。

# 用例 1:收集所有图像

假设您正在开发一个游戏，并且在资产验证步骤中，您希望确保场景的图像存在。

我们需要遍历场景[描述](https://hackernoon.com/tagged/description)并收集所有图像文件路径。我们来做一个`Scene`型:

```
data Scene = Scene 
  { backgroundImage   :: Text
  , characters        :: [Character]   
  , bewilderedTourist :: Maybe Character
  , objects           :: [Either Rock WoodenCrate]  
  }data Character = Character
 { hat   :: Maybe DamageArray
 , head  :: DamageArray
 , torso :: DamageArray
 , legs  :: DamageArray
 , shoes :: Maybe DamageArray
 }data DamageArray = DamageArray
  { noDamage        :: Text
  , someDamage      :: Text
  , excessiveDamage :: Text
  }data Rock = Rock 
  { weight    :: Double
  , rockImage :: Text
  }data WoodenCrate = WoodenCrate
  { strength         :: Double
  , woodenCrateImage :: DamageArray  
  }
```

你明白了。有很多类型，有相当数量的嵌套。一个真实的场景可能有数百种类型和两位数级别的嵌套，但对于我们的目的来说，这是一个足够好的例子。

所以现在我们想写一个函数，`collectImages :: Scene -> Set Text`。

## 你知道没有

最直接的方法是只编写函数:

```
collectImages :: Scene -> Set Text
collectImages Scene {..} 
  =  singleton backgroundImage 
  <> mconcat (map collectCharacterImages characters)
  <> maybe mempty collectCharacterImages bewilderedTourist
  <> mconcat (map (either (singleton . collectRockImage) 
                          collectWoodenCrateImages)     
                  objects)collectCharacterImages :: Character -> Set Text
collectCharacterImages Character {..}  
  =  maybe mempty collectDamageArrayImages hat
  <> collectDamageArrayImages head
  <> collectDamageArrayImages torso
  <> collectDamageArrayImages legs
  <> maybe mempty collectDamageArrayImages shoescollectDamageArrayImages :: DamageArray -> Set Text
collectDamageArrayImages DamageArray {..} = fromList
  [ noDamage
  , someDamage
  , excessiveDamage
  ]collectRockImage :: Rock -> Text
collectRockImage Rock {..} = rockImagecollectWoodenCrateImages :: WoodenCrate -> Set Text
collectWoodenCrateImages WoodenCrate {..} = 
  collectDamageArrayImages woodenCrateImage
```

代码冗长且有点乏味，但编写或理解起来并不十分困难。我遵守纪律，用一致的方式给每样东西命名，这让我很容易记住。最棘手的部分是记住在我的多态容器上操作时要调用什么帮助函数(所有的`maybe`和`mconcat`之类的东西)。

## 世界上所有的财富

下面是用`Has`类型类模式的变体编写的相同代码:

```
class HasImages a where
  images :: a -> Set Textinstance HasImages a => HasImages [a] where
  images xs = foldr (\x accum -> images x <> accum) mempty xsinstance HasImages a => HasImages (Maybe a) where
  images x = maybe [] images xinstance (HasImages a, HasImages b) => HasImages (Either a b) where
  images x = either images images xinstance HasImages Scene where
  images Scene {..} 
    =  singleton backgroundImage 
    <> images characters
    <> images bewilderedTourist
    <> images objectsinstance HasImages Character where
  images Character {..} 
    =  images hat
    <> images head
    <> images torso
    <> images legs
    <> images shoesinstance HasImages DamageArray where
  images DamageArray {..} = fromList
    [ noDamage
    , someDamage
    , excessiveDamage
    ]instance HasImages Rock where
  images Rock {..} = singleton rockImageinstance HasImages WoodenCrate where
  images WoodenCrate {..} = images woodenCrateImage
```

好的，这是`Has`类型职业模式的最简单的变体。我们有一个`HasImages`类型的类，它需要一个函数`a -> Set Text`，由每个实例来实现。

`Has`例子和前面例子的第一个区别是我为我的多态容器`[]`、`Maybe`和`Either`实现了通用函数。这种方法的价值在于，我不必考虑调用什么函数来收集图像:它总是`images`。在前面的例子中，我每次都必须考虑如何收集图像，这需要将脑力更好地用在其他地方。

`Has`模式的好处是:

1.  我可以为每个多态容器编写一个单独的实例，它将适用于我需要的所有专门化。
2.  我不需要做太多的决定，这样我就可以腾出脑力专注于其他事情
3.  它为其他工程师提供了扩展的结构，而不必决定函数的名称和函数的类型(参见第一个例子中的`collectRockImage`不一致)。

缺点是:

1.  它使用类型类，这是一个比函数更复杂的概念。

2.实例声明比函数声明更嘈杂，需要更大的缩进。

# 用例 2:可组合阅读器

`Has`模式也可以用来创建一个可组合的阅读器单子。

假设您有一个包含以下内容的库 A:

```
foo :: Reader Int Bool
```

而库 B 具有:

```
bar :: Reader String Int
```

你希望能够写作

```
foobar = do 
  flag <- foo

  if flag then 
    bar
  else
    return 0
```

但是它不会进行类型检查，因为`foo`需要一个`Int`环境而`bar`需要一个`String`环境。

诀窍是定义助手`Has`类型类:

```
class HasFooEnv a where
  getFooEnv :: a -> Intclass HasBarEnv a where
  getBarEnv :: a -> String
```

然后我们修改类型签名以使用`MonadReader`:

```
foo :: (MonadReader e m, HasFooEnv e) => m Bool
```

我们将不得不修改对`ask`的调用来使用`asks getFooEnv`。我们对`bar`做一个类似的修改:

```
bar :: (MonadReader e m, HasBarEnv e) => m Int
```

和实例:

```
instance HasFooEnv (Int, String) where
  getFooEnv = fstinstance HasBarEnv (Int, String) where
  getBarEnv = snd
```

我们得到组合版本进行类型检查:

```
foobar :: Reader (Int, String) 
foobar = do 
  flag <- foo

  if flag then 
    bar
  else
    return 0
```

迈克尔·斯诺曼也在 ReaderT [的一篇文章中讨论了`Has`模式。](https://www.fpcomplete.com/blog/2017/06/readert-design-pattern)

# 用例 3:方便的参数传递

在数据库应用程序中，具有以下类型是很常见的:

```
newtype Key a = Key UUID
data Entity a = Entity 
  { entityKey   :: Key a
  , entityValue :: a
  }
```

此外，我们将编写类似如下的查询:

```
getFriends :: Key User -> [Entity User]
```

通过从一个`Entity User`中提取一个`Key User`来经常调用它。

```
getFriends (entityKey user)
```

您可以让 API 更容易使用:

```
class HasKey a k | a -> k where
  key :: a -> Key kinstance HasKey (Key a) a where
  key = idinstance HasKey (Entity a) a where
  key = entityKeygetFriends :: HasKey a User => a -> [Entity User]
```

现在可以传入一个`Entity User`或`Key User`。

```
getFriends user
```

这是一件小事，但是我以前的一个同事喜欢它，我自己也喜欢它，所以我把它包括在内。更难的错误消息是一个缺点。

# 用例 4:遍历

目前为止我们讨论的是`Has`的简单版本，只能得到东西。这仅仅是开始。回到我们的第一个例子，假设我们不仅仅是收集图像，我们还想遍历场景并使用图像的散列作为后缀来更新图像文件路径。

我们将利用`lens`包，我们的新`HasImages`类将看起来像:

```
class HasImages a where
  images :: Traversal' a Text
```

我们的实例看起来像:

```
instance HasImages a => HasImages [a] where
  images = traversed . imagesinstance HasImages a => HasImages (Maybe a) where
  images = traversed . imagesinstance (HasImages a, HasImages b) => HasImages (Either a b) where
  images f e = case e of
    Left  x -> Left  <$> traverseOf images f x
    Right x -> Right <$> traverseOf images f xinstance HasImages Scene where
  images f Scene {..} 
    =  Scene 
   <$> f backgroundImage
   <*> traverseOf images f characters
   <*> traverseOf images f bewilderedTourist
   <*> traverseOf images f objectsinstance HasImages Character where
  images f Character {..} 
     =  Character
    <$> traverseOf images f hat
    <*> traverseOf images f head
    <*> traverseOf images f torso
    <*> traverseOf images f legs
    <*> traverseOf images f shoesinstance HasImages DamageArray where
  images f DamageArray {..} 
    =  DamageArray
   <$> f noDamage
   <*> f someDamage
   <*> f excessiveDamageinstance HasImages Rock where
  images f Rock {..} 
    =  Rock weight 
   <$> f rockImageinstance HasImages WoodenCrate where
  images f WoodenCrate {..} 
    = WoodenCrate strength 
   <$> traverseOf images f woodenCrateImage
```

我们可以像这样应用我们的散列更新器:

```
hashFilePath :: Text -> IO Text
hashFilePath filePath = do 
  let pathStr = T.unpack filePath
  fileHash <- hashBytes <$> BSL.readFile pathStr
  return $ T.pack $ dropExtension pathStr 
         ++ "-" ++ fileHash <.> takeExtension pathStrhashSceneImages :: Scene -> IO Scene
hashSceneImages x = traverseOf images hashFilePath x
```

不仅如此，我们还获得了“免费”的`collectImages`(尽管性能会有所不同，这可能并不重要)。

```
collectImages :: Scene -> [Text]
collectImages x = fromList $ toListOf images x
```

# 用例 5:可组合状态

通过使用简单函数的`Lens`实例，我们可以获得可组合的状态单子，就像我们获得可组合的读取器单子一样:

```
class HasFooState a where
  fooState :: Lens' a Intclass HasBarState a where
  barState :: Lens' a String
```

然后我们修改类型签名以使用`MonadStates`:

```
foo :: (MonadState s m, HasFooState s) => m Bool
```

我们将不得不用`use fooState`交换对`get`的呼叫，用`modifying fooState`交换对`modify`的呼叫，并且`put`变成`assign fooState`。我们以类似的方式修改`bar`:

```
bar :: (MonadReader s m, HasBarState s) => m Int
```

和实例:

```
instance HasFooState (Int, String) where
  fooState = _1instance HasBarState (Int, String) where
  barState = _2
```

我们把合并后的版本进行类型检查。

```
foobar :: State (Int, String) 
foobar = do 
  flag <- foo

  if flag then 
    bar
  else
    return 0
```

# 用例 6:可扩展异常

基于`Prism` s 的`Has`类允许我们用`MonadError`扩展异常。

我们的班级将会是这样的:

```
class HasIdNotFound a where
  _IdNotFound :: Prism a UUID
```

然后我们把函数写成这样:

```
foo :: (HasIdNotFound e, MonadError e m) => m a
```

并且可以通过调用以下命令抛出我们的异常:

```
throwError $ review _IdNotFound theId
```

对于需要更少实例的更复杂的变体，请看这篇[帖子](/@jonathangfischoff/prisms-open-unions-and-extendible-exceptions-e71882bb9429)。

# 神奇的选择

如果你像我一样，你可能想知道是否有什么神奇的方法可以不用做任何工作就能写出`collectImages`和`hashSceneImages`。有！我们可以使用`[uniplate](https://hackage.haskell.org/package/uniplate-1.6.12/docs/Data-Generics-Uniplate-Data.html)`(或者另一个类似的库)。

我们需要启用`DeriveDataType`并为每种类型添加一个`deriving (Data)`。那么我们的`collectImages`就变成了:

```
import Data.Generics.Uniplate.DatacollectImages :: Scene -> Set Text
collectImages x = fromList (universeBi x)
```

而我们的`hashSceneImages`现在是:

```
hashSceneImages :: Scene -> IO Scene
hashSceneImages x = transformBiM hashFilePath x
```

这种方法的缺点是它不加选择地收集所有的`Text`值。这不一定是我们想要的(我们可以制造一个`newtype ImageFile = ImageFile Text`来使它更安全)。另一个缺点是它比自定义遍历类慢。

# 结论

类型类很简单，但是它们可以让你的代码保持良好的结构，并帮助你处理常见的任务。此外，你也许可以用`uniplate`YOLO 它。

回购与更完整的例子[此处](https://github.com/jfischoff/has-type-class-pattern)。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！