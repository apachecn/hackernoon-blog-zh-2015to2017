# 快速编码能力

> 原文：<https://medium.com/hackernoon/swift-codability-d0d232065cad>

尝试过在 swift 中解析 JSON 吗？我知道！没错，大量的样板文件和大量的零支票让这种体验变得非常糟糕。

我在这里为您提供一个新推出的 Swift 4 解决这个问题的方案，有了它，苹果终于毫无痛苦地回答了解析 JSON 的问题。

# 让我们从基础开始

让我们看一个基本的 JSON 结构

我们的 swift 映射模型将如下所示:

为了将这些 JSON 数据转换成我们的模型，这就是我们需要编写的全部代码

您也可以将这个模型编码到 JSON 中

太好了！！不是吗？

我已经将 Person 模型标记为可编码的，它是两个单向协议的组合。所以，如果你只想编码或解码，你可以采用适当的协议。

但是，`Codable`带有默认实现，您可以采用这个协议来获得**的免费实现。**

有一个陷阱！
模型中的变量名应该与 JSON 中的键名相匹配。

哇…生活是不公平的，大多数 API 都是基于蛇的案例命名的😢。好吧，放松…

# 使用自定义键名

键名由编译器在符合`CodingKey`协议的`CodingKeys`枚举中自动生成。它定义了我们如何将一个属性与一个值联系起来。

因此，要定制密钥，我们必须编写自己的`CodingKeys`实现，在那里我们可以为您想要更改的密钥提供`String`值。

让我们举个例子，考虑这个 JSON

这是我们的模型的样子

嗯，就是这样👍🏻

Swift Encoder & Decoder 使用这个`CodingKeys` enum 作为查找，并将相应的 JSON 键匹配到模型变量。

> 此外，由于`String`枚举被隐式地赋予原始值作为每个案例的名称，我们只需要为有问题的案例提供原始值。

但是等等，对象或包装呢？

# 对象和包装

假设我们的 JSON 是

在这里，你必须确保你的`Film`对象也符合`Codable`，基本上就是这样。来，看一看。

根级数组呢？任何简单的解码方法，如果我们的 JSON 被包装在一个数组中。\_(ツ)_/以这个 JSON 为例。

你只需要写这个，

😬

# 如何处理日期？

好吧，让你的生活在一起，^.~

然而在斯威夫特看来，

因为 JSON 没有表示日期的数据类型，所以这些信息要么以 ISO 8601、从参考日期算起的秒数，要么以某种自定义格式进行序列化。然后使用`String`对其进行处理，并使用 DateFormatter 将其转换为`Date`。啊！我知道！！

好消息是，现在您可以使用`JSONDecoder`将 JSON 中的日期字符串解码到模型中。检查一下

`Encoder`也是如此。除了这些策略，你甚至可以使用定制的编码器和解码器

```
custom((Decoder) throws -> Date)  //For Decoding
custom((Date, Encoder) throws -> Void)  //For Encoding
```

我知道，我知道，举个例子就好了。这里

# 处理 Swift。数据和网址！！

您可能会在 JSON 中遇到 base64 编码字符串形式的`Data`,为了处理这种情况，`JSONEncoder`给出了两种策略

```
.base64
.custom((Data, Encoder) throws -> Void)
```

T4 也是如此。

到了`URL`，只需将数据类型从`String`替换为`URL`即可

# 处理浮点？？

嗯，这并不经常发生，但有时 JSON 可能包含无效的***【NaN】、“+Infinity”或“-Infinity”***。这些在 Swift 中不被认可！

通常，如果您不提供处理这种不一致性的实现，您的解码器将抛出一个错误。只管写

同样，这也可以用`JSONEncoder`来完成。

等等，但是单向一致性呢？没有默认的实现！我给你看，真的很酷

# 单向可编码和可解码

有时你可能只想遵从`Encodable`或`Decodable`并从那里开始工作。但是，在此之前，我们需要理解容器的概念。以下是一些类型的容器:

*   **键控容器:**这本质上是一个字典类型，包含键值对。
*   **未加密的容器:**表示一个包装器，本质上是一个数组。
*   **单值容器:**表示由键表示的 raw 类型。

现在，你所要做的就是指导编译器你希望数据如何被解码，就这样。看一看

让我们假设我们有下面的 JSON，现在我们可以做一个嵌套模型**或者**我们可以这样做:

由于电影名和导演名嵌套在一个容器中，我们可以使用 decoder 的嵌套容器属性来提取值并在相应的键中使用。这里，我将`"film"`对象提取到`filmInfo`容器中，然后用它来解码`movieName`和`director`键中的值。

类似地，对于`Encoder`，我们获取`movieName`和`director`键，并在`filmInfo`容器中对它们进行编码，这将像以前一样返回准确的 JSON。这就是编码键的力量。

# 处理字典和枚举？

您不经常需要这样做，但是您也可以在字典或枚举中解析您的 JSON。

## 词典

考虑下面的 JSON

你能找出这些物体之间的相似之处吗？两个`"Death Star" & "Millennium Falcon"`都在里面嵌套了一个模型。让我们看看我们能对此做些什么

## 列举

让我们直接进入这个 JSON

耶！这是一个奇怪的 JSON，但它会做的把戏。让我们看看我们的代码

恭喜你。您已经成功地将 JSON 解析为枚举案例。

# 有一些错误需要处理:)

到目前为止，我们所使用的只是一个简单的`catch`作为灵丹妙药，但是有一些已知的情况，我们可以相应地捕捉和处理。

1.  **DecodingError . data corrupted(DecodingError。Context):** 如果您收到的数据不是 JSON，可能是 HTML 或 API 错误，就会出现这种情况。您将使用`context`获得这些信息。
2.  **DecodingError . keynotfound(coding key，DecodingError。Context):** 如果在 JSON 中找不到所需的密钥，就会出现这种情况，`CodingKey`会给出有问题的密钥，`context`会给出关于在哪里发生了什么的信息。
3.  **decodingerror . type mismatch(Any。类型，DecodingError。上下文):**如果键之间的类型不匹配，就会出现这种情况。你可以用`context`和`type`来了解发生了什么。

看一看

`Encoder`也是如此，这些错误非常有助于您灵活地适应某些情况并适当地处理它们。

# 进一步学习

没有比 WWDC 2017 资源本身更好的了。这里…

[](https://developer.apple.com/videos/play/wwdc2017/212/) [## 基础新功能- WWDC 2017 -视频-苹果开发者

### 无论你是在为 iOS、macOS、watchOS 还是 tvOS 开发应用，你都可以从苹果的 SDK 中获得很多功能…

developer.apple.com](https://developer.apple.com/videos/play/wwdc2017/212/) [](https://developer.apple.com/documentation/foundation/archives_and_serialization/using_json_with_custom_types) [## 将 JSON 用于自定义类型| Apple 开发人员文档

### 您从其他应用程序、服务和文件发送或接收的 JSON 数据可以有许多不同的形状和结构…

developer.apple.com](https://developer.apple.com/documentation/foundation/archives_and_serialization/using_json_with_custom_types) 

# 结论

呼！这是一个漫长的旅程，但这是 Swift JSON 解析的未来。欢迎评论/建议:)

## 阿迪奥斯 (-ω-ゞ

## 愿原力与你同在