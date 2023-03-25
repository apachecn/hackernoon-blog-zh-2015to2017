# 2017 年 JavaScript vs. Python

> 原文：<https://medium.com/hackernoon/javascript-vs-python-in-2017-d31efbb641b4>

*我可能是你最不可能想到会写文章批评 JavaScript 的人之一，但现在我们来了。*

我最感兴趣的两个专业领域是 [JavaScript 和“大型编程”2013 年，我在 mloc.js](https://www.youtube.com/watch?v=M3uWx-fhjUc) 上做了一个[演讲，我在演讲中指出，在为大型软件项目选择语言时，静态类型是一个必不可少的特性。出于这个原因，](https://www.youtube.com/watch?v=PCL3dXQZ_Wk)[和其他原因](http://blog.bolinfest.com/2017/03/python-airing-of-grievances.html)，我一直将 Python 的使用限制在只有少量文件的小项目上。最近，我需要为工作构建一个可以说节俭的命令行工具。我最近一直很喜欢[核素](https://nuclide.io/) / [流](https://flowtype.org/) / [巴别塔](https://babeljs.io/) / [ESLint](http://eslint.org/) 工具链，所以我的第一反应是在这个新项目中也使用 [JavaScript](https://hackernoon.com/tagged/javascript) 。然而，很快就清楚了，如果我走那条路，我将不得不预先花很多时间让节俭绑定正常工作。我无法说服自己，我对 JavaScript 的个人偏好是否值得这样的投资，所以我决定看看 Python。我隐约意识到有人在努力增加对 Python 中静态类型的支持，所以我搜索了一下技术水平。原来是一个名为 [Mypy](http://mypy.readthedocs.io/en/latest/) 的工具，它提供了[渐进类型](https://en.wikipedia.org/wiki/Gradual_typing)，很像 [Flow](https://flowtype.org/) 对 JavaScript 的作用或者 [Hack](http://hacklang.org/) 对 PHP 的作用。幸运的是，Mypy 更像 Hack+HHVM，而不是 Flow，因为 Python 3 运行时本身接受类型注释，而在将代码传递给 JavaScript 运行时之前，Flow 类型注释必须由单独的工具剥离。(要在 Python 2 中使用 Mypy，必须将类型注释放在注释中，[按照 Google Closure toolchain](https://github.com/google/closure-compiler/wiki/Annotating-JavaScript-for-the-Closure-Compiler) 的风格操作。)虽然 Mypy 看起来没有 Flow 成熟(例如，对增量类型检查的支持仍在进行中)，但是能够简洁地记录类型信息就足以重新激起我对 Python 的兴趣。

在研究如何使用 Python 中的 Thrift 时，[谷歌](https://hackernoon.com/tagged/google)搜索发现了一些使用异步抽象的示例 [Python 代码。在逐步输入之后，`async` / `await`是 JavaScript 中另一个我离不开的特性，所以这个代码样本引起了我的注意！由于我们最近在工作中增加了对在 Python 3.6 中构建项目的支持，所以对我来说，使用 Python 中最新最棒的特性并运行起来是很容易的。(顺便说一下，我还了解到你真正想要的是 Python 3.6，而不是 3.5，因为 3.6 对 Mypy](https://github.com/facebook/fbthrift/tree/master/thrift/tutorial/py.asyncio) 有一些重要的[改进，对](http://mypy.readthedocs.io/en/latest/python36.html) `[asyncio](https://bugs.python.org/issue28613)` [API](https://bugs.python.org/issue28613) 、[的](https://www.python.org/dev/peps/pep-0498/)[修复，就像你在 ES6](https://bugs.python.org/issue28613) 、[和更多](https://docs.python.org/3/whatsnew/3.6.html)中所做的那样！)

来自[“现代”JavaScript](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f) 时代，一件特别令人耳目一新的事情是重新发现 Python 是一种开箱即用的编辑/刷新语言，而 JavaScript 曾经是这样，但现在不是了。让我解释一下我的意思:

*   在 Python 3.6 中，我可以在我的文本编辑器中创建新的`example.py`文件，编写使用`async` / `await`和类型注释的 Python 代码，切换到我的终端，运行`python example.py`来执行我的代码。
*   在 JavaScript 中，我可以在我的文本编辑器中创建新的`example.js`文件，编写使用`async` / `await`和类型注释的 JavaScript 代码，切换到我的终端，运行`node example.js`，看到它因为不理解我的类型注释而失败，运行`npm install -g babel-node`，运行`babel-node example.js`，看到它再次失败，因为我没有声明`babel-plugin-transform-flow-strip-types`插件的`.babelrc`，在我的机器上翻找，找到我在另一个项目中使用的`.babelrc`， 将那个`.babelrc`复制到我当前的目录，再次运行`babel-node example.js`，看着它失败，因为它不知道在哪里可以找到`babel-plugin-transform-flow-strip-types`，回到我取出`.babelrc`文件的目录，现在也复制它的`package.json`文件，从`package.json`中移除`example.js`不需要的垃圾，运行`npm install`，失去耐心，杀死`npm install`，运行`yarn install`，运行`babel-node example.js`来执行我的代码。 作为奖励，`babel-node example.js`运行起来比`node example.js`慢得多(去掉了类型注释)，因为我每次运行它时，它都会重新传输`example.js`。

事实上，JavaScript 生态系统提供了各种各样的工具来使用守护进程和缓存来加速这个过程，但是在任何人能够编写一行“现代”JavaScript 之前，您或您团队中的某个人必须投入相当多的时间来研究、组装和维护您项目的 JavaScript 工具链。尽管您最终可能会实现一个不错的编辑/刷新工作流，但您肯定不会像在 Python 中那样有现成的工作流。

> JavaScript 不再是一种编辑/刷新语言。

JavaScript 和 Python 之间另一个令人耳目一新的区别是 Python 的“内置电池”特性。如果你看看 JavaScript 附带的标准库，它是相当小的。节点环境在扩充标准库所提供的功能方面做了适度的工作，但是您需要的大部分功能不可避免地必须从 npm 中获取。具体来说，考虑 Python 标准库中包含的以下功能，但必须从 npm 中为节点项目提取这些功能:

*   [删除目录](https://www.npmjs.com/package/rimraf)
*   论点解析( [I](https://www.npmjs.com/package/commander) [不能](https://www.npmjs.com/package/yargs) [甚至……](https://www.npmjs.com/package/argparse))
*   [创建临时文件](https://www.npmjs.com/package/temp)
*   日志记录(有太多的 to 选项让我无法决定链接到哪个)
*   单元测试(再次，[太多](https://www.npmjs.com/package/mocha) [太多](https://www.npmjs.com/package/chai) [到](https://www.npmjs.com/package/jasmine) [列表](https://www.npmjs.com/package/jest))
*   [printf 格式字符串](https://www.npmjs.com/package/sprintf-js)
*   [左对齐一个字符串](https://www.npmjs.com/package/left-pad) ( [节点开发者仍然在做这个的噩梦](https://www.theregister.co.uk/2016/03/23/npm_left_pad_chaos/))

如您所见，对于其中的许多特性，有多个第三方库提供重叠的功能。(比如你在找一个 JSON 解析器，你会选择`parse-json`、`safe-json-parse`、`fast-json-parse`、`jsonparse`还是`json-parser`？)更糟糕的是，npm 模块名称是按照先来先服务的原则分配的。就像域名一样，这意味着伟大的名字经常出现在不值得拥有的项目中。(例如，从下载数量来看，[名为](https://www.npmjs.com/package/logging) `[logging](https://www.npmjs.com/package/logging)`的 npm 模块使其成为 JavaScript 最不受欢迎的日志包之一。)这使得第三方模块的比较更加费时，因为名字的质量不是库质量的有用信号。Python 的第三方生态系统可能和 npm 的一样糟糕。令人印象深刻的是*我不知道是不是这样，因为这种情况很少见，我不得不求助于第三方 Python 包来获得我需要的功能*。我知道数据科学家非常依赖像 NumPy 这样的第三方包，但与节点生态系统不同的是，有一个每个人都使用的*NumPy 包，而不是一系列名为`numpy-fast`、`numpy-plus`、`simple-numpy`等的竞争对手。*

> *“我们应该停止将 npm 视为 JavaScript 生态系统多样性的证明，而是将其视为 JavaScript 标准库的失败。”*

对我来说，所有这一切中最大的讽刺之一是，与其他编程语言相比，JavaScript 中强大的标准库的存在将是最大的优势。为什么？因为今天，每一个重要的网站都要求你下载下划线. js 或其作者选择用来弥补 JavaScript 薄弱核心的任何东西。当您考虑这对网络流量和页面加载时间的总体负面影响时，这些数字是惊人的。

# 那么…你是说 JavaScript 已经死了吗？

不，不，我没有。如果你正在使用 web 技术构建 UI(很多开发人员都是这样)，那么我仍然认为 JavaScript 是你最好的选择。模数 [Web Assembly](http://webassembly.org/) 的出现(值得关注)，JavaScript 仍然是唯一一种原生运行在浏览器中的语言。已经有很多尝试将现有的编程语言编译成 JavaScript，以避免“不得不”编写 JavaScript。有些情况下，结果是好的，但他们似乎从来没有伟大的。也许某些翻译工具链最终会成功地取代 JavaScript 作为 web 编写语言，但我怀疑在相当长的一段时间内，我们仍会有大多数 web 开发人员编写 JavaScript。

此外，浏览器并不是开发者使用 web 技术构建 UI 的唯一地方。另外两个突出的例子是[电子](https://electron.atom.io/)和[反应原生](https://facebook.github.io/react-native/)。Electron 之所以吸引人，是因为它让你可以为 Windows、Mac 和 Linux 编写一次代码，而 React Native 之所以吸引人，是因为它让你可以为 Android 和 iOS 编写一次代码。这两种工具都很强大，因为使用这些工具的编辑/刷新周期比它们的本地等价物要快得多。从招聘的角度来看，似乎懂 web 技术的开发人员(1)比本地开发人员数量更多，并且(2)比本地开发人员的团队更小，可以支持更多的平台。事实上，我可以设想修改这些平台的方法，以支持 Python 作为脚本语言，而不是 JavaScript，这可能会改变微积分。然而，JavaScript 社区中存在的所有疯狂工具都让位于 Babel 这样的 transpiler 工具链，它使普通开发人员(不必是编译器黑客)更容易试验新的 JavaScript 语法。特别是，这种工具为像 [JSX](https://facebook.github.io/react/docs/jsx-in-depth.html) 这样的东西铺平了道路，我认为这是使 React 成为构建 UI 的令人愉快的技术的关键特性之一。(注意，您可以在没有 JSX 的情况下使用 React，但这很繁琐。)

据我所知，Python 社区还没有一个对等的、流行的机制来在 Python 中试验 DSL。因此，尽管在这些现有环境中添加 Python 绑定可能很简单，但我不认为这足以让产品开发人员转向 Python，除非也对语言进行更改，使用 Python 表达 UI 代码像现在用 JavaScript+JSX 一样容易。

# 关键要点

Python 3.6 内置了对渐进类型和异步/等待的支持。与 JavaScript 不同，这意味着您可以编写使用这些语言特性的 Python 代码，并直接运行该文件，而无需任何额外的工具。此外，它丰富的标准库意味着您只需花很少的时间去搜索和评估第三方库来填补缺失的空白。它在很大程度上是一种“完成工作”的服务器端脚本语言，与 JavaScript 相比，它需要更少的脚手架来启动项目。尽管 Mypy 可能不像今天的 Flow 或 TypeScript 那样有特色或有表现力，但那个项目的速度肯定是我将要开始关注的事情。相比之下，我预计 JavaScript 将在产品开发人员中保持强势，但那些今天使用 Node 作为服务器端代码或命令行工具的人可能会更好地使用 Python。如果 Node 社区想要抵制这种变化，那么我认为他们将受益于(1)扩展 Node API 以使其更加全面，以及(2)减少 Node 的启动时间。如果他们可以修改他们的运行时来识别类型注释和 JSX 之类的东西，那就更好了，尽管这需要对 V8(或 Windows 上的 Chakra)进行更改，我预计这将很难维护和/或上游。让 TC39 标准化这些语言特性中的任何一个(这将迫使谷歌/微软在其 JS 运行时中加入原生支持)似乎也是一个艰难的推销。

总的来说，我很高兴看到这两个社区的事情如何发展。你永远不知道什么时候会有人发布一项新技术，一夜之间淘汰你的整个工具链。据我所知，我们可能明天醒来，都决定[我们应该写 OCaml](http://facebook.github.io/reason/) 。最好设置你的闹钟。

*本帖原载于*[*bolinfest.com*](http://blog.bolinfest.com/2017/03/javascript-vs-python-in-2017.html)*。*

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！