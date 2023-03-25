# 在 2 分钟内构建一个基于 CMS 的浏览器应用程序

> 原文：<https://medium.com/hackernoon/build-a-cms-powered-browser-app-in-2-minutes-7a0adbef36a9>

![](img/644619a13a4c2e9115269eef7778c74f.png)

在这个简短的教程中，我将向您展示使用 [Cosmic JS API](https://cosmicjs.com) 将 CMS 添加到一个简单的浏览器应用程序是多么容易。它将花费你 2 分钟来建造。我们的应用程序将只包含 3 个文件:

1.index.html
2。app.js
2。package.json

让我们开始吧。在您选择的终端中运行以下命令:

```
mkdir easy-browser-example
cd easy-browser-example
npm install cosmicjs
npm install browserify -g
```

现在让我们建立我们的 index.html 文件。在终端中运行以下命令:

```
vim index.html
```

将以下内容添加到我们的 index.html 文件中:

```
<!DOCTYPE html>
<html>
<head>
  <title>Cosmic JS Easy Browser Example</title>
</head>
<body>
  <h1 id="title">If you see this, something isn't working...</h1>
  <div id="content"></div>
  <div id="metafields"></div>
  <script src="app.browser.js"></script>
</body>
</html>
```

我们可以很容易地使用 jQuery 和 Ajax 来呈现我们的内容，但是对于这个例子，我们将使用[官方的 Cosmic JS Node.js 包](https://www.npmjs.com/package/cosmicjs)。

[](https://www.npmjs.com/package/cosmicjs) [## cosmicjs

### 宇宙 JS 的官方客户端模块。此模块帮助您轻松地将动态内容添加到您的网站或…

www.npmjs.com](https://www.npmjs.com/package/cosmicjs) 

现在创建一个名为 app.js 的文件:

```
vim app.js
```

并将以下内容添加到 app.js 中:

```
// app.js
var Cosmic = require('cosmicjs')
const bucket = { slug: 'easy-browser-example' }
const object = { slug: 'home' }
Cosmic.getObject({ bucket }, object, (err, res) => {
  var object = res.object
  document.getElementById('title').innerHTML = object.title
  document.getElementById('content').innerHTML = object.content
  document.getElementById('metafields').innerHTML = '<pre>' + JSON.stringify(object.metafields, null, 2) + '</pre>'
})
```

注意，在我们的 app.js 文件中，我们从 [Cosmic JS API](https://cosmicjs.com) 返回内容，然后将我们的内容附加到“title”、“content”和“metafields”处的 DOM 元素。

接下来，我们将添加一个 package.json 文件，这将允许我们添加一些简单的脚本来“浏览”我们的 app.js 文件:

```
vim package.json
```

将以下内容添加到名为 package.json 的新文件中:

```
{
  "name": "easy-browser-example",
  "main": "app.js",
  "scripts": {
    "browserify": "browserify app.js -o app.browser.js"
  }
}
```

现在，让我们运行脚本，将代码捆绑到浏览器中。运行以下脚本，将新文件绑定到 app.browser.js:

```
npm run browserify
```

现在在您的浏览器中查看 index.html 文件，您将看到我们的示例存储桶“easy-browser-example”中的内容，并且元字段数据被呈现为一个字符串，向您显示哪些数据是可用的。更进一步，你可以看到如果你把[反应](https://cosmicjs.com/knowledge-base/react-cms)或[角度](https://cosmicjs.com/knowledge-base/angularjs-cms)加入到组合中，这将是多么强大。

我希望你喜欢这个简短的教程。如果你还没有，你可以[注册一个宇宙 JS 账户](https://cosmicjs.com/signup)，并使用你自己桶中的内容开始玩这个例子。Cosmic JS 提供了一个直观的 API，可以向任何网站或应用程序提供内容。这使您可以自由地使用任何编程语言来构建应用程序，并允许在您的开发团队中更容易地扩展。从阅读文档开始。

这篇文章由托尼·斯皮罗撰写，最初发表在宇宙 JS 博客 T4 的 T2 上。

# 推荐阅读:

[](https://hackernoon.com/how-to-build-a-simple-blog-using-react-and-graphql-e32d78bbd724) [## 如何使用 React 和 GraphQL 构建一个简单的博客

### 在本教程中，我将向你展示如何使用 React、GraphQL 和 Cosmic JS 创建一个简单的博客。这将会…

hackernoon.com](https://hackernoon.com/how-to-build-a-simple-blog-using-react-and-graphql-e32d78bbd724)