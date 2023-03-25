# 银河前沿指南:第一卷和第二卷

> 原文：<https://medium.com/hackernoon/a-frontenders-guide-to-the-galaxy-pt-i-ii-3ae2f85d4a01>

*在过去的几个月里，我一直致力于为从过时的前端堆栈(ColdFusion)迁移到能再使用 3 到 5 年的产品奠定基础。在本文中，我将概述所做的选择以及它们如何解决(经典的)前端开发问题。它是技术性的，但它并不假定读者了解前端世界的最新发展。*

## 一.开店

[**【NodeJS】**](https://nodejs.org/):2017 年，任何一个涉足前端开发的人都应该做的第一件事，可能就是安装 NodeJS。NodeJS 是一个 [JavaScript](https://hackernoon.com/tagged/javascript) 运行时，它最初被设计用来交付快速和可伸缩的[网络](https://hackernoon.com/tagged/network)应用程序，但从我们的角度来看更重要的是，它已经发展成为今天前端开发中使用的许多工具的基础。

[**npm**](https://www.npmjs.com/) : NodeJS 自带一个包管理器， *npm* (节点包管理器的简称)，开发者可以在一个中央注册表中共享 JavaScript 模块/包。这个注册中心现在拥有超过 400，000 个独立的包，从像`left-pad`这样的小型实用模块，到像`angular`这样的框架，到……成熟的包管理器*，*像`bower`。您可以通过导航到您的项目根目录并运行`npm install package`来简单地安装一个依赖项。

除了聪明地使用他人的代码而不是通过`npm install`运行自己的代码之外，npm 还允许我们通过使用 JSON 文件来管理依赖关系，该文件包含您的应用程序所需的(版本)包，从而消除了在存储库中签入外部依赖关系的需要。*(你也可以为 npm 注册使用新的 Yarn 客户端，它更快更可预测)。*

[**Git Bash** 您需要一些更接近 Bash 所提供的东西。Windows 10 拥有令人难以置信的(嗯，对于 Windows 来说) *Bash on Ubuntu on Windows* ，但要启动并运行它还需要更多的工作。Git Bash 是一个轻量级的替代方案，它可以让您在 98%的情况下达到目标。](https://git-scm.com/downloads)

[**【Visual Studio Code**](https://code.visualstudio.com/):微软最新的代码编辑器功能强大:语法高亮、代码补全、代码片段、主题化、扩展等常见功能，还有一个集成的终端、出色的调试支持，以及对那些不使用命令行的人的 Git 的一流(好吧，也许是二流)支持。额外的好处:扩展是用 JavaScript 编写的(并通过 NodeJS 执行)，所以创建/贡献这些是轻而易举的事(我认为微软有扩展开发的愉快体验，所以试试吧！).*(提示:使用* [*这些快捷键*](https://gist.github.com/dgieselaar/ff74be3e02cf571f6b47a8c61157581f) *可以更快地切换标签)。*

## 二。脚手架

现在我们有了工具箱，我们需要有一个工作场所。我们需要建立一个构建系统，使我们能够:

*   开发和构建单页应用程序
*   开发和构建可嵌入单页应用程序和任何给定网站的独立组件
*   以最小的开销开发、构建和发布独立的包

**文件夹结构**:我们需要做的第一件事，是将软件包与我们将要部署的应用程序分开。`packages`是包所在的地方，`apps`是我们构建应用程序和组件的地方(为了简洁起见，我们称它们为应用程序)。

```
- apps
- packages
```

在应用程序中，我们定义了一个`package.json`文件，它包含了应用程序的依赖关系。我们假设所有应用程序的依赖关系大致相同，因此我们将共享它们，而不是为每个应用程序定义依赖关系。然后我们将在`apps`中创建两个文件夹。

```
apps *- entries* -- app
-- componentA
-- server
*- shared* -- util
package.json
```

`entries` 包含了我们单独应用的入口点，每个入口点一个文件夹。例如，我们可能有一个单页应用程序的入口点，或者 componentA 的入口点，或者服务器端呈现的应用程序 shell 的入口点。然后在`shared`中，我们有将在应用间共享的代码，例如认证服务。

[**Lerna**](https://lernajs.io/)**:**为了将分离的包链接在一起，并在我们的应用程序代码中使用，我们使用 Lerna。这是一个很棒的工具，它基本上在你的`node_modules`文件夹(你的 npm 安装的依赖项所在的地方)中创建了一个到定义的包的符号链接。这使得我们可以非常快速地开发，因为我们不必构建和发布每个更改——我们可以在应用程序代码中直接使用更改后的包。

[**Webpack**](https://webpack.js.org/) :为了构建我们的应用程序，我们使用 Webpack:一个功能强大得离谱的现代捆绑器。设置起来有点困难，但一旦一切就绪，这个不可思议的机器会为我们做些什么:

*   无缝导入和使用的不仅是 JavaScript 模块，还有 JSON、CSS、图片、字体，基本上你能想到的都有。
*   使用这些导入来构建依赖图，并且 a)只服务于我们实际使用的代码，导致更小的包和更好的性能，b)容易地找到和删除死代码，以保持我们的代码库干净。
*   启用一种开发模式，该模式侧重于快速重建，并在发生更改时自动刷新页面。这使得开发人员可以快速看到他们更改的结果(通常在 1 秒钟之内)，防止失去焦点，提高生产力。更神奇的是用 React 和 React Hot Loader 开发时的体验:你甚至不用刷新页面就能看到代码变化。更新的不仅仅是 CSS 甚至 JavaScript 函数也被替换为中的*。对任何开发者来说都是难以置信的体验。*
*   优化的生产版本具有更长的构建时间、更小的规模和更好的性能(例如，一些库删除了生产版本中的调试功能)。以生产为目标的包附带了源代码图，允许我们“美化”缩小的、不可读的代码来调试活的 bug。
*   允许开发人员定义“分割点”，将应用程序的一部分代码分割成一个单独的包，只在需要时才加载(例如，单独的视图)。这再次帮助我们保持最初的 JavaScript 包较小，这样页面可以启动得更快。
*   当我们的包超过 300KB 时，请出错，[这是 JavaScript 文件的推荐最大值](/webpack/webpack-performance-budgets-13d4880fbf6d#.n5x0l2c96)，确保我们继续关注性能。

[**Babel**](http://babeljs.io/):web 开发的一个问题一直是跨浏览器兼容性:我们只能使用目标浏览器支持的(新)JavaScript 特性。很难记住什么在什么浏览器中起作用，功能检测很难得到正确的结果，这使得大多数开发人员犯了谨慎的错误，并远离新的语言功能。这就是 Babel 的用武之地:一种将“现代”JavaScript 代码转换成每个浏览器都支持的子集的工具。再加上令人惊叹的`[babel-preset-env](http://babeljs.io/docs/plugins/preset-env/)`，我们可以简单地提供一个支持的浏览器列表，Babel 将会传送足够的文件以确保它在任何地方都能工作。这样我们就可以安全地使用一些好东西，比如箭头函数、对象析构、扩展操作符、rest 参数，以及更高级的东西，比如 async/await 和生成器。

[**post CSS**](https://github.com/postcss/postcss)**:**对于 CSS 和 JavaScript 来说也是一样:当你处于 CSS 规范的前沿时，跨浏览器兼容性很快就会成为一个问题。这就是为什么我们使用 PostCSS，一个针对 CSS 的编译器，它允许你定义一组用于转换源代码的插件。我们使用的一些插件是`[autoprefixer](https://github.com/postcss/autoprefixer)`，它自动将厂商前缀添加到需要它的属性中，或者是`[postcss-custom-properties](https://github.com/postcss/postcss-custom-properties)`，它允许我们使用 CSS 变量(目前，仅在编译时)，我们用它们来进行主题化。

[**流**](https://flowtype.org/)**:**JavaScript 的好处之一，它难以置信的动态性，在较大的代码库中会很快成为不利因素，越来越多地导致运行时错误。更严格的类型系统将允许我们在编译时而不是运行时捕捉错误。Flow 是一个静态类型检查器，由脸书开发，它允许您为变量声明和函数参数定义类型。如果这个变量没有被正确使用，Flow 可以警告您。例如，如果您将一个数字传递给一个需要字符串的函数，Flow 将会出错。但是 Flow 的伟大之处在于它为您完成了大部分工作——它遍历您的代码(和外部依赖项)并确定变量的类型*而无需定义它*。可能 Flow 唯一需要一点帮助的地方是来自后端的数据。这就是为什么我们在前端代码中添加了反映我们平台的对象模型的类型，使我们能够放心地访问和转换来自后端 API 的数据，并且能够在对象模型改变和我们更新类型时看到我们的代码将在哪里中断。我为 Visual Studio 代码使用了一个流扩展，它提供了出色的自动完成功能，并在编辑器中显示错误。

[](http://eslint.org/)**:最后，我们使用 ESLint 来检查代码中我们(或其他人)认为不好的模式。ESLint 是完全可配置的，允许您做任何事情，从强制单引号或双引号到函数括号中的一致间距。我还为 Visual Studio 代码使用了一个 ESLint 扩展，它可以内联显示错误，并在保存时修复“可修复的”规则(例如，[将行后大括号转换为行内大括号](http://eslint.org/docs/rules/brace-style))。此外，我有一个预提交 git 挂钩，每当我尝试提交新的更改时，它都会在我的代码上运行 ESLint。**

**请继续关注第三部分，在这一部分中，我将解释我们在应用程序中使用了哪些库，以及它们如何帮助我们更快、更可靠地发布。**

**同时，[在 Twitter 上关注我](https://twitter.com/GieselaarD)，或者阅读我以前的一些文章:**

**[](/@d.gieselaar/a-sense-of-speed-2042c2804f80) [## 速度感

### 提高单页应用程序的性能

medium.com](/@d.gieselaar/a-sense-of-speed-2042c2804f80)  [## 离线体验(或者说，告别强制性数据获取)

### 我们最近很高兴能够在我们的(Angular)网络应用 Zaaksysteem.nl 上从头开始

medium.com](/@d.gieselaar/the-offline-experience-or-saying-goodbye-to-imperative-data-fetching-9b2fa487eea7) [](/@d.gieselaar/structuring-a-modern-web-app-take-two-432b1e1ad2b7) [## 构建一个现代的网络应用程序，需要两个

### 在用一个非常基本的连接和缩小设置工作了几年后，它变得非常不舒服，因为我们…

medium.com](/@d.gieselaar/structuring-a-modern-web-app-take-two-432b1e1ad2b7)**