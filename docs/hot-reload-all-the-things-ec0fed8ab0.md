# 热重装所有的东西！

> 原文：<https://medium.com/hackernoon/hot-reload-all-the-things-ec0fed8ab0>

## 如何使用 Webpack 实现后端和前端的热模块替换，以获得更高效的开发环境。

![](img/04ef230fcbfbb4ef2936d1e5bcfe581b.png)

# 第一部分:服务器

## 设置我们的 webpack 配置

首先，我们需要安装 Webpack 和其他一些依赖项；

```
yarn add webpack babel-loader babel-core babel-preset-env webpack-node-externals start-server-webpack-plugin
```

那就让我们创造我们的。babelrc

```
{
  "presets": [["env", {"modules": false}]]
}
```

现在我们将在 webpack.config.server.js 中为服务器设置我们的 webpack 配置；

```
const webpack = require('webpack')
const path = require('path')
const nodeExternals = require('webpack-node-externals')
const StartServerPlugin = require('start-server-webpack-plugin')module.exports = {
    entry: [
        'webpack/hot/poll?1000',
        './server/index'
    ],
    watch: true,
    target: 'node',
    externals: [nodeExternals({
        whitelist: ['webpack/hot/poll?1000']
    })],
    module: {
        rules: [{
            test: /\.js?$/,
            use: 'babel-loader',
            exclude: /node_modules/
        }]
    },
    plugins: [
        new StartServerPlugin('server.js'),
        new webpack.NamedModulesPlugin(),
        new webpack.HotModuleReplacementPlugin(),
        new webpack.NoEmitOnErrorsPlugin(),
        new webpack.DefinePlugin({
            "process.env": {
                "BUILD_TARGET": JSON.stringify('server')
            }
        }),
    ],
    output: {
        path: path.join(__dirname, '.build'),
        filename: 'server.js'
    }
}
```

现在，创建一个名为“server”的文件夹，里面有两个文件；index.js 和 server.js .
index . js 文件将作为我们的挂载点，server . js 将作为我们的实际应用程序。我们的项目结构应该是这样的:

```
server
  -- index.js
  -- server.js
.babelrc
webpack.config.server.babel.js
```

让我们安装并设置 express

```
yarn add express
```

然后我们在/server/server.js 中创建我们的服务器应用程序；

```
import express from 'express'const app = express()app.get('/api', (req, res) => {
    res.send({
      message: 'I am a server route and can also be hot reloaded!'
    })
})export default app
```

然后在/server/index.js 中，让我们添加魔法酱，让 HMR 使用我们的 Express 应用程序；

```
import http from 'http'
import app from './server'const server = http.createServer(app)
let currentApp = app
server.listen(3000)if (module.hot) {
 module.hot.accept('./server', () => {
  server.removeListener('request', currentApp)
  server.on('request', app)
  currentApp = app
 })
}
```

在我们的索引文件中，Webpack 正在轮询对 server.js 文件的更改。在对文件进行更改时，我们将监听器从 Express 重新连接到我们的导入。多亏了 Webpack 2，我们不需要重新要求我们的 server.js 文件，但是重要的是，我们接受为热模块替换而导入的相同文件。

让我们试一试。将它添加到我们的 package.json 脚本中；

```
"scripts": {
    "start:server": "rm -rf ./build && webpack --config webpack.config.server.js"
}
```

现在跑；

```
npm run start:server
```

打开浏览器，进入[http://localhost:3000/API](http://localhost:3000/api)。
你应该会看到一条信息“我是服务器路由，也可以热重装！”。然后尝试更改服务器文件中的消息并刷新页面。消息现在应该已经改变了。请注意，在您的终端中，服务器本身不会重启，但是 Webpack 会通过 HMR 更新模块。感谢 Webpack，我们现在正在热重装您的 Express 应用程序！

在下一部分中，我们将在服务器和客户端添加服务器端渲染的 React 和 HMR。

# 第二部分:在服务器和客户机上与 HMR 互动

## 在服务器上

让我们从向依赖项添加 React 开始；

```
yarn add react react-dom babel-preset-react
```

然后将反应预设添加到。babelrc

```
{
  "presets": [["env", {"modules": false}], "react"]
}
```

接下来，让我们创建一个文件夹来存放我们的组件，我们称之为“公共”。
在里面创建一个名为 App.js 的文件。
您的文件夹结构现在应该是这样的；

```
common
  -- App.js
server
  -- index.js
  -- server.js
.babelrc
webpack.config.server.babel.js
```

让我们在 App.js 中创建我们的 React 组件；

```
import React from 'react'const App = () => <div>Hello from React!</div>export default App
```

最后，让我们在服务器上呈现我们的组件，将/server/server.js 更改为:

```
import express from 'express'
import React from 'react'
import { renderToString } from 'react-dom/server'
import App from '../common/App'const app = express()app.get('/api', (req, res) => {
    res.send({
      message: 'I am a server route and can also be hot reloaded!'
    })
})app.get('*', (req,res) => {
    let application = renderToString(<App />) let html = `<!doctype html>
    <html class="no-js" lang="">
        <head>
            <meta charset="utf-8">
            <meta http-equiv="x-ua-compatible" content="ie=edge">
            <title>HMR all the things!</title>
            <meta name="description" content="">
            <meta name="viewport" 
            content="width=device-width,  initial-scale=1">
        </head>
        <body>
            <div id="root">${application}</div>
        </body>
    </html>`res.send(html)
})export default app
```

现在快跑

```
npm run start:server
```

然后转到 [http://localhost:3000/](http://localhost:3000/) 你会看到我们的服务器渲染 React 组件。尝试编辑/common/App.js 中的组件并刷新页面，它应该会更新。我们现在有服务器渲染反应热模块更换除了 HMR 对我们的常规服务器路线。

## 客户端反应——拼图的最后一块

让我们为我们的客户安装更多的依赖项；

```
yarn add webpack-dev-server react-hot-loader@next npm-run-all
```

首先，让我们在 webpack.config.client.js 中创建我们的客户端 webpack 配置；

```
const webpack = require('webpack')
const path = require('path')module.exports = {
 devtool: 'inline-source-map',
 entry: [
  'react-hot-loader/patch',
  'webpack-dev-server/client?[http://localhost:3001'](http://localhost:3001'),
  'webpack/hot/only-dev-server',
  './client/index'
 ],
 target: 'web',
 module: {
  rules: [{
   test: /\.js?$/,
   use: 'babel-loader',
   include: [
    path.join(__dirname, 'client'),
    path.join(__dirname, 'common')
   ]
  }]
 },
 plugins: [
  new webpack.NamedModulesPlugin(),
  new webpack.HotModuleReplacementPlugin(),
  new webpack.NoEmitOnErrorsPlugin(),
        new webpack.DefinePlugin({
            "process.env": {
                "BUILD_TARGET": JSON.stringify("client")
            }
        })
 ],
 devServer: {
  host: 'localhost',
  port: 3001,
  historyApiFallback: true,
  hot: true
 },
 output: {
  path: path.join(__dirname, '.build'),
  publicPath: '[http://localhost:3001/'](http://localhost:3001/'),
  filename: 'client.js'
 }
}
```

然后创建一个名为“client”的文件夹，里面有一个 index.js 文件；

```
import React from 'react'
import { render } from 'react-dom'
import { AppContainer } from 'react-hot-loader'
import App from '../common/App'render(<AppContainer>
 <App />
</AppContainer>, document.getElementById('root'))if (module.hot) {
    module.hot.accept('../common/App', () => {
        render(<AppContainer>
          <App />
        </AppContainer>, document.getElementById('root'))
    })
}
```

我们最终的文件夹结构应该是这样的；

```
client
  -- index.js
common
  -- App.js
server
  -- index.js
  -- server.js
.babelrc
webpack.config.server.babel.js
```

然后，让我们通过在 body 标签中添加

```
let html = `<!doctype html>
    <html class="no-js" lang="">
        <head>
            <meta charset="utf-8">
            <meta http-equiv="x-ua-compatible" content="ie=edge">
            <title>HMR all the things!</title>
            <meta name="description" content="">
            <meta name="viewport" content="width=device-width, initial-scale=1">
        </head>
        <body>
            <div id="root">${application}</div>
            <script src="[http://localhost:3001/client.js](http://localhost:3001/client.js)"></script>
        </body>
    </html>`
```

最后，将 package.json 中的脚本改为:

```
"scripts": {
    "start:server": "rm -rf ./build && webpack --config  webpack.config.server.js",
    "start:client": "webpack-dev-server --config webpack.config.client.js",
    "start": "rm -rf ./.build && npm-run-all --parallel start:server start:client"
  }
```

现在你可以跑了；

```
npm start
```

然后去 [http://localhost:3000](http://localhost:3000) 。我们现在知道 HMR 如何与我们的服务器端路由合作，以及服务器端和客户端的反应。

## 快速入门

在这里克隆一个简单的样本库；【https://github.com/mhaagens/hot-reload-all-the-things】T5


在 Twitter 上关注我，了解更多关于前端开发的信息；
[https://twitter.com/mhaagens](https://twitter.com/mhaagens)

## 承认

非常感谢 Sean T. Larkin、Tobias Koppers 和 Webpack 团队的其他成员所做的出色工作，感谢他们鼓舞人心并与社区分享他们的知识！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)