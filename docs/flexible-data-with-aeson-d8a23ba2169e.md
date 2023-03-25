# Aeson 提供灵活的数据

> 原文：<https://medium.com/hackernoon/flexible-data-with-aeson-d8a23ba2169e>

在某种程度上，我们的 Haskell 程序必须与网络上运行的其他程序兼容。考虑到微服务作为一种架构的使用越来越多，这一点尤其有用。不管怎样，在不同堆栈上的应用程序之间传输数据是很常见的。你需要某种格式来传输数据并在两端读取。

做这件事有许多不同的方法。但是当前 web 编程的生态系统很大程度上依赖于 JSON 格式。JSON 代表“JavaScript 对象符号”。这是一种与 Javascript 程序兼容的数据编码方式。但它也是一个任何语言都可以解析的有用的序列化系统。在本文中，我们将探索如何在 Haskell 中使用这种数据格式。

# JSON 101

JSON 将数据编码成几种不同的类型。有四种基本类型:字符串、数字、布尔值和空值。这些都以可预测的方式发挥作用。这里的每一行都是有效的 JSON 值。

```
“Hello”4.53truefalsenull
```

JSON 随后提供了两种不同的组合对象的方式。第一个是数组。数组包含多个值，并用括号分隔的列表来表示它们。与 Haskell 列表不同，您可以将多种类型的对象放在一个数组中。你甚至可以把数组放入你的数组中。

```
[1, 2, 3][“Hello”, false, 5.5][[1,2], [3,4]]
```

从 Haskell 来看，这种结构似乎有点粗略。毕竟，数组中的东西的类型并不清楚。但是你可以把多类型数组看作元组，而不是列表，这样更有意义。在 JSON 中创建对象的最后也是最重要的方法是通过对象格式。对象由大括号分隔。它们就像数组一样，可以包含任意数量的值。但是，每个值也作为键-值对分配给一个字符串名称。

```
{
  “Name” : “Name”,
  “Age” : 23,
  “Grades” : [“A”, “B”, “C”]
}
```

这些对象是无限可嵌套的，所以你可以拥有数组的对象的数组等等。

# 编码 Haskell 类型

这很好，但是我们如何使用这种格式来传输 Haskell 数据呢？我们从一个虚拟的例子开始。我们将创建一个 Haskell 数据类型，并展示该数据的几种可能的 JSON 解释。首先，我们的类型和一些示例值:

```
data Person = Person
  { name :: String
  , age :: Int
  , occupation :: Occupation 
  } deriving (Show)data Occupation = Occupation
  { title :: String
  , tenure :: Int
  , salary :: Int 
  } deriving (Show)person1 :: Person
person1 = Person
  { name = “John Doe”
  , age = 26
  , occupation = Occupation
    { title = “teacher”
    , tenure = 5
    , salary = 60000
    }
  }person2 :: Person
person2 = Person
  { name = “Jane Doe”
  , age = 25
  , occupation = Occupation
    { title = “engineer”
    , tenure = 4
    , salary = 90000
    }
  }
```

现在我们可以选择许多不同的方式来编码这些值。最基本的方法可能是这样的:

```
{
  “name” : “John Doe”,
  “age” : 26,
  “occupation” : {
    “title” : “teacher”,
    “tenure” : 5,
    “salary” : 60000
    }
}{
  “name” : “Jane Doe”,
  “age” : 25,
  “occupation” : {
    “title” : “engineer”,
    “tenure” : 4,
    “salary” : 90000
  }
}
```

现在，我们可能希望使用动态类型语言来帮助我们的朋友。为此，我们可以围绕对象中包含的类型提供更多的上下文。这种格式可能看起来更像这样:

```
{
  “type” : “person”,
  “contents” : {
    “name” : “John Doe”,
    “age” : 26,
    “occupation” = {
        “type” : “Occupation”,
        “contents” : {
        “title” : “teacher”,
        “tenure” : 5,
        “salary” : 60000
        }
      }
  }
}
```

无论哪种方式，我们最终都必须决定与谁进行互操作的格式。不过，很可能您将与一个已经设定了他们期望的外部 API 一起工作。你必须确保你匹配他们。

# 数据。艾松

现在我们知道了我们的目标值是什么，我们需要一个 Haskell 表示。这来自于[数据。Aeson](https://hackage.haskell.org/package/aeson) 图书馆(以神话人物“杰森”的父亲命名)。该库包含类型`Value`。它在其构造函数中封装了所有基本的 JSON 类型。(为了清楚起见，我替换了几个同义词):

```
data Value =
  Object (HashMap Text Value) |
  Array (Vector Value) |
  String Text |
  Number Scientific |
  Bool Bool |
  Null
```

所以我们看到所有六种元素都被表现出来了。所以当我们想要表示我们的项目时，我们将使用这些构造函数。像许多有用的 Haskell 思想一样，我们将在这里使用 typeclasses 来提供一些结构。我们将使用两个类型类:`ToJSON`和`FromJSON`。我们将首先把 Haskell 类型转换成 JSON。我们必须在我们的类型上定义`toJSON`函数，这将把它变成一个`Value`。

一般来说，我们希望将我们的数据类型放入一个对象中，这个对象将有一系列的“对”。A `Pair`是数据。键-值对的 Aeson 表示，它由一个`Text`和另一个值组成。然后我们将使用`object`函数将这些对组合成一个 JSON `Object`。我们将从`Occupation`类型开始，因为这稍微简单一些。

```
{-# LANGUAGE OverloadedString #-}import Data.Aeson (ToJSON(..), Value(..), object, (.=))...instance ToJSON Occupation where
  toJSON :: Occupation -> Value
  toJSON occupation = object
    [ “title” .= toJSON (title occupation)
    , “tenure” .= toJSON (tenure occupation)
    , “salary” .= toJSON (salary occupation)
    ]
```

`.=`操作符为我们创建了一个`Pair`。我们所有的字段都是简单类型，它们已经有了自己的`ToJSON`实例。这意味着我们可以对它们使用`toJSON`而不是`Value`构造函数。还要注意，我们使用重载字符串扩展(如这里介绍的)，因为我们使用字符串文字代替`Text`对象。一旦我们为`Occupation`类型定义了实例，我们就可以在 occupation 对象上调用`toJSON`。这使得为`Person`类型定义一个实例变得容易。

```
instance ToJSON Person where
  toJSON person = object
    [ “name” .= toJSON (name person)
    , “age” .= toJSON (age person)
    , “occupation” .= toJSON (occupation person)
    ]
```

现在我们可以从我们的数据类型创建 JSON 值了！一般来说，我们还希望能够解析 JSON 值，并将它们转换成我们的数据类型。我们将使用一元符号来封装失败的可能性。如果我们要寻找的键没有出现，我们想要抛出一个错误:

```
import Data.Aeson (ToJSON(..), Value(..), object, (.=), (.:), FromJSON(..), withObject)...instance FromJSON Occupation where
  parseJSON = withObject “Occupation” $ \o -> do
    title_ <- o .: “title”
    tenure_ <- o .: “tenure”
    salary_ <- o .: “salary”
    return $ Occupation title_ tenure_ salary_instance FromJSON Person where
  parseJSON = withObject “Person” $ \o -> do
    name_ <- o .: “name”
    age_ <- o .: “age”
    occupation_ <- o .: “occupation”
    return $ Person name_ age_ occupation_
```

这里有一些注释。`parseJSON`函数通过 eta 缩减来定义。这就是为什么似乎没有参数。`.:`操作符可以抓取任何符合`FromJSON`本身的数据类型。正如我们可以将`toJSON`与简单类型`String`和`Int`一起使用一样，我们也可以开箱即用地解析它们。另外，我们描述了如何解析一个`Occupation`，这就是为什么我们可以在 occupation 字段上使用操作符。此外，`withObject`函数的第一个参数是一条错误消息，如果解析失败，我们会收到这条消息。最后一点需要注意的是，我们的`FromJSON`和`ToJSON`实例是彼此相反的。这绝对是您在自己的 API 定义中实施的一个好属性。

现在我们有了这些实例，我们可以看到不同对象的 JSON 字节串:

```
>> Data.Aeson.encode person1
"{\"age\":26,\"name\":\"John Doe\",\"occupation\":{\"salary\":60000,\"tenure\":5,\"title\":\"teacher\"}}"
>> Data.Aeson.encode person2
"{\"age\":25,\"name\":\"Jane Doe\",\"occupation\":{\"salary\":90000,\"tenure\":4,\"title\":\"engineer\"}}"
```

# 派生实例

您可能会看到我们派生的实例，并认为代码看起来像样板文件。事实上，这些并不是特别有趣的实例。请记住，外部 API 可能有一些奇怪的需求。所以知道如何手工创建这些实例是很好的。不管怎样，你可能想知道是否有可能用我们可以派生`Eq`或`Ord`的相同方式来派生这些实例。我们可以，但是有点复杂。实际上有两种方法可以做到这一点，它们都涉及编译器扩展。第一种方式看起来更像是衍生路线。我们将最终把`deriving (ToJSON, FromJSON)`放入我们的类型中。但是在我们这样做之前，我们必须让它们派生出`Generic` typeclass。

`Generic`是一个类，它允许 GHC 在通用构造函数的层次上表示你的类型。要使用它，首先需要打开`DeriveGeneric`的编译器扩展。这允许您为数据派生泛型类型类。然后你也需要打开`DeriveAnyClass`扩展。一旦你完成了这些，你就可以为你的类型派生出`Generic`、`ToJSON`和`FromJSON`实例。

```
{-# LANGUAGE DeriveGeneric     #-}
{-# LANGUAGE DeriveAnyClass    #-}…data Person = Person
  { name :: String
  , age :: Int
  , occupation :: Occupation
  } deriving (Show, Generic, ToJSON, FromJSON)data Occupation = Occupation
  { title :: String
  , tenure :: Int
  , salary :: Int
  } deriving (Show, Generic, ToJSON, FromJSON)
```

有了这些定义，我们将获得与手动实例相同的编码输出:

```
>> Data.Aeson.encode person1
"{\"age\":26,\"name\":\"John Doe\",\"occupation\":{\"salary\":60000,\"tenure\":5,\"title\":\"teacher\"}}"
>> Data.Aeson.encode person2
"{\"age\":25,\"name\":\"Jane Doe\",\"occupation\":{\"salary\":90000,\"tenure\":4,\"title\":\"engineer\"}}"
```

正如你所看到的，这是一个超级酷的想法，它在 Haskell 生态系统中有着广泛的用途。有许多有用的类型类具有与`ToJSON`和`FromJSON`相似的模式。您需要实例来满足库约束。但是你要写的实例是非常样板化的。你可以通过结合使用泛型类型类和`DeriveAnyClass`得到很多这样的实例。

第二条路线涉及编写模板 Haskell。模板 Haskell 是另一个编译器扩展，允许 GHC 为你生成代码。有许多库有特定的模板 Haskell 函数。这些可以让你避免大量的样板代码，否则会非常乏味。`Data.Aeson`就是这些库中的一个。

首先，您需要启用模板 Haskell 扩展。然后导入`Data.Aeson.TH`，你就可以在你的类型上使用简单的功能`deriveJSON`。这将给你一些漂亮的新的`ToJSON`和`FromJSON`实例。

```
{-# LANGUAGE TemplateHaskell #-}import Data.Aeson.TH (deriveJSON, defaultOptions)data Person = Person
  { name :: String
  , age :: Int
  , occupation :: Occupation
  } deriving (Show) data Occupation = Occupation
  { title :: String
  , tenure :: Int
  , salary :: Int
  } deriving (Show)-- The two apostrophes before a type name is template haskell syntax
deriveJSON defaultOptions ''Occupation
deriveJSON defaultOptions ''Person
```

我们将再次得到类似的输出:

```
>> Data.Aeson.encode person1
"{\"name\":\"John Doe\",\"age\":26,\"occupation\":{\"title\":\"teacher\",\"tenure\":5,\"salary\":60000}}"
>> Data.Aeson.encode person2
"{\"name\":\"Jane Doe\",\"age\":25,\"occupation\":{\"title\":\"engineer\",\"tenure\":4,\"salary\":90000}}"
```

不像派生这些类型整块布料，您实际上在这里有选择。注意我们最初通过了`defaultOptions`。我们可以改变这一点，转而传递一些修改过的选项。例如，如果需要，我们可以在字段名前添加类型:

```
deriveJSON (defaultOptions { fieldLabelModifier = ("occupation_" ++)}) ''Occupation
deriveJSON (defaultOptions { fieldLabelModifier = ("person_" ++)}) ''Person
```

产生的输出是:

```
>> Data.Aeson.encode person1
"{\"person_name\":\"John Doe\",\"person_age\":26,\"person_occupation\":{\"occupation_title\":\"teacher\",\"occupation_tenure\":5,\"occupation_salary\":60000}}"
```

模板 Haskell 可以方便。它减少了您必须编写的样板代码的数量。但是这也会使你的代码需要更长的编译时间。当您使用模板 Haskell 时，可访问性也是有代价的。大多数 Haskell 新手都知道`deriving`语法。但是如果你使用`deriveJSON`，他们可能会挠头想知道你到底在哪里定义了 JSON 实例。

# 编码、解码和通过网络发送

一旦我们定义了不同的实例，你可能想知道我们实际上是如何使用它们的。答案取决于您使用的库。例如，`Servant`库使它变得简单。除了定义实例之外，您不需要做任何序列化工作！服务端点定义它们的返回类型以及它们的响应内容类型。一旦定义了这些，序列化就会在后台进行。如果你需要做一个 API，Servant 是一个很棒的库。如果你想了解图书馆的基本情况，你应该看看我在 BayHac 2017 上的演讲。还可以看一下那个演讲中的[代码示例](https://www.mmhaskell.com/bayhac)中的一个特定示例。

现在，其他库会要求您处理 JSON 字节串。幸运的是，一旦定义了 FromJSON 和 ToJSON 实例，这也非常容易。正如我在本文的例子中所展示的，`Data.Aeson`具有`encode`功能。这将把您的 JSON 使能类型转换成一个可以通过网络发送的`ByteString`。很简单。

```
>> Data.Aeson.encode person1
"{\"name\":\"John Doe\",\"age\":26,\"occupation\":{\"title\":\"teacher\",\"tenure\":5,\"salary\":60000}}"
```

一如既往，解码有点棘手。您必须考虑数据格式不正确的可能性。你可以调用简单的`decode`函数。这为您提供了一个`Maybe`值，因此如果解析不成功，您将得到`Nothing`。在解释器中，您还应该确保指定您想要从`decode`得到的结果类型，否则您将得到它试图将它解析为`()`，这将失败。

```
>> let b = Data.Aeson.encode person1
>> Data.Aeson.decode b :: Maybe Person
Just (Person {name = "John Doe", age = 26, occupation = Occupation {title = "teacher", tenure = 5, salary = 60000}})
```

为了更好地处理错误情况，您应该选择`eitherDecode`。如果失败，这将给你一个错误信息。

```
>> Data.Aeson.eitherDecode b :: Either String Person
Right (Person {name = "John Doe", age = 26, occupation = Occupation {title = "teacher", tenure = 5, salary = 60000}})
>> let badString = "{\"name\":\"John Doe\",\"occupation\":{\"salary\":60000,\"tenure\":5,\"title\":\"teacher\"}}"
>> Data.Aeson.decode badString :: Maybe Person
Nothing
>> Data.Aeson.eitherDecode badString :: Either String Person
Left "Error in $: key \"age\" not present"
```

# 摘要

现在我们知道了将 Haskell 数据序列化为 JSON 的所有要点。第一步是为您想要序列化的类型定义`ToJSON`和`FromJSON`实例。大多数情况下，这些都很容易写出来。但是也有一些不同的机制来推导它们。一旦你这样做了，像 Servant 这样的库就可以直接使用这些实例了。但是处理手动`ByteString`值也很容易。你可以简单地使用`encode`功能和`decode`的各种风格。

也许你总是对自己说，“Haskell 不可能对 web 编程有用。”我希望这让你看到了一些可能性。你应该试试哈斯克尔！下载我们的[入门清单](https://www.mmhaskell.com/checklist)，获取一些有用工具的链接！

也许你已经使用了一些 Haskell，但是你担心自己在尝试 web 编程时走在了前面。你应该下载我们的[递归练习册](https://www.mmhaskell.com/workbook)。它将帮助您巩固您的函数式编程技能！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！