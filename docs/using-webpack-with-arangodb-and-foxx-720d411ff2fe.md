# 将 Webpack 与 ArangoDB 和 Foxx 一起使用

> 原文：<https://medium.com/hackernoon/using-webpack-with-arangodb-and-foxx-720d411ff2fe>

我正在用 [Foxx](https://www.arangodb.com/why-arangodb/foxx/) 微服务框架和 [Vue](https://vuejs.org/) 构建一个项目。这非常令人兴奋，但是当涉及到工具时，我遇到了一些麻烦。我正在快速地进行更改，我必须调用 webpack 来捆绑我的前端文件，并调用 foxx-manager 来“升级”ArangoDB/Foxx 服务。另外，我很惊讶在 Foxx 中修改静态文件需要升级。我需要进一步挖掘。

几年前，当我迁移到一个更加以后端为中心的角色时，前端世界对任务运行者和编译器变得疯狂。这让我百思不得其解——因为我老了，我记得用解释语言写作的一个令人信服的理由是你不必等待编译器。哦，事实证明这不仅仅是一个短暂的时尚。大多数现代前端框架要求(或者强烈鼓励)使用某种工具。谢天谢地，后端 JS 世界和前端世界已经开始正常化，有了 webpack 这样的工具，你会看到两个世界正在融合。

但是首先，让我们回顾并描述一下 webpack。Webpack 与其他前端工具略有不同，它是一个模块捆绑器，而不是像 Gulp 或 Grunt 那样的任务运行器。它的任务是将模块(广泛使用的)打包成前端友好的资产。它如何通过管理和打包资产来使模块在前端变得易于消化。Webpack 本身在 [Node.js](https://hackernoon.com/tagged/node-js) 中作为 CLI 工具或者模块运行。Webpack 还有一个内置的开发 web 服务器和文件监视器。

我的第一个想法是，也许 Foxx 本身可以作为一个模块运行 webpack。毕竟设置脚本(*scripts . setup*in*manifest . JSON*)是做这种事情的完美地方。而且都是 [Javascript](https://hackernoon.com/tagged/javascript) 。不幸的是，结果并不理想。 *fs* 的 Foxx 实现缺少 *readFile* 函数，只有 *readFileSync* 。这是有意义的，因为 ArangoDB 同步实现 Javascript。我也许可以把大部分拼凑起来，但为什么要逆流而上呢？

既然阿兰戈不能运行 webpack，那么也许我可以让 webpack 运行 foxx-manager。在 webpack 中，您可以使用命令行运行一个简单的设置，或者创建一个 Javascript 模块来定义您的配置。我决定走那条路。然而，首先，让我们来看看 webpack。因为我们想要一个可重复的设置，所以让我们初始化一个 *package.json* 文件。

```
npm init
```

这样做似乎有点过分，因为 *package.json* 文件是一个明显的 node 文件，但是我们在这里也会使用一点 Node.js。

在您回答了 npm 初始化过程中的问题之后，我们可以添加一些开发依赖项。

```
npm install --save-dev webpack
npm install --save-dev webpack-shell-plugin
```

让我们构建我们的配置文件。习惯上把这个文件命名为 *webpack.config.js* ，尽管它可能是任何东西。我的文件是这样的:

浏览这个文件，让我们检查几个关键点。

1.  在第 2 行，我们使用了 [webpack-shell-plugin](https://github.com/1337programming/webpack-shell-plugin) 。这使得 webpack 可以作为一个基本的任务运行器。从命令行的角度来看，foxx-manager 非常简单，我们只需要运行一些东西——没有输入或输出。它应该符合我们的需要。
2.  *路径*模块用于平滑不同平台上的差异。除了 macOS 之外，我还没有在任何设备上测试过我的脚本，但是这可以确保我们通常使用正确的目录分隔符来构建路径。
3.  *module.exports* (第 6 行)的*条目*属性是我们将要运行 webpack 的单个文件。在这个文件中，你可以做一些很酷的事情，比如…

```
import joi from 'joi-browser'; 
```

…访问一个模块。Webpack 将为您管理依赖关系。

4.*模块的*输出*对象. exports* (第 7–9 行)定义了*条目*属性中提到的输出文件。

5.可能最有趣的部分是在第 12 行使用了 *WebpackShellPlugin* 。这就是我们要触发 foxx-manager 升级的地方。现在，在这个例子中，我已经放入了一个本地主机服务器的开发凭证。您可能希望包含一个来自 repo 外部的文件来管理用户名、密码、挂载点和数据库名称。我试图访问 webpack 的命令行参数，但没有骰子。我很想知道一种能够传入参数或 JSON 文件路径来管理这些凭证的解决方法。

现在，要运行它，您可以从项目目录中的命令行运行 webpack。

```
./node_modules/.bin/webpack --config webpack.config.js
```

这将首先打包你的 webpack 文件，然后运行 foxx-manager。如果你想变得更有趣，你可以添加 watch 命令行开关，当你改变 webpack 管理的文件时，webpack 和 foxx-manager 会自动运行。

```
./node_modules/.bin/webpack --config webpack.config.js --watch
```

这就是了。当你改变你的 Webpack 依赖关系时，webpack 将自动运行 foxx-manager。现在，这将只为 *module.exports.entry，*中的文件运行 foxx-manager，因此，如果您更改了另一个静态文件，您需要手动重新运行 foxx-manager。

另外，如果你对 ArangoDB 感兴趣，你可能也想看看《ArangoDB 和 Foxx 入门:Joifully CRUD-y in the best way》。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！