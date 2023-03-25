# 以打字打的文件

> 原文：<https://medium.com/hackernoon/the-first-typescript-demo-905ea095a70f>

上周是推出 [TypeScript](https://hackernoon.com/tagged/typescript) 的五周年纪念日，我有机会回去拜访 [Anders](http://twitter.com/ahejlsberg) 和团队，参加每周一次的 TypeScript [设计](https://hackernoon.com/tagged/design)会议——祝贺他们的里程碑，祝贺他们在我离开团队后的 4 年里继续用 TypeScript 取得的令人难以置信的进步。

我还利用这个机会翻出了一些我在我们从事 TypeScript(当时的代号为 Strada)工作的早期所做的 TypeScript 旧演示，与团队分享。

2010 年秋天，我和 Steve Lucco 一起开始了一个项目，帮助应用规模的开发团队管理大型 JavaScript 代码库。Steve 曾经和办公室的几个工程副总裁聊过，他们强调了他们的开发团队在投资于基于 web 的应用程序和体验时，从 C++/C#到 JavaScript 的快速转变。这些团队错过了 C++和 C#的类型系统和 Visual Studio IDE 提供的生产力工具和信心——他们想知道当他们转向 JavaScript 时我们能做些什么来帮助他们。与此同时，我们看到了更广泛的行业趋势，即快速的 JavaScript 引擎、HTML5 的进步以及几个令人印象深刻的大型 web“应用程序”都在快速改变 JavaScript 在过去几年中在 web 上的使用方式。

已经有很多选择，但似乎没有一个能引起足够广泛的市场共鸣。在微软内部， [Script#](https://github.com/nikhilk/scriptsharp) 被一些大型团队使用。这让他们可以直接使用 C#而不是 JavaScript，但结果是，当你试图与你真正要编程的运行时模型保持一定距离时，你会遇到阻抗不匹配的问题。还有[谷歌的 Closure 编译器](https://developers.google.com/closure/compiler/)，它提供了一个丰富的类型系统，嵌入在 JavaScript 代码的注释中，以指导一些高级的缩小过程(同时，捕捉并报告与类型相关的错误)。最后，这是 JavaScript 生态系统中 [CoffeeScript](http://coffeescript.org/) 迅速崛起的时间框架——成为第一个被大量使用的 trans pile-to-JavaScript 语言，并为 JavaScript 开发工作流中的 trans piler 铺平了道路。(*先不说*——我在早期经常用一个类比“coffee script:TypeScript::Ruby:c#/Java/c++”来解释 TypeScript，经常加上——“c#/Java/c++开发人员比 Ruby 开发人员多 50 倍:-)”)

我们很快发现我们想要提供的是这三者交汇处的“最好的”——一种尽可能接近 JavaScript 语义(如 CoffeeScript)和语法(如 Closure Compiler)但能够提供类型检查和丰富工具(如 Script#)的语言。

我们在 2010 年的秋天和冬天开始做这个兼职项目。作为帮助确保我们有东西展示的强制功能，我们在一个小型会议上与来自微软研究和产品团队的编程语言人员签约做了一个内部演示。然而，在演讲的前一个月，史蒂夫(当时该项目的唯一工程师！)手腕受伤，这使得他无法进行任何严肃的编码工作。因此，我不想退出这个话题——我决定拼凑一些东西，让我们能够理解我们试图提供的体验——即使没有实际工作的编译器。(史蒂夫在构建第一个真正的 TypeScript 编译器后不久就这样做了，这使得我们的第一个内部客户——构建后来成为 VS 代码的团队——开始真正使用 TypeScript)。

这是我们在 2011 年 2 月 1 日，在我们团队之外的第一次 Strada 演示中使用的主要代码。

这里有许多最终成为 TypeScript 的元素非常突出。

1.  第 28–30 行代码中的隐式类型流，不需要注释。
2.  类是能够同时表达类的实现和类型的重要方式——类在普通的 JavaScript 中很方便，但在 TypeScript 中更有价值，在 TypeScript 中，您希望同时描述实现和类型，并且不希望每件事都要说两遍。
3.  能够与普通的旧 JavaScript 代码和库无缝互操作——即使它们还没有相关的类型。

但是你也可以在这里看到一些我们最终从 TypeScript 中摆脱的东西。

1.  这里展示的更“函数式”的类语法是我们真正想要实现的——我刚刚完成了 F#的工作，它使用了类似的简化类语法，我们也在同一时间讨论了 C#的语法。它也非常适合当时流行的将 JavaScript“类”中的状态表示为闭包捕获的状态的风格——而不是像您在 ECMAScript 中标准化的基于原型的类方法中看到的那样表示为对象属性。在 TypeScript 公开发布之前的大部分时间里，这是一个有争议的话题 ECMAScript 类的进展工作正朝着一个不同的方向发展，尽管我们想努力与未来的标准保持一致，但我们并不喜欢在标准方法之上编写 TypeScript 类的方式(在常见情况下，变量的名称要说四次)。我们最终优先考虑标准一致性——这在当时是一个风险很大的赌注——事先并不清楚 ES6 最终会比 ES4(从未发布)更好——但回想起来，这是 TypeScript 承诺在 JavaScript 上保持尽可能薄的层的关键促成因素，即使它从 ES3 发展到 ES5，再到 ECMAScript2017 及更高版本。
2.  前缀位置的类型——您可以看到类型在上面被指定为`string name`,而不是现在 TypeScript 中的`name: string`。我们最初被这种语法所吸引，因为我们对现在被称为`any`的类型使用了名称`var`，允许对`any`的隐式类型化在类似于`var x = ...`的语句中有效地显式化。这也类似于不久前在 C#中引入`var`时所做的工作。但是将一个类型放在语法中的这个位置会导致许多解析问题，类似 JavaScript 的语言使用了`name: string`格式(最著名的是——action script 和不再使用的 ECMAScript4)。这个很早就改变了——几个月后我得到的代码样本已经使用了`:`语法。
3.  我们有 C#风格的`class Foo : Bar`语法用于超类——我们后来过渡到使用`extends`。
4.  稍微微妙一些——注意代码在`<script type="text/strada">`块中——注释指出`StradaCompiler.js`负责动态编译`text/strada`块。实际上，我们当时设想您将通过`<script>`标签在 HTML 中直接提供类型脚本代码——而不是有一个单独的构建步骤——并且它将在页面加载时被传输(并且希望被缓存)。这在当时对于小规模 CoffeeScript 项目来说是一种有点流行的技术，但是当我们与更多更大的开发团队交谈时，我们发现这对于许多/大多数生产工作流来说是不实际的。在实践中，获得这种紧密的开发工作流体验最终由 WebPack 和类似的运行观察器等工具解决，这些工具动态地重新编译代码，但仍然以编译后的形式呈现给浏览器。

但更有趣的是我们如何让这段代码实际工作。

当时我有一个小项目，用 F#编写一个 JavaScript 解释器(作为我从使用 F#过渡到使用 JavaScript 的一个宣泄工具)，其中一部分是一个相当可靠的 JavaScript (ES5)解析器。我对语法做了一些修改(令人惊讶地小),以支持我们在 Strada 中添加的新结构——主要是按照以下思路:

Aside — looks like we had `readonly` in this early version too.

我侵入了 pretty-printer，将生成的 ast 作为普通的 JavaScipt 发出——但是我没有丢弃类型，而是将它们转换成闭包编译器注释。现在，或多或少没有办法使这成为一个健壮的过程——但这对于演示来说已经足够了！

从那里——你猜对了——我们只是在结果代码上调用闭包编译器作为“类型检查器”。Closure Compiler 在 AppEngine 上有一个方便的托管服务(现在[仍然有](https://closure-compiler.appspot.com/home)),所以我们只是将漂亮打印的代码发布到那个服务上以获取错误。

这也是 Silverlight(和 Flash)仍然在客户端发挥重要作用的时代。所以为了运行解析器(在 F#中实现),我们将它作为 Silverlight 托管在页面中——它寻找`text/strada`脚本，解析它们，将它们发送到 Closure 并报告错误。

Still a little cringe-inducing seeing this code.

结果是，当我们在开发人员工具中改变了`<script>`标签的内容时，我们可以得到实时的错误报告，给人一种只有后台类型检查器在运行的印象。这成功地演示了我们试图实现的体验的一些关键部分——尽管在实践中，这种方式表明它不可能扩展到甚至单个真实用户😄。

这是我们演示的另一个代码片段。您可以从这里的 TypeScript 中看到一些更有趣的区别——将`double`作为一种类型而不是`number`(我们想象我们也可以添加`int`——但这使我们远离了 JavaScript 语义)和接口上的`I`前缀作为 C#启发的约定——以及一些保留下来的东西，如接口和无缝混合非类型化对象查找和强类型类的能力。

在第一次演示后一年半多一点，我们推出了 TypeScript。在此后的 5 年里，TypeScript 已经成为软件行业中发展最快的编程语言之一。

现在从外部来看，我一直对 TypeScript 团队的能力印象深刻，他们既坚持了作为 JavaScript 之上的一个薄层的根基，同时又不断发展以应对作为一个类型系统的挑战，该类型系统实际上可以在几乎任何 JavaScript 代码库上高效地使用。随着新的框架和开发风格在 JavaScript 生态系统中出现，TypeScript 也在设法发展，同时仍然保持专注和平易近人。

祝贺团队和 TypeScript 社区中的每一个人，他们做出了令人难以置信的努力，让这一切成为现实！

Our JSConf EU 2012 presentation launching TypeScript