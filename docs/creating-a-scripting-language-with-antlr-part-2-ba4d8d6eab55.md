# 用 ANTLR 创建脚本语言—第 2 部分

> 原文：<https://medium.com/hackernoon/creating-a-scripting-language-with-antlr-part-2-ba4d8d6eab55>

在第一部分中，我们学习了编译器理论的基础，以及 ANTLR 在计算机语言识别中的作用。现在，我们将继续使用 ANTLR 在代码中构建 AST。

如果您还没有安装 ANTLR，请按照官方[入门页面](https://github.com/antlr/antlr4/blob/master/doc/getting-started.md)上的说明进行操作。

如果您不知道如何编写或阅读 ANTLR 4 语法，那么您可以通过非常详细的 ANTLR 4 文档来学习。信不信由你，这并不是关于 ANTLR 语法本身的教程。不管怎样，你可以在不真正了解 ANTLR 的情况下阅读本指南。

现在我们的系统上有了 ANTLR，我们可以设计我们的语言和编写我们的语法了。

我们将遵循第 1 部分中的示例，编写一个运行这种类型代码的语法:

```
set sky to "blue"
set roses to 3say "The sky is colored ${sky}."
say "I have ${roses} roses."
```

我们的语法文件可能如下所示:

```
grammar Foo;compilationUnit: stmt*;
stmt:
    assignStmt
    | invocationStmt
;
assignStmt: SET ID TO expr;
invocationStmt: name=ID ((expr COMMA)* expr)?;expr: ID | INT | STRING;COMMA: ',';
SAY: 'say';
SET: 'set';
TO: 'to';INT: [0-9]+;
STRING: '"' (~('\n' | '"'))* '"';
ID: [a-zA-Z_] [a-zA-Z0-9_]*;
```

然后我们可以简单地从命令行调用 ANTLR 来生成词法分析器和语法分析器。在最基本的情况下，我们只需要提供输入语法的名称，以及 *"-Dlanguage=JavaScript"* 来告诉 ANTLR 我们想要 JavaScript 输出。

```
antlr4 -Dlanguage=JavaScript Foo.g4
```

这将给你留下大约 5 个文件，包括 *FooLexer.js* 、 *FooParser.js* 和 *FooListener.js* 。

在代码中使用它非常容易。以下函数获取输入文本并返回生成的 [AST](https://hackernoon.com/tagged/ast) 。

```
var antlr4 = require('antlr4');
var FooLexer = require('./FooLexer').FooLexer;
var FooParser = require('./FooParser').FooParser;**function buildAst**(inputText) {
    **var** chars = **new** antlr4.InputStream(inputText);
    **var** lexer = **new** FooLexer(chars);
    **var** tokens = **new** antlr4.CommonTokenStream(lexer);
    **var** parser = **new** FooParser(tokens);
    parser.buildParseTrees = **true**; **return** parser.compilationUnit();
}module.exports = buildAst;
```

使用 ANTLR，创建一个词法分析器*和一个解析器*只需要几毫秒的时间。想象一下手写这些！

在第 3 部分的[中，我们将通过从我们的编译器中分离出](/@thosakwe/creating-a-scripting-language-with-antlr-part-2-e41fc68c60ba) [Javascript](https://hackernoon.com/tagged/javascript) 来结束一切。敬请期待！

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！