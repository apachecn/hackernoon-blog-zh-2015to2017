# 为货币格式创建 Elm 本机模块

> 原文：<https://medium.com/hackernoon/creating-an-elm-native-module-for-currency-formatting-c9800e57a908>

埃尔姆的很多东西都很好，但偶尔你会发现一个缺口。对我来说，货币格式化就是这样一个缺口。如果你需要处理不同的地区和不同的货币，这就够烦人的了。Javascript 有一个完美的 api 来实现这一点，它就是 [toLocaleString](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Number/toLocaleString) 函数。

Elm 有一个未记录的特性叫做本地模块，允许您直接调用 javascript 函数。当然，这是一个坏主意，很容易重新引入您试图避免的所有类型混乱和运行时错误。但是在这种情况下，这似乎是合理的——为什么要重新发明一个完美的 api 呢？

我能找到的关于如何做这个的[信息有点过时了，这就是为什么我写了如何在 Elm 0.18 中做的原因。](https://github.com/eeue56/take-home/wiki/Writing-your-first-Elm-Native-module)

# 创建一个普通的 Elm 模块

第一步是创建一个普通的 Elm 模块来公开您的函数。

```
module Utils.CurrencyFormat exposing (format)--this is where we import the native module
import Native.CurrencyFormatformat : String -> String -> number -> String
format culture currency num =
    let
        res =
            Native.CurrencyFormat.format culture currency num
    in
        case res of
            Ok str ->
                str Err err ->
                let
                    _ =
                        Debug.log "CurrencyFormatError" err
                in
                toString num
```

# 创建您的本机 js 文件

接下来创建一个名为/src/Native/CurrencyFormat.js 的 js 文件。现在，再一次，这是*不安全*所以尽你所能使这个 js 函数防弹。

```
var _user$project$Native_CurrencyFormat = (function () { var format = function(cultureCode, currency, num) {
        try {
            var result = num.toLocaleString(cultureCode, {style: 'currency', currency: currency});
            return {
                ctor: "Ok",
                _0: result
            };
        } catch (e) {
            return {
                ctor: "Err",
                _0: e.message
            };
        }
    };
    return {
        format: F3(format)
    };
})();
```

这将向 Elm 公开这个函数，以便从我们在第一步中创建的模块中调用。注意一些事情:如果我们给它错误的输入，toLocaleString 可能会失败。为此，我们将调用包装在 try catch 中，并通过提供 Ok 或 Err 的构造函数将结果作为 Elm(结果字符串)返回。我们可以选择将结果传递给调用代码，或者在我们的实用函数中处理它，就像我已经做的那样，只记录它并返回一个普通的字符串数字。这取决于这个错误对你来说有多重要。

第二件要注意的事情是，我将返回的函数包装在对一个神秘的 F3 函数的调用中。我在这里猜测了一点，但是我很确定这确保了函数被适当地定制，这样部分应用程序将在 Elm 端按预期工作。

# 配置您的应用程序以允许本机模块

对您的 elm-package.json 文件做如下小小的修改

```
{
    "version": "1.0.0",
    "summary": "",
    "repository": "[https://github.com/user/project.git](https://github.com/user/project.git)",
    "license": "BSD3",
    "source-directories": [
        "./client/elm"
    ],
    **"native-modules": true,**
    "exposed-modules": [],
    "dependencies": {
        "elm-lang/core": "5.0.0 <= v < 6.0.0",
        "elm-lang/html": "2.0.0 <= v < 3.0.0",
        "elm-lang/http": "1.0.0 <= v < 2.0.0"
    },
    "elm-version": "0.18.0 <= v < 0.19.0"
}
```

# 像平常一样消耗你的功能

如果你做的一切都正确，你的函数应该像一个普通的 Elm 函数一样工作。它甚至会像任何其他 Elm 函数一样支持自动 currying。尝试一下…

```
toCurrency : number -> String
toCurrency =
    Currency.format "en" "GBP"
```

我认为在某些有限的情况下，这是一个很好的解决方案。但是请注意，这是一个没有文档记录的特性，它可以随时改变和被破坏，所以使用它要自担风险。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！