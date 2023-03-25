# Haskell 中改进导入列表的 4 个步骤

> 原文：<https://medium.com/hackernoon/4-steps-to-a-better-imports-list-in-haskell-43a3d868273c>

所以你看到这个标题的想法可能是“天哪，一篇关于进口的文章…还有比这更无聊的吗？”但是请原谅我。这实际上是很容易正确完成的事情。但是如果你甚至有点懒(我经常这样)，你就会做错。那会对你和你队友的工作效率产生非常不好的影响**。**

想象一下，有人正试图对你的代码库做出他们的第一个**贡献**。他们不知道哪些函数在哪里定义。他们不一定熟悉你使用的库。那么当他们遇到一个他们不知道的函数时会发生什么呢？他们将在文件本身中搜索定义。但是如果不在那里，他们将不得不寻找进口部门。

一旦您构建了一个规模适中的 Haskell 程序，您就会体会到任何源文件的 imports 部分的重要性。几乎所有重要的程序都需要基本库之外的代码。这意味着你必须导入通过 Stack 或 Cabal 获得的库代码。您还会希望代码的不同部分协同工作。因此，自然地，您的不同模块也会相互导入。

当你写好你的进口清单，你的贡献者会喜欢你的。他们将确切地知道他们需要在哪里寻找正确的文档。写的很差，他们会被卡住，会很沮丧。他们将会失去各种时间去搜索函数定义。

像 Java 和 iOS 这样的技术栈有像 IntelliJ 或 XCode 这样的成熟 ide。这使得人们很容易点击一个方法并找到它的文档。但是你不能指望人们在他们的 Haskell 环境中拥有这些特性。现在想象一下，他们正在寻找的函数或表达式没有在他们正在寻找的文件中定义。他们需要自己找出是哪个模块导入了它。这里有一些好的做法，使这一过程变得简单。

# 仅导入您需要的功能

让你的导入更清晰的第一个方法是**指定你导入哪个函数**。最大的诱惑是在导入中只写模块名。这将允许您使用该库中的任何函数。但是你也可以限制你使用的功能。您可以在模块名称后使用带括号的列表来实现这一点。

```
import Data.List.Split (splitOn) 
import Data.Maybe (isJust, isNothing)
```

现在假设有人在您的代码中看到了 splitOn 函数，但不知道它是做什么的，也不知道它的类型是什么。通过查看你的进口清单，他们知道他们可以通过谷歌搜索数据找到答案。列表。拆分库。

# 合格进口

澄清您的导入的第二种方法是使用**限定的**关键字。这意味着您必须用分配给该模块的名称作为您在该模块中使用的每个函数的前缀。您可以使用完整的模块名，也可以使用关键字`as`。这表明您将使用不同的、通常更短的名称来引用该模块。

```
import qualified Data.Map as M
import qualified Data.List.Split
...
myMap :: M.Map String [String]
myMap = M.fromList [(“first”, Data.List.Split.splitOn “abababababa” “a”)]
```

在这个例子中，贡献者可以确切地看到我们的函数和类型来自哪里。由于前缀“M”,`fromList`函数和“Map”数据结构属于`Data.Map`模块。`splitOn`功能显然也来自`Data.List.Split`。

你甚至可以用不同的方式导入**同一个模块**。这允许您以不同的方式命名某些函数。这有助于避免在类型名前面加上前缀，因此您的类型签名保持干净。在这个例子中，我们从`Data.ByteString`显式导入`ByteString`类型。然后我们也使它成为一个合格的导入，允许我们使用其他函数，比如`empty`。

```
import qualified Data.ByteString as B
import           Data.ByteString (ByteString)
…
myByteString :: ByteString
myByteString = B.empty
```

# 组织您的导入

接下来，您应该将内部导入与外部导入分开。也就是说，你应该有两个列表。第一个列表由内置包组成。第二个列表包含代码库中的模块。在这个例子中，“OWA”模块来自代码库。其他模块要么是 Haskell 基础库，要么是从 Hackage 下载的:

```
import qualified Data.List as L
import           System.IO (openFile)
import qualified Text.PrettyPrint.Leijen as PPrintimport           OWAPrintUtil
import           OWASwiftAbSyn
```

这很重要，因为它告诉某人**在哪里寻找模块**。如果它来自第一个列表，他们会立即知道是否需要在网上寻找。对于第二个列表中的导入，他们可以在代码库中找到该文件。

您可以通过用名称来分隔模块名称来提供更多帮助。例如，您可以将您的项目名称(或缩写)附加到所有模块名称的前面，如上所述。一个更好的方法是用文件夹来分隔你的模块，并用句点隔开。这使得在代码库的文件结构中寻找它变得更加清楚。

首先清晰地组织你的代码也有助于这个过程。例如，拥有一个包含代码库特定部分的所有类型的模块可能是一个好主意。那么对于用户来说，不同的类型名来自哪里应该是显而易见的。这可以使您不必限定所有的类型名，也不必从一个模块中导入大量的类型。

# 使列表易于阅读

最后一点，你想让**更容易阅读**你的进口清单。如果列表中只有一个合格的导入，那么就用它来排列所有其他的导入。这意味着将它们隔开，就好像它们也使用了单词`qualified`。这使得模块的实际名称都对齐。

接下来，按字母顺序写下你的清单。这有助于人们在列表中找到合适的模块。最后，尽可能将`as`语句和特定的函数导入联系起来。这样，人们就很容易看出你使用了哪些不同的模块前缀。这是你可以从 Haskell 文本编辑器插件中得到的另一个特性。

```
import qualified Data.ByteString (ByteString)
import qualified Data.Map        as M
import           Data.Maybe      as May
```

# 摘要

组织您的导入是让其他开发人员能够访问您的代码的关键！通过以下方式明确函数的来源。有两种方法可以做到这一点。您可以限定您的导入，也可以指定模块中使用的函数。将库导入与您自己的代码导入分开。这让人们知道他们是否需要在线或在代码库中寻找模块。通过按字母顺序排列和排列所有名称，使导入列表本身易于阅读。

下周敬请关注！我们将浏览 Haskell 中所有不同的字符串类型！这是另一个应该简单但有很多陷阱的话题，尤其是对初学者来说！我们将重点讨论它们之间的不同转换方式。

如果你从来没有一行 Haskell 之前，不要绝望！你可以看看我们的[入门清单](https://www.mmhaskell.com/checklist)。它将引导您下载 Haskell，并为您指出一些好的学习资源的方向。

如果你以前学过一点 Haskell，但是想要一些基础的练习，你应该看看我们的[递归练习册](https://www.mmhaskell.com/workbook)。它有两章内容，后面是 10 道练习题供你参考！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！