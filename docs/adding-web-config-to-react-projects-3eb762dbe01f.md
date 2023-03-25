# 向 React 项目添加 web.config

> 原文：<https://medium.com/hackernoon/adding-web-config-to-react-projects-3eb762dbe01f>

![](img/0260f306d4e7e98804bb3a9318a408ad.png)

Adding a web.config to deploy a react app to IIS

你有一个 react 项目，你想把它部署在一个运行 IIS 或任何其他 IIS 支持的网站的 Azure 应用服务上。您的 react 应用程序使用 react-router，并且在您的项目中有一些客户端 URL。

一切都很好，直到你想点击刷新按钮，这时你得到一个 HTTP 404。这很正常，因为在服务器上这些 urls 页面并不存在，它们只存在于客户端。当点击“刷新”时，浏览器会向服务器请求该页面，服务器会尝试定位该页面，因为那里什么也没有，所以会返回 404 响应。你开始阅读这个问题，你发现了关于 [URL 重写](https://www.iis.net/downloads/microsoft/url-rewrite)和导入 [URL 重写规则](https://docs.microsoft.com/en-us/iis/extensions/url-rewrite-module/creating-rewrite-rules-for-the-url-rewrite-module)的方法。

一步一步地，你会发现一个带有这种重写规则的 web.config 文件应该被部署到 Azure App 服务中。我将向您展示如何将 web.config 文件添加到 react 项目中的两种方法，以及您的文件如何成为 app bundle 的一部分。

这里有一个带有重写规则的 [web.config](https://gist.githubusercontent.com/lcostea/f17663ebf041b103d98989b6b52d8353/raw/6744846d241c9b785df9054fecbcfc4f2e5dda80/web.config) 文件的例子，你可以在你的项目中使用它。

# 第一种方式:web.config 是 src 的一部分

这是一个简单的解决方案，它包括将 web.config 文件与源文件并排添加，并设法使其成为捆绑包的一部分。它只适用于 webpack，如果你用 react-scripts 构建，你可以直接走第二条路。

1.  在 src 中添加 web.config。这是一个简单的步骤，只需将 web.config 复制到 src 文件夹中。
2.  现在，为了成为捆绑包的一部分，无论您是否使用 webpack 进行构建，都要在 index.js 中添加这样一行代码:

> 进口”。/web . config "；

这将标记该文件正在被使用的事实。

3.对于 webpack，我们需要告诉它如何处理 web.config 文件，因为如果此时运行，您将会得到类似的错误:

> 模块分析失败:…src\web.config 意外标记(1:0)

所以在你的 webpack.config.*里。js 在模块的 rules 部分添加下面一行，这将告诉 [webpack 文件加载器](https://github.com/webpack-contrib/file-loader)在输出中 web.config 应该被视为一个文件:

> {测试:/\。(config)$/，loader: 'file-loader？name=[name]。[ext]'}

# 第二种方式:额外的构建步骤

使用 react-scripts 时，您没有任何配置文件，因此我们无法使用文件加载器执行第三步。相反，这里我们将添加一个额外的构建步骤，这允许我们在代码、配置和部署之间进行更好的分离。第二种方法对 webpack 和 react-scripts 都有效。

1.  在项目根目录(不是 src 的一部分)中创建一个名为 iisConfig 的文件夹。在里面放入 web.config 文件和一个新文件，该文件将包含实际副本的代码。我们可以把这个新文件叫做 copyIISConfig.js，这里有一个要点。要点已经为 react-scripts 做好了，但是下面的第三步将告诉你如何为 webpack 修改它。
2.  在脚本部分内的 package.json 文件中，添加 postbuild 事件的附加构建步骤:

> " post build ":" node IIS config/copyiisconfig . js "，

3.这也可以用于 webpack，只需修改 copyIISConfig.js 文件中的 webConfigPath 变量，就可以将它复制到 dist 文件夹，而不是 build 文件夹。

现在只需执行 *npm run build* ，在输出文件夹(build 或 dist)中，您将拥有 web.config 文件。这使得这个包可以部署到 IIS 网站上。