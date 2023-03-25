# 解开哈斯克尔的弦

> 原文：<https://medium.com/hackernoon/untangling-haskells-strings-d47d2170cc4>

Haskell 也不是没有缺点。即使对于专业人士来说，最普遍承认的烦恼之一是跟踪不同的字符串类型。Haskell 中总共有五种不同的字符串类型。记住 Haskell 是强类型的。所以如果我们想用不同的方式来表示字符串，我们必须有不同的类型。这激发了对这五种类型的需求，它们的用例略有不同。当你使用它们中的任何一个的时候，它都不是那么糟糕。但是当你不断地在它们之间转换时，这可能是一个大麻烦。在本文中，我们将讨论这五种不同的类型。我们将研究它们不同的用例，并观察如何在它们之间转换。

# 用线串

`String`类型是 Haskell 中表示字符串的最基本形式。它是 unicode 字符列表**的简单类型同义词**(`Char`类型)。所以每当你在编译错误中看到`[Char]`，要知道这是指基本的`String`类型。默认情况下，当您在 Haskell 代码中输入一个字符串文字时，编译器会将其推断为一个`String`。

```
myFirstString :: String
myFirstString = “Hello”
```

字符串的列表表示给了我们一些有用的行为。因为字符串实际上是一个列表，所以我们可以使用 Data.List 中所有我们熟悉的函数。

```
>> let a = “Hello”
>> map Data.Char.toUpper a
“HELLO”
>> ‘x’ : a
“xHello”
>> a ++ “ Person!”
“Hello Person!”
>> Data.List.sort “dbca”
“abcd”
```

普通字符串类型的主要缺点是效率低下。这是不变性的结果。例如，假设我们有:

```
myFirstString :: String
myFirstString = “Hello”myModifiedString :: String
myModifiedString = map toLower (sort (myFirstString ++ “ Person!”))
```

这将总共分配 4 个字符串。首先是`myFirstString`。第二是“人！”字面意思。然后，我们可以追加这些内容，而无需创建另一个字符串。但是排序后的版本将是第三个分配，第四个将是小写版本。这种持续的分配会使我们的代码无法执行，并且**不适合重载操作**。

# 文本

字符串类型的`Text`家族解决了这个难题。有两种`Text`类型:严格型和懒惰型。大多数情况下，您会使用严格形式。然而，在你知道不需要完整字符串的某些情况下，惰性形式是有用的。

`Text`的主要优势在于其功能受到“融合”。这意味着编译器实际上可以**防止我们在上一个例子中看到的多重分配的问题**。例如，如果我们看这个:

```
import qualified Data.Char as C
import qualified Data.Text as T
optimizedTextVersion :: T.Text
optimizedTextVersion = T.cons ‘c’ (T.map C.toLower (T.append (T.Text “Hello “) (T.Text “ Person!”)))
```

这实际上只会在运行时分配一个`Text`对象。这将使它**比`String`版本**更有效。所以对于大量文本处理的工业应用，你最好使用`Text`类型而不是`String`类型。

# 字节字符串

第三种类型属于`ByteString`类别。和`Text`一样，字节串也有严格和懒惰的变种。不过，惰性字节字符串比惰性文本值更常见一些。字节串是字符的**最低级别表示**。这是你能得到的最接近真实机器水平的解释。字节串的核心是一系列`Word8`对象。`Word8`只是一个代表 unicode 字符的 8 位数字。

大多数网络库将使用字节串，因为它们对**序列化**最有意义。当您跨平台发送信息时，您无法确定另一端的编码。如果您将信息存储在数据库中，您通常也会希望使用字节字符串。像`Text`类型一样，它们通常比字符串更有效。

# 转换

因此，所有这些类型都存在，真正的问题是它们之间的转换。当你想写一些基本的代码，但你有一个不同的字符串类型时，这可能是非常令人沮丧的。如果你不记得的话，你将不得不查找转换，这可能很烦人。我们的第一个例子是`String`和`Text`。这很简单。`Data.Text`包导出了这两个函数，它们正是您想要的:

```
pack :: String -> Text
unpack :: Text -> String
```

`Data.Text.Lazy`中有对等物。我们将在`ByteStrings`和`Strings`之间找到相似的函数。它们存在于`Data.ByteString.Char8`包中:

```
pack :: String -> ByteString
unpack :: ByteString -> String
```

请注意，这些仅适用于严格的`ByteStrings`。要在严格和懒惰之间转换，您需要来自文本类型的`.Lazy`版本的函数。例如，`Data.Text.Lazy`出口:

```
toStrict :: Data.Text.Lazy.Text -> Data.Text.Text -- (Lazy to strict)
fromStrict :: Data.Text.Text -> Data.Text.Lazy.Text -- (Strict to lazy)
```

`Data.ByteString.Lazy`中有对等词。我们要讨论的最后一个转换是在`Text`和`ByteString`之间。您可以使用`String`作为具有上述功能的中间类型。但是这需要对编码做一定的假设，并且容易失败。从`Text`到`ByteString`很简单，假设**你知道你的数据格式**。以下功能存在于`Data.Text.Encoding`中:

```
encodeUtf8 :: Text -> ByteString-- LE = Little Endian format, BE = Big EndianencodeUtf16LE :: Text -> ByteString
encodeUtf16BE :: Text -> ByteString
encodeUtf32LE :: Text -> ByteString
encodeUtf32BE :: Text -> ByteString
```

一般来说，您将使用 UTF8 编码的文本，因此`encodeUtf8`。解码稍微复杂一点。这个库中有简单的函数:

```
decodeUtf8 :: ByteString -> Text
decodeUtf16LE :: ByteString -> Text
decodeUtf16BE :: ByteString -> Text
decodeUtf32LE :: ByteString -> Text
decodeUtf32BE :: ByteString -> Text
```

但是如果您的字节字符串与格式不匹配，这些会抛出错误。运行时异常是不好的，所以对于 UTF8，我们有这个函数:

```
decodeUtf8’ :: ByteString -> Either UnicodeException Text
```

这让我们将它包装在一个`Either`中，并处理可能的错误。对于其他格式，我们必须依赖如下函数:

```
decodeUtf16LEWith :: OnDecodeError -> ByteString -> Text
```

其中`OnDecodeError`是特定类型的处理器。这些功能可能特别麻烦和难以处理。幸运的是，您将最经常使用 UTF8。

# 过载字符串

因此，在我的文章中，我们还没有过多地涉及语言扩展。但这是我们第一个真实的例子。它旨在向您展示**语言扩展并不特别可怕**！正如我们前面看到的，Haskell 通常会将代码中的字符串解释为`String`类型。这意味着您不能拥有以下代码:

```
-- Fails
myText :: Text
myText = “Hello”myBytestring :: ByteString
myBytestring = “Hello”
```

编译器希望这两个值都是`String`类型，而不是您给定的类型。所以这通常会抛出编译器错误。然而，使用`OverloadedStrings`扩展，您可以修复这个问题！扩展使用类似`{-# LANGUAGE … #-}`的标签。它们通常被添加到源文件的顶部。

```
{-# LANGUAGE OverloadedStrings #-}
-- This works!
myText :: Text
myText = “Hello”myBytestring :: ByteString
myBytestring = “Hello”
```

事实上，对于您创建的任何类型，您都可以创建一个`IsString` typeclass 的实例。这将允许您使用字符串来表示它。

```
{-# LANGUAGE OverloadedStrings #-}import qualified Data.String (IsString(..))data MyType = MyType Stringinstance IsString MyType where
  fromString s = MyType smyTypeAsString :: MyType
myTypeAsString = “Hello”
```

您也可以在 GHCI 中启用此扩展。你需要使用命令`:set -XOverloadedStrings`。

# 摘要

Haskell 使用 5 种不同的类型来表示字符串。其中两个是懒惰版本。`String`类型是字符列表的类型同义词，通常效率很低。`Text`以稍微不同的方式表示字符串，并且可以将操作融合在一起以提高效率。`ByteString`是最适合序列化的低级表示。类型之间的转换方式有很多，很难保持直线。最后，`OverloadedStrings`编译器可以让你的生活更轻松。它允许你用一个字符串来引用你的任何不同的字符串类型。

如果你还没有机会开始使用 Haskell，你应该看看我们的[入门清单](https://mmhaskell.com/checklist)。它将指导您完成安装和一些基础知识！

下周一定要回来看看！现在我们已经理解了字符串，我们将分成不同类型的另一个潜在棘手的系统。我们将研究不同的数字类型以及可以在它们之间运行的简单转换。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！