# React 教程:使用 webpack 设置 React 项目

> 原文：<https://medium.com/hackernoon/setting-up-a-react-project-from-scratch-6cb7cbb4da28>

使用 [webpack](https://hackernoon.com/tagged/webpack) 从头开始建立一个 [React](https://hackernoon.com/tagged/react) 项目需要很多部分。这是一个关于我如何设置我的最后一个项目的演练。

首先让我们初始化 npm。

您需要:

*   打开你的终端:在 Mac 上你可以用 Spotlight 搜索终端或者在`Applications -> Utilities -> Terminal`中找到它
*   使用命令行创建项目目录:`mkdir react-from-scratch`
*   导航到目录:`cd react-from-scratch`
*   运行`npm init`，npm 将引导您创建一个新的 npm 包
*   首先你会被提示输入一个包名:`package name: (react-from-scratch)`。这个项目将采用当前目录的名称，在这个例子中是`react-from-scratch`，如果你只是按下回车键。
*   还可以设置版本号(默认为`1.0.0`)、描述、入口点(默认为`index.js`)、测试命令、git 库、关键字、作者和许可证。我将坚持使用大多数默认值，但我添加了一个简短的描述和作者姓名。

一旦你完成了所有的选项，你将会得到一条`Is this ok? (yes)`消息的提示。只需按回车键确认，现在您就完成了对`package.json`文件的初始化！

```
// package.json{
  "name": "react-from-scratch",
  "version": "1.0.0",
  "description": "the demo",
  "main": "index.js",
  "scripts": {
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Vinny Martinez",
  "license": "ISC"
}
```

在项目中添加一个. gitignore 文件，这样 node_modules 目录就不会转到 GitHub。我只使用为 Node 创建的一个，可以在这里找到:[https://github . com/github/git ignore/blob/master/Node . git ignore](https://github.com/github/gitignore/blob/master/Node.gitignore)

将 webpack 安装到 npm 开发环境

```
npm install --save-dev webpack webpack-dev-server
```

创建网页包.配置. js

我在我的项目目录中创建了一个“app”目录和一个“dist”目录。我将 index.js 文件放在 app 目录中，并将一个空白的 bundle.js 文件放在 dist 目录中。我设置了如下所示的入口和输出。注意公共路径。我需要 publicPath 选项来启用我的 webpack-dev-server。

```
// webpack.config.jsvar path = require('path');module.exports = {
  entry: './app/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
    publicPath: "/dist/"
  }
};
```

将 babel 和用于 react 和 es6 的 babel 预设安装到 npm dev only 环境中

```
npm install --save-dev babel-core babel-loader babel-react
```

安装 React 和 ReactDOM

```
npm install --save react react-dom
```

然后用 babel-loader 设置 webpack.config.js 文件

```
var path = require('path')module.exports = {
  entry: './app/index.js',
  output: {
    filename: 'bundle.js',
    path: path.resolve(__dirname, 'dist'),
    publicPath: "/dist/"
  },
  // add the babel-loader and presets
 **module: {
    rules: [
      {
        test: /\.jsx?$/,
        exclude: /node_modules/,
        use: [{
          loader: "babel-loader",
          options: { presets: ['env', 'react']}
        }]
      }
    ]
  }**
  // end of babel-loader
}
```

现在我只需要在 package.json 文件中添加一个脚本来轻松启动我的 webpack-dev-server，这样我就可以编辑我的项目并立即得到结果。

```
// package.json{
  "name": "react-from-scratch",
  "version": "1.0.0",
  "description": "the demo",
  "main": "index.js",
  "scripts": {
    **"start": "webpack-dev-server --open",**
    "test": "echo \"Error: no test specified\" && exit 1"
  },
  "author": "Vinny Martinez",
  "license": "ISC"
}
```

这就是所有的 webpack 设置。

现在来设置目录。我做了一些看起来有点像这样的事情:

```
├── app
│   ├── components
│   │   ├── componentA.js
│   │   ├── componentB.js
│   │   ├── componentC.js
│   │   ├── componentD.js
│   │   └── componentE.js
│   └── index.js
├── dist
│   └── bundle.js
├── index.html
├── package.json
└── webpack.config.js
```

这是我的 index.html 档案:

```
<!DOCTYPE html>
<html lang="en">
  <head>
    <meta charset="utf-8">
    <title>My App</title>
  </head>
  <body>
    <div id="root"></div>
    <script src="dist/bundle.js"></script>
  </body>
</html>
```

这是我的 index.js 文件:

```
import React from 'react';
import ReactDOM from 'react-dom';
import ComponentA from './components/componentA'ReactDOM.render(
  <div>
    <ComponentA />
  </div>,
  document.getElementById('root')
)
```

现在我们可以创建一些组件:

组件 a:

```
// components/componentA.js
import React, { Component } from 'react'
import ComponentB from './componentB'
import ComponentC from './componentC'export default class ComponentA extends Component {
  render() {
    return(
      <div>
        <ComponentB />
        <ComponentC />
      </div>
    )
  }
}
```

构成部分 b:

```
// components/componentB.js
import React, { Component } from 'react'export default class ComponentB extends Component {
  render() {
    return(
      <div>
        <h1>Hello,</h1>
      </div>
    )
  }
}
```

组件 c:

```
// components/componentC.js
import React, { Component } from 'react'export default class ComponentC extends Component {
  render() {
    return(
      <div>
        <h1>World</h1>
      </div>
    )
  }
}
```

就是这样！享受 React 带来的乐趣！