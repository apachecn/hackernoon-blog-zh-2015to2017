# 类型类模式和反模式

> 原文：<https://medium.com/hackernoon/type-class-patterns-and-anti-patterns-efd045c5af66>

在之前的[帖子](https://hackernoon.com/typeclass-instance-selection-fea1068920e6)中，我写过类型类实例选择是如何工作的。为了帮助理解好的类型类设计，我想介绍一个类型类模式和一个相关的类型类反模式。

# 到/自类型类

第一种模式是`To`和`From`类型的类。这种模式出现在许多包中:`aeson`、`[cassava](https://hackage.haskell.org/package/cassava-0.4.5.1/docs/Data-Csv.html)`、`[postgresql-simple](https://hackage.haskell.org/package/postgresql-simple)`等等。

一般来说，`To`类看起来像:

```
class ToBlah a where
  toBlah :: a -> Blah
```

尽管它也可以具有以下形式:

```
class ToBlah a where
  toBlah :: a -> BlahBuilder
```

这里的`BlahBuilder`是`Blah`更有效的可组合中间版本(当通过`Builder` s 转换为`ByteString` s 时就是这种情况)。

`From`类型的类看起来像:

```
class FromBlah a where
  parseBlah :: Parser a
```

`Parser`类型通常公开一个带有某种状态类型的`Monad`接口，以及一种简化解析以产生错误消息的方法。往往会有一些 API 函数使用解析器(这是`aeson`中的`[decode](https://hackage.haskell.org/package/aeson-1.2.0.0/docs/Data-Aeson.html#v:decode)`函数和`postgresql-simple`中的`[query](https://hackage.haskell.org/package/postgresql-simple-0.5.3.0/docs/Database-PostgreSQL-Simple.html#g:15)`函数)，它们只被直接用来组成其他解析器。

有时`To`和`From`类都有；其他时候，如在`[Yesod.Core.Content.ToContent](https://hackage.haskell.org/package/yesod-core-1.4.33/docs/Yesod-Core-Content.html#t:ToContent)`的情况下，只需要一个方向。

一般来说，这种模式用于在用户定义的类型和围绕其构建 API 的特定类型之间进行转换。

# 为什么 To 模式是好的

让我们看一个带有`aeson`的`ToJSON`的例子，它用于将用户的类型转换为`Value`或 JSON 表达式类型。

如果我有喜欢的类型，

```
data BigRecord = BigRecord
  { fiestaPolicies  :: [Policy]
  , brunchMenu      :: Menu
  , dinnerMenu      :: Menu
  , backgroundMusic :: Maybe (Either ScreamingMan [Song])
  }
```

此类类型的`ToJSON`实例往往很无聊，如*:

```
instance ToJSON BigRecord where
  toJSON BigRecord {..} = object
    [ ("fiesta-policy"   , toJSON fiestaPolicies )
    , ("brunch-menu"     , toJSON brunchMenu     )
    , ("dinner-menu"     , toJSON dinnerMenu     )
    , ("background-music", toJSON backgroundMusic)
    ]
```

无聊就好。无聊意味着可预测。无聊是指我在写或者读的时候不用费劲思考。

此外，类型类允许我们为像`Maybe a`和`Either a b`这样的多态类型编写一个函数。`a`和`b`的实例将自动正确使用。

类型类提供规范性，一个类型只有一个实例。对于每种类型只有一个有效转换的转换来说，`To`类是一个很好的选择。类型类模式利用标识符重载和规范化来减少编写实例时的自由度。

# 自由不是免费的

如果我不使用`To`类型的类模式，我需要做出更多的选择。每次我做出选择，我就增加了做出错误选择的机会。

这里有一种我们可以像`ToJSON`例子一样编写转换代码的方法:

```
bigRecordToJSON :: BigRecord -> Value
bigRecordToJSON BigRecord {..} = object
  [ ("fiesta-policy" . , listToJSON policyToJSON fiestaPolicies)
  , ("brunch-menu"     , brunchMenuToJSON brunchMenu)
  , ("dinner-menu"     , dinnerMenuToJSON dinnerMenu)
  , ("background-music", maybeToJSON (eitherJSON screamingManToJSON           
                                                 playlistToJSON
                                     ) 
                                     backgroundMusic
    )
  ]
```

即使转换完全由类型决定，我也必须为转换选择正确的函数。此外，我必须向每个多态转换函数传递一个额外的函数——实际上，每个类型变量传递一个函数。

这段代码是最好的情况。我已经预先分解了我的转换函数，并使用了命名约定。我并不总是发现自己处于如此幸运的境地。

由于将代码分解成小函数的动机较小，它也可能看起来像这样:

```
bigRecordToJSON :: BigRecord -> Value
bigRecordToJSON BigRecord {..} = object
  [ ("fiesta-policy"   , Array 
                       $ Vector.fromList 
                       $ map policyToJSON fiestaPolicies
    )
  , ("brunch-menu"     , Array 
                       $ Vector.fromList
                       $ map brunchMenuItem brunchMenu    
    )
  , ("dinner-menu"     , Array 
                       $ Vector.fromList
                       $ map dinnerMenuItem dinnerMenu    
    )
  , ("background-music", maybe (object []) 
                               (eitherJSON screamingManToJSON           
                                           playlistToJSON
                               ) 
                               backgroundMusic
    )
  ]
```

对于这两个版本，我添加了潜在的问题。如果你还没有找到问题所在，试试看你能不能找到。

如果一个人不使用类型类，他们会给自己比他们潜在需要更多的自由。这为错误、不一致和混乱打开了大门。

# 往返反模式

有时使用一个既有`to`又有`from`方法的类型类。

来自`binary`包的`[Binary](https://hackage.haskell.org/package/binary-0.8.5.1/docs/Data-Binary.html#g:1)`类型类就是这种情况。在`binary` API 中没有要求指定两个方向的函数，但是无论如何我必须实现两个方向。

如果我正在编写一个向服务器发送数据的客户端，很可能我只会序列化数据类型而不会反序列化它们。我仍然需要一个反序列化的实现，所以我将创建一个伪实现，或者抛出一个错误。

无意义的方法实现是维护的隐患。未来的程序员可能会认为实例已经完全实现，当反序列化失败时会感到困惑。

# 制定法律

让`Binary`的实现同时拥有`to`和`from`的理由可能是它允许一个人要求法律`decode . encode = id`，而`Binary`将法律`decode . encode = id`记录为一个需求。

法律作为一种规范是有用的。它对等式推理也很有用，这意味着我可以用`id`代替`decode . encode`。这是一个非常有用的优化…如果有人曾经遇到过`decode . encode`的实际使用。**

只是我不认为这在实践中会发生。编码和解码是完全不同的代码路径的一部分。

# 为什么不好

实际上，你有一个类型类，它有时对方法有无意义的实现，有一个没有利用两个方向的 API，有一个对等式推理不是特别有用的法则。

从这个角度来看，我认为往返类型类在很多时候是一种反模式。不是给程序提供更多的推理能力，而是因为被滥用而提供的更少。他们试图鼓励一种并不总是适用的最佳实践。它们不适合它们用来解决的问题。

如果我们的类型应该从`to`和`from`方向往返，我们应该做与我们的法律相同的事情:记录它并测试它。***

在某些情况下，在一个类型类中同时拥有`to`和`from`是有意义的，但是缺乏令人信服的理由(比如一个共享的关联类型，或者一个 API 函数需要一个类型的两个方向)，将它们包含在一起是错误的。

# 非法商品

对于类型和转换函数之间存在单一映射的情况，可以使用`To`和`From`类型类来组成易于推理的函数。避免类型类会导致转换函数接受额外的元素函数(想想前面的`eitherToJSON`例子)，或者一些定制的不一致的过程。`To`和`From`实例隐式地使用唯一且正确的实现。这个过程自动防止一类不正确的实现，并鼓励纯粹通过组合的正确性。

简而言之，`To`和`From`类型类是简单而强大的类型类模式，你不应该害怕使用。

*对于`aeson` smarties，我避免使用`.=`方法，因为大多数`To`类没有类似的东西，这使得一般模式更加明显。

**等式推理不仅可用于重写表达式以简化它们，还可用于扩展表达式和导出函数。然而，除了 Conal，我没有见过 Haskellers 这样做。

*   ** Cale Gibbard 除了帮助我完善我的想法之外，还指出了在对一个`Just Nothing`进行编码和解码时，`Maybe`的`ToJSON`和`FromJSON`实例不会往返。我认为`postgresql-simple`的`ToField`实例对于`Maybe`来说也是如此。理想情况下，应该更清楚地记录这一点，因为 Haskeller 倾向于假设这些实例是往返的。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个大家庭的一员。我们现在[正在接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！