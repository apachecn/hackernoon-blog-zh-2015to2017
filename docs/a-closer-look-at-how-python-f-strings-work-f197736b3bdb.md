# 仔细看看 Python f 字符串是如何工作的

> 原文：<https://medium.com/hackernoon/a-closer-look-at-how-python-f-strings-work-f197736b3bdb>

![](img/3e16b4e68e9aa10ed0ffe155481b260a.png)

Photo By [Fancycrave](https://www.pexels.com/photo/accessory-bobbin-close-up-clothing-355148/)

[PEP 498](https://www.python.org/dev/peps/pep-0498/) 引入了一种新的字符串格式化机制，被称为**文字字符串插值**，或者更普遍地被称为 **F 字符串(因为在字符串文字前面有一个前导的 F 字符)**。F-strings 提供了一种简洁而方便的方法来将 python 表达式嵌入到字符串文字中进行格式化:

我们可以在 f 字符串中执行函数:

f 弦很快！比 **%-formatting** 和 **str.format()** 快得多——这是两种最常用的字符串格式化机制:

为什么 f 弦这么快，它们实际上是如何工作的？ [PEP 498](https://www.python.org/dev/peps/pep-0498/) 提供了一个线索:

> string 提供了一种在字符串中嵌入表达式的方法，使用了最少的语法。应该注意，f 字符串实际上是一个在运行时计算的表达式，而不是一个常数值。在 Python 源代码中，f-string 是一个文字字符串，以' f '为前缀，在大括号内包含表达式。表达式被替换为它们的值。

这里的关键点是，**f 字符串实际上是一个在运行时计算的表达式，而不是一个常量值** *。*这实质上意味着 f 字符串中的表达式就像它们出现的范围内的任何其他 python 表达式一样被求值。CPython 编译器在解析阶段完成繁重的工作，将 f-string 分成字符串文字和表达式，以生成适当的 [**抽象语法树**](https://en.wikipedia.org/wiki/Abstract_syntax_tree) (AST):

我们使用 [ast](https://docs.python.org/3/library/ast.html) 模块来查看与 f 字符串内外的简单表达式`a + b`相关联的抽象语法树。我们可以看到 f 字符串`f{a + b}`中的表达式`a + b`被解析成一个普通的二元运算，就像它在 f 字符串之外一样。

我们甚至可以看到，在字节码级别，f-string 表达式就像任何其他 python 表达式一样被求值:

**add_two** 函数简单地将本地变量 **a** 和 **b** 相加并返回结果。函数 **add_two_fstring** 做同样的事情，但是加法发生在 f 字符串内。除了 **add_two_fstring 函数**的反汇编字节码中的 **FORMAT_VALUE** 指令之外(该指令的存在是因为 f-string 毕竟需要对包含的表达式的结果进行字符串化)，在 f-string 内部和外部计算`a + b`的字节码指令是相同的。

处理 f 字符串可以简单地分解为计算花括号内的表达式(就像任何其他 python 表达式一样),然后将它与 f 字符串的字符串文字部分结合起来，以返回最终字符串的值。不需要额外的运行时处理。这使得 f 弦非常快速有效。

为什么 **str.format()** 比 f 字符串慢很多？一旦我们使用 str.format()查看函数的反汇编字节代码，答案就变得很清楚了:

从被拆解的字节码中，立刻跳出两条字节码指令: **LOAD_ATTR** 和 **CALL_FUNCTION** 。当我们使用 **str.format()** 时，需要在全局范围内查找**格式**函数第一个。这是通过 **LOAD_ATTR** 字节码指令完成的。全局变量查找并不是一个真正廉价的操作，它包括许多步骤(如果你好奇，可以看看我之前的一篇关于属性查找如何工作的文章[。一旦定位到**格式**函数，调用二进制加法运算( **BINARY_ADD)** 对变量 **a** 和 **b.** 求和，最后通过 **CALL_FUNCTION** 字节码指令执行**格式**函数，返回字符串化的结果。python 中的函数调用并不便宜，而且开销相当大。当使用 **str.format()，**花费在 **LOAD_ATTR** 和 **CALL_FUNCTION** 上的额外时间是 str.format()比 f-strings 慢很多的原因。](/stepping-through-the-cpython-interpreter/how-does-attribute-access-work-d19371898fee)

那么 **%-string** 格式呢？我们看到这比 str.format()快，但仍然比 f-strings 慢。同样，让我们看看使用%字符串格式的函数的反汇编字节代码:

马上，我们看不到 **LOAD_ATTR** 和 **CALL_FUNCTION** 字节码指令——所以% string 格式避免了全局属性查找和 python 函数调用的开销。这解释了为什么它比 str.format()快。但是为什么% string 格式化还是比 f-string 慢？% string 格式可能花费额外时间的一个潜在地方是在 **BINARY_MODULO** 字节码指令**中。**我还没有对 **BINARY_MODULO** 字节码指令进行过全面的剖析，但是看一下 CPython 源代码，我们可以理解为什么调用 **BINARY_MODULO:** 会有一点点开销

从上面的 python C 源代码片段中，我们看到 **BINARY_MODULO** 操作被重载。每次被调用时，它都需要检查其操作数的类型(上面代码片段中的第 7 -13 行)，以确定操作数是否是字符串对象。如果是，则模运算符执行字符串格式化操作。否则，它计算通常的模(返回第一个参数除以第二个参数所得的余数)。虽然很小，但这种类型检查确实带来了 f 字符串可以避免的开销。

希望这篇文章有助于解释为什么 f-strings 在字符串格式化方面鹤立鸡群。f-string 速度快，使用简单，实用，代码更简洁。使用它们！