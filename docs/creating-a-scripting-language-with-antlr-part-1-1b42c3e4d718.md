# 用 ANTLR 创建脚本语言—第 1 部分

> 原文：<https://medium.com/hackernoon/creating-a-scripting-language-with-antlr-part-1-1b42c3e4d718>

在此之后阅读[第 2 部分](/@thosakwe/creating-a-scripting-language-with-antlr-part-2-ba4d8d6eab55)。

计算机科学是一个不断发展的领域。无论是小范围还是大范围，它总是在变化的。70 年前，当第一台通用计算机 ENIAC 问世时，没有人会想到我们最终能够将强大的计算机放在口袋里随身携带。

![](img/7845c71c2fcf83cec9f6e65fdf194374.png)

Imagine that the first computers looked like this monster!

随着计算机科学的发展，计算机[编程](https://hackernoon.com/tagged/programming)也在发展。最明显的变化之一是编程语言语法可用性的极大提高。像 C 和 Javascript 这样的语言不再需要像 ENIAC 的程序员那样在物理机器上移动电缆和开关。相反，我们可以在文本编辑器中键入类似代数的东西，然后让计算机遵循我们的指令。

当新的问题出现在开发人员面前时，现有的语言会改变自己以适应新的改进。有时，全新的编程语言诞生了。谁知道呢？你的可能就是下一个。当我们构建一个编译成 Javascript 的最小脚本语言时，本教程将教你编译理论的基础。

我们选择的工具叫做 [ANTLR](http://www.antlr.org/) ，“另一种语言识别工具”ANTLR 是由 Terrence Parr 教授在 1989 年作为一个高级解析器生成工具创建的，现在已经是它的第四个版本了。

ANTLR 将语法文件(. g4)作为输入，并根据该语法生成解析输入文本的代码。[工具](https://hackernoon.com/tagged/tool)官方支持 Java、C#、Javascript 和 Python 的代码生成。

我们要写的编译器其实是一个 *transpiler* ，它取用一种语言编写的代码，输出另一种语言的代码。一个真正的编译器会接受输入代码并生成机器码或低级语言的代码。

这个 transpiler 将用 Javascript 编写，并根据我们语言的输入文本输出有效的 Javascript 代码。

但是在我们设计我们的语言之前，我们需要了解一点关于编译器的理论。

假设您有这样一段代码:

```
set name to “world”
set one to 1say "Hello, ${name}!"
```

你想产生:

```
var name = “world”;
var one = 1;alert("Hello, " + name + "!");
```

有几种方法可以获取输入代码并生成 Javascript。一种方法是通过一系列正则表达式传递输入。然而，对于更复杂的语言，仅仅通过正则表达式进行编译会变得非常复杂和难以管理。

首选的方法是一个分为三步的过程，首先是所谓的*扫描、*或*词法分析*。扫描仪(也称为*词法分析器*)枚举输入文本中的每个字符，并将这些字符分组到称为*词法分析器*的单元中。词位的概念存在于自然语言和计算机语言中。词位非常抽象，只不过是一组字符。简而言之，词位就是字符串。词法分析器从输入文本中提取特定的字符串。

扫描器将每个字符串与预定义的模式进行比较(这里适合使用正则表达式或类似的东西)，并将每个词位归类为一个*标记*。标记确实是词位，只是附加了语义定义。例如，假设您将关键字“if”存储为一个词位。词法分析器的责任是将这个“if”词法分析器识别为一个“if 关键字”标记。

一个重要的考虑事项是，除了将字符串与预定义的模式进行匹配之外，词法分析器不应该包含任何真正的逻辑，这将为您节省大量时间和精力。

这个逻辑是一个*解析器*的工作。*语法分析*或*解析*，是识别特定记号序列并将其组织成输入文本的结构表示的编译步骤。执行此操作的代码单元被称为*解析器*。通常，解析器构建树，其中有一个根上下文，它有多个子上下文，表示从输入代码中识别的标记序列。如果你以前研究过编译器，你可能会认为这个概念是一个抽象语法树，缩写为 AST T21。AST 是编译过程中最重要的部分，因为它实际上是编译器处理代码的路线图。

以上面的代码示例为例，AST 可能如下所示:

```
<root context>
    <variable assignment statement>
        - target name is 'name'
        - value: <expression (string)> "world" <variable assignment statement>
        - target name is 'one'
        - value: <expression (int}> 1 <function invocation statement>
        - target function is 'say'
        - arguments:
            * <expression (string)> "Hello, ${name}!"
```

最后一步被称为*语义分析*，并且是完全开放的。您可以随意处理生成的 AST，但是在大多数情况下，我们遍历树并输出每个节点的表示。

简而言之，我们从输入代码->词法分析器->令牌流->解析器-> AST ->语义分析->输出。

ANTLR 在本教程中很重要，因为它自动为我们进行扫描和解析。可以想象，这极大地缩短了编写编译器(或任何支持语言的程序)所需的时间，因为我们只需要实现语义分析。

在本教程的第 2 部分中，我们将学习如何将 ANTLR 语法转换成我们代码中使用的词法分析器和语法分析器。敬请期待！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)