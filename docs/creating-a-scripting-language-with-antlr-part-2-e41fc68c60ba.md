# 用 ANTLR 创建脚本语言—第 3 部分

> 原文：<https://medium.com/hackernoon/creating-a-scripting-language-with-antlr-part-2-e41fc68c60ba>

在[第 2 部分](/@thosakwe/creating-a-scripting-language-with-antlr-part-2-ba4d8d6eab55)中，我们编写了一个语法，生成了一个词法分析器和解析器，还编写了一个简单的函数来从输入代码生成 AST。为了把所有的放在一起，我们需要走那条路。

我们的脚本语言支持两种类型的语句——变量赋值和函数调用。可以想象，变量赋值只是将变量的值设置为一个表达式。函数调用使用给定的参数调用给定的函数。如果函数的名字是“say”，那么我们改叫“alert”。

[ANTLR](https://hackernoon.com/tagged/antlr) 不仅仅生成词法分析器和语法分析器。它也产生听众和访问者。对于本教程，我们将使用一个监听器。侦听器遍历整个 AST，并在进入给定类型的规则上下文时触发各种事件。通过扩展 ANTLR 生成的默认监听器，我们只需编写代码来处理在编译过程中起作用的规则。

在这种情况下，我们的侦听器只需要处理赋值和调用，以及解析表达式的值。

```
function FooTranspiler {
    BaseListener.call(this);
    this.output = "";
}
```

首先，我们将填写 resolveExpr 方法。它将简单地把一个表达式上下文转换成一个字符串，该字符串包含该表达式的 Javascript 表示形式[。在大多数情况下，表达式将一对一地转换。](https://hackernoon.com/tagged/javascript)

您会注意到，为了访问表达式上下文中的子令牌和规则上下文，我调用了 ExprContext 对象的成员函数。除了命名的孩子，这些都应该通过函数访问。

```
function resolveExpr(ctx) {
    if (ctx.ID() != null) {
        // full code in attached gist...
    }
}
```

接下来，我们将实现赋值语句。这对于 ANTLR 来说非常简单，因为我们需要做的就是获得目标变量的文本，并将其设置为解析后的表达式值。

```
FooTranspiler.prototype.enterAssignStmt = function(ctx) {
    var target = ctx.ID().getText();
    var value = this.resolveExpr(ctx.expr());
    this.output += “var “ + target + “ = “ + value + “;”;
};
```

最后是调用语句。使用 ANTLR，我们甚至可以遍历输入代码中提供的每个表达式。

```
FooTranspiler.prototype.enterInvocationStmt = function(ctx) {
    var functionName = ctx.name.text;
    this.output += functionName + “(“;
    // full code in gist...
}
```

以下是完整的代码:

现在，我们可以把它们结合在一起。

```
var antlr4 = require('antlr4');
var buildAst = require('./build-ast');
var FooTranspiler = require('./foo-transpiler');function runScript(inputText) {
    var ast = buildAst(inputText);
    var transpiler = new FooTranspiler();      antlr4.tree.ParseTreeWalker.DEFAULT.walk(transpiler, ast);
    eval(transpiler.output);
}module.exports = runScript;
```

你可以在你的浏览器中运行它(在使用了类似于 [browserify](http://browserify.org/) 或 [webpack](https://webpack.github.io/) 的东西之后)，并且将你的代码编译成 Javascript 并运行。

恭喜你！您刚刚创建了一种脚本语言！然而，这仅仅是开始。高级别的项目，比如 Twitter 的搜索 API，使用 ANTLR。天空才是真正的极限。

如果你喜欢这个教程，请点击下面的链接推荐给朋友，或者在社交媒体上分享。

在推特上关注我: [@wapaa_](https://twitter.com/_wapaa_)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！