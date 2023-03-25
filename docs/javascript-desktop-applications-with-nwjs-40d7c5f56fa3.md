# JavaScript:带有 NWJS 的桌面应用程序

> 原文：<https://medium.com/hackernoon/javascript-desktop-applications-with-nwjs-40d7c5f56fa3>

对于 JavaScript 来说，这是激动人心的几年；新的设备和技术正在拓展我们对语言的认识。

很多年来，我的游乐场就是浏览器……它并不总是一个好的游乐场，到处都是 IE7 和他的哥哥 IE6 这样的恶霸，但随着智能手机和平板电脑的出现，事情开始发生变化，尽管是逐渐的。

过了一会儿，事情开始变得有趣起来。我们从[浏览器](https://hackernoon.com/tagged/browser)，到移动浏览器，到混合应用程序，到本地应用程序，到后端，到云协作工具，到机器人和桌面应用程序**…所有这些都使用了 JavaScript！**

![](img/2b916c3f67573aad0c5f3b05d53daad3.png)

但是我离题了，回到了手头的事情…在研究了 JavaScript 桌面应用程序的主题并浏览了一些框架之后，我偶然发现了[**nwjs . io**](http://nwjs.io/)**用他们自己的话说:**

> **让您直接从 DOM 调用所有的 [Node.js](https://nodejs.org/) [模块](https://www.npmjs.org/)，并启用一种使用所有 Web 技术编写应用程序的新方法。它以前被称为“node-webkit”项目。**

**NWJS 运行在 NodeJS 和 Chromium 上。我将花一些时间来解释如何设置它，创建一个构建过程，该过程将自动为所有支持的平台(Windows、Linux 和 Mac OS X-32 和 64 位)打包您的应用程序，并开始使用您喜欢的框架和库开发桌面应用程序。**

**请记住，在撰写本文时，NWJS 的稳定版本是 v0.13.2。我们还需要一些其他的东西:NodeJS 和 Gulp。**

1.  **下载并安装 [NodeJS](https://nodejs.org) 和 [NWJS](http://nwjs.io) (SDK 版本)**
2.  **一旦安装了 NodeJS，您还可以访问 NPM(节点包管理器)，这是一个与 NodeJS 捆绑在一起的命令行工具。通过在终端中运行 ***node -v*** 和 ***npm -v*** 来测试是否一切正常。**
3.  **如果你在装有 OSX 10.11 El Capitan 的 Mac 上，你可能会经历一种奇怪的行为，即**节点**工作，而 **npm** 不工作。阅读[这篇文章](https://coolestguidesontheplanet.com/installing-node-js-on-osx-10-10-yosemite/)……应该会有帮助。**
4.  **你可能已经注意到 NWJS 没有安装程序……没关系，只需将该文件解压到你选择的位置，并将其添加到你的系统路径中，这样你就可以全局访问 **nw** 二进制文件。要做到这一点，打开一个终端并运行以下命令之一(**确保你没有拼错，并输入 nwjs 文件夹的正确路径！**):**

```
**# windows**
set PATH=%PATH%;X:\path\to\nwjs\**# linux & mac**
PATH=$PATH:~/path/to/nwjs**# restart all terminals and test if it's working by running**
nw**# if it works, a dummy desktop app will start**
```

**至此，您已经完成了主设置。我们将使用 Gulp 来处理 CSS & JS 缩小/打包和分发等任务，因此打开一个终端并安装它(如果您还没有安装的话),运行:**

```
npm install -g gulp
```

**NWJS 与你用来创建桌面应用的框架和库无关，所以你可以选择任何你喜欢的。在这一点上，我想为所有不耐烦的读者提供一个到[nwjs-样板文件](https://github.com/raduGaspar/nwjs-boilerplate)的链接:**

**[](https://github.com/raduGaspar/nwjs-boilerplate) [## raduga spar/nwjs-样板

### nwjs 桌面应用程序的样板文件设置

github.com](https://github.com/raduGaspar/nwjs-boilerplate) 

样板文件非常简单。它有一些用于打包和构建应用程序的依赖项，以及一个加载一个 CSS 文件和一个 JS 文件的 HTML 文件(用作入口/起始点)。

JS 文件简单地说明了如何打开开发人员工具；除非您自己将该功能添加到应用程序中，否则 F12 在这里不起作用，Ctrl+Shift+I 也不起作用。

```
npm install -d # will install npm dependencies
nw . # will run the app
```

别忘了最后那个点，它告诉 ***nw*** 它运行 app 需要的 ***package.json*** 在当前文件夹里；如果你不添加它，你会得到一个默认的 ***nwjs*** 应用程序运行。

因此，让我们将样板文件分解成几个部分:

*   ***package . JSON***—这可以通过运行 ***npm init*** 生成，通常在使用 ***npm*** 的应用程序中可以找到。在 NWJS 中，它还有另一个功能:你用它来定义应用程序特定的参数，如:窗口大小、应用程序图标、透明度、工具栏、标题、位置和许多其他参数，以及应用程序入口/起始点……在我们的例子中，这将是***main:index.html***。
*   ***package.json*** 中的 ***devDependencies*** 是 ***gulpfile.js*** 中定义的任务所需要的。
*   ***gulpfile . js***—用于定义任务和自动化；在我们的例子中，我们使用 gulp 来读取***index.html***文件，并找到它加载的所有 CSS 和 JS 文件。然后，我们合并并缩小/美化所有的 CSS 和 JS 文件，生成一个新的**【index.html】**来加载生成的 CSS 和 JS 资产，创建一个 ***dist*** 文件夹来保存我们刚刚创建的所有文件以及任何 ***npm 依赖项*** (样板文件中没有)和原始的 ***package.json***
*   ***package . js***—使用 ***nw-builder*** (一个开发者依赖 npm 包)读取 ***dist*** 文件夹中的所有文件，并为我们在 ***平台*** 数组中定义的所有操作系统释放应用程序。如您所见，我们只针对 x64 平台，但是如果需要，您可以添加 x32。

请注意，在本文发表时，打包用 v0.13.2 构建的 NWJS 应用程序仍有 bug，有时无法工作(希望不会太久)。不过，它在 v0.12.3 中工作正常。您可以在 ***包中更改使用哪个版本。***

如果你仔细观察 ***package.json*** 我们在脚本里面有一个命令叫做 ***nwjs*** 它调用 ***gulp*** 和***node package . js***。这将创建一个 dist 文件夹并打包您的应用程序。从命令行运行它很简单:

```
npm run nwjs
``` 

**我也有一个实际的项目给你看，其中使用:NWJS (v0.12.x)，AngularJS (1.x)，SASS，指南针和 NEDB 创建一个管理电视节目的桌面应用程序。希望这能让你更好地理解 NWJS:**

**[](https://github.com/raduGaspar/nwjs-show-tracker) [## raduga spar/nwjs-展示-跟踪器

### 这是一个跟踪电视节目的简单界面。

github.com](https://github.com/raduGaspar/nwjs-show-tracker) 

为自己探索，寻找 nw-builder 的新版本，找点乐子，看看你能走多远。不要忘记你是在 Chromium 上开发的，所以没有必要担心跨浏览器的兼容性，如果它适合你，它也适合所有人。编码快乐！**