# 使用 webpack 开发同构应用程序

> 原文：<https://medium.com/hackernoon/developing-isomorphic-applications-using-webpack-eca814a418ad>

> **TL；dr；**我已经着手开发一个程序，能够使用 webpack 在服务器端呈现任何客户端代码。最终的结果是[https://github.com/gajus/isomorphic-webpack](https://github.com/gajus/isomorphic-webpack)前往“你好，世界”。

在过去的 6 个月里，我一直在做一个需要在服务器端提供内容的项目。这个未命名的项目是 Alexa 全球 100 强网站的一部分。网站本身广泛利用 [Edge Side Includes](https://en.wikipedia.org/wiki/Edge_Side_Includes) (ESI)来“在互联网的边缘”组装页面内容。服务于内容服务器端的需求来自于对 ESI 的利用。

这是一个独特的挑战:我在一个更大的前端团队中工作；代码将需要由前端团队维护。因此，我的重点是使用前端团队已知的框架，尽可能避免后端特定的框架。我已经开始使用 React 和 webpack 开发应用程序。

我已经启动并运行了应用程序，但是有一个问题——现有的同构渲染解决方案都没有现成的代码库。现有的解决方案需要模糊的配置，运行多个节点进程(使应用程序容器化变得很困难)，并且不能与所有的 webpack 加载器(例如`style-loader`)一起工作。

我已经着手开发一个程序来解决上述所有问题。我把它叫做 https://github.com/gajus/isomorphic-webpack。这篇文章的其余部分介绍了它的工作原理和使用方法。

# “你好，世界！”

让我们从“你好，世界”开始，并以此为基础。

我们的示例是一个 React 应用程序。它使用`react-dom`来呈现`ReactElement`，并将结果 DOM 添加到`#app`元素中。

`/src/app/index.js`

```
import React from 'react';
import ReactDOM from 'react-dom';const app = <div>Hello, World!</div>;ReactDOM.render(app, document.getElementById('app'));
```

`webpack`配置为使用`babel-loader`加载 JavaScript 文件。

`/src/webpack.config.js`

```
import path from 'path';export default {
  context: __dirname,
  entry: {
    app: [
      path.resolve(__dirname, './app')
    ]
  },
  module: {
    loaders: [
      {
        include: path.resolve(__dirname, './app'),
        loader: 'babel-loader',
        test: /\.js$/
      }
    ]
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, './dist')
  }
};
```

一个服务器端脚本使用`webpack`来编译应用程序，使用`express`来提供内容。

`/src/bin/server.js`

> 注意:`webpack-dev-middleware`不是`isomorphic-webpack`的依赖。这里它仅用于服务客户端应用程序。

```
import express from 'express';
import webpack from 'webpack';
import webpackDevMiddleware from 'webpack-dev-middleware';
import webpackConfiguration from '../webpack.configuration';const compiler = webpack(webpackConfiguration);const app = express();app.use(webpackDevMiddleware(compiler, {
  noInfo: false,
  publicPath: '/static',
  quiet: false,
  stats: 'minimal'
}));app.get('/', (req, res) => {
  res.send(`
  <!doctype html>
  <html>
    <head></head>
    <body>
      <div id='app'></div> <script src='/static/app.js'></script>
    </body>
  </html>
  `);
});app.listen(8000);
```

这不是同构的应用程序。发出 HTTP 请求只需用服务器端脚本中硬编码的字符串进行响应。

```
$ curl [http://127.0.0.1:8000](http://127.0.0.1:8000)<!doctype html>
<html>
  <head></head>
  <body>
    <div id='app'></div> <script src='/static/app.js'></script>
  </body>
</html>
```

同构应用程序将评估 React 应用程序代码，并使用呈现的应用程序进行响应。

如果您只想签出代码，请使用以下命令:

```
$ git clone [git@github.com](mailto:git@github.com):gajus/isomorphic-webpack-demo.git
$ cd isomorphic-webpack-demo
$ git reset --hard f66783c89040c0fc19a19df961cbb2633f27348d
$ npm install
$ npm start
```

# 同构“你好，世界！”

让上面的应用程序同构需要什么？

需要对我们的代码库进行以下更改:

1.  安装`isomorphic-webpack`
2.  使用 webpack 配置设置`isomorphic-webpack`
3.  将应用程序作为模块导出。
4.  使用`[react-dom/server](https://facebook.github.io/react/docs/react-dom-server.html)` `[renderToString](https://facebook.github.io/react/docs/react-dom-server.html#rendertostring)`渲染应用程序。

下面是它如何改变我们的示例应用程序:

`/src/app/index.js`需要导出应用程序:

```
import React from 'react';
import ReactDOM from 'react-dom';const app = <div>Hello, World!</div>;if (typeof ISOMORPHIC_WEBPACK === 'undefined') {
  ReactDOM.render(app, document.getElementById('app'));
}export default app;
```

> `[ISOMORPHIC_WEBPACK](https://github.com/gajus/isomorphic-webpack#how-to-differentiate-between-nodejs-and-browser-environment)`是用来区分 Node.js 和浏览器环境的常数。常数的存在表明它是 Node.js 环境。

服务器端脚本需要初始化`createIsomorphicWebpack`编译器，并使用`react-dom/server` `renderToString`来呈现应用程序的内容:

```
import express from 'express';
import webpack from 'webpack';
import webpackDevMiddleware from 'webpack-dev-middleware';
import {
  renderToString
} from 'react-dom/server';
import {
  createIsomorphicWebpack
} from 'isomorphic-webpack';
import webpackConfiguration from '../webpack.configuration';const compiler = webpack(webpackConfiguration);createIsomorphicWebpack(webpackConfiguration);const app = express();app.use(webpackDevMiddleware(compiler, {
  noInfo: false,
  publicPath: '/static',
  quiet: false,
  stats: 'minimal'
}));const renderFullPage = (body) => {
  return `
  <!doctype html>
  <html>
    <head></head>
    <body>
      <div id='app'>${body}</div> <script src='/static/app.js'></script>
    </body>
  </html>
  `;
};app.get('/', (req, res) => {
  const appBody = renderToString(require('../app').default); res
    .send(renderFullPage(appBody));
});app.listen(8000);
```

`createIsomorphicWebpack`覆盖 Node.js 模块解析系统，即所有引用属于`webpack`包的资源的`require()`调用将由`isomorphic-webpack`处理。

这使得我们的应用程序同构。发出一个 HTTP 请求，用呈现的 React 应用程序进行响应:

```
$ curl [http://127.0.0.1:8000/](http://127.0.0.1:8000/)<!doctype html>
<html>
  <head></head>
  <body>
    <div id='app'>
      <div data-reactroot="" data-reactid="1" data-react-checksum="1607472067">Hello, World!</div>
    </div><script src='/static/app.js'></script>
  </body>
</html>
```

![](img/b7e28c95a0e740446f1ac9a4d8480bfb.png)

Free isomorphism.

如果您只想签出代码，请使用以下命令:

```
$ git reset --hard 4fb6c11d488405a7c9b7f5a7cda4abec2396be00
$ npm install
$ npm start
```

# 使用 Webpack 加载器

> 加载器允许您在`require()`或“加载”文件时对其进行预处理。[..]loader 可以将文件从不同的语言转换成 JavaScript，或者将内嵌图像转换成数据 URL。加载器甚至允许你在你的 JavaScript 中做像 require() css 文件这样的事情！

–[https://webpack.github.io/docs/loaders.html](https://webpack.github.io/docs/loaders.html)

出于演示的目的，我将展示如何使用`style-loader`和`css-loader`。

首先，我们需要更新我们的 webpack 配置。

`/src/webpack.config.js`

```
import path from 'path';export default {
  context: __dirname,
  entry: {
    app: [
      path.resolve(__dirname, './app')
    ]
  },
  module: {
    loaders: [
      {
        include: path.resolve(__dirname, './app'),
        loader: 'babel-loader',
        test: /\.js$/
      },
      {
        loaders: [
          {
            loader: 'style-loader',
            query: {
              sourceMap: 1
            }
          },
          {
            loader: 'css-loader',
            query: {
              importLoaders: 1,
              localIdentName: '[path]___[name]___[local]',
              modules: 1
            }
          }
        ],
        test: /\.css$/
      }
    ]
  },
  output: {
    filename: '[name].js',
    path: path.resolve(__dirname, './dist')
  }
};
```

> 注:以上配置没有什么`isomorphic-webpack`具体的。我包括配置只是为了示例的完整性。

接下来，创建一个样式表。

`/src/app/style.css`

```
.greetings {
  color: #f00;
}
```

更新应用程序以使用样式表:

```
import React from 'react';
import ReactDOM from 'react-dom';
import style from './style.css';const app = <div className={style.greetings}>Hello, World!</div>;if (typeof ISOMORPHIC_WEBPACK === 'undefined') {
  ReactDOM.render(app, document.getElementById('app'));
}export default app;
```

最后，重启应用程序并发出 HTTP 请求。

```
$ curl [http://127.0.0.1:8000/](http://127.0.0.1:8000/)<!doctype html>
<html>
  <head></head>
  <body>
    <div id='app'>
      <div class="app-___style___greetings" data-reactroot="" data-reactid="1" data-react-checksum="72097819">Hello, World!</div>
    </div> <script src='/static/app.js'></script>
  </body>
</html>
```

如您所见，服务器用属性`class`的评估值`app-___style___greetings`进行响应。

如果你觉得在这一部分没有学到任何新东西，那是因为没有任何具体的东西。配置或应用没有`isomorphic-webpack`具体变化。这是一个真正通用的代码库。

![](img/970a8ed0f1c86ca6870a07c4d6a8411f.png)

You just won all the loaders!

如果您只想签出代码，请使用以下命令:

```
$ git reset --hard 90d6e2708719a1727f2f8afd06f8b47432707b88
$ npm install
$ npm start
```

# 路线

> 本节描述了一个实验性的实现。我没有在生产中测试过这个。小心行事。不过，还是挺酷的。

文档中已经包含了关于[服务器渲染](https://github.com/ReactTraining/react-router/blob/master/docs/guides/ServerRendering.md)的部分。您可以遵循这条路径……(但它需要编写服务器端特定的代码)或者您可以欺骗`react-router`认为脚本正在浏览器中运行，并避免对您的应用程序进行任何更改。

默认情况下，`createIsomorphicWebpack`不评估`node_modules`目录中的脚本。这样做是出于性能原因:很少有脚本依赖于浏览器环境。然而，`react-router` (和`history`)确实依赖于浏览器环境。

我将告诉`createIsomorphicWebpack`评估`react-router`(和`history`)，就像它在浏览器中运行一样。这是使用`nodeExternalsWhitelist`配置完成的。

```
createIsomorphicWebpack(webpackConfiguration, {
  nodeExternalsWhitelist: [
    /^react\-router/,
    /^history/
  ]
});
```

现在`react-router`和`history`包包含在 webpack 包中，并将使用仿浏览器环境执行。

然而，我们还没有完成。在评估代码时，我们需要知道什么是`window` URL。可以使用`evalCode`函数(`evalCode`是`createIsomorphicWebpack`结果的一个属性)来评估包代码，例如

```
const {
  evalCode
} = createIsomorphicWebpack(webpackConfiguration, {
  nodeExternalsWhitelist: [
    /^react\-router/,
    /^history/
  ]
});app.get('/*', (req, res) => {
  evalCode(req.protocol + '://' + req.get('host') + req.originalUrl);
  const appBody = renderToString(require('../app').default);
  res.send(renderFullPage(appBody));
});
```

现在，让我们提出一些要求:

```
$ curl [http://127.0.0.1:8000/hello-world](http://127.0.0.1:8000/hello-world)<!doctype html>
<html>
  <head></head>
  <body>
    <div id='app'><div class="app-___style___greetings" data-reactroot="" data-reactid="1" data-react-checksum="72097819">Hello, World!</div></div><script src='/static/app.js'></script>
  </body>
</html>$ curl [http://127.0.0.1:8000/hello-magic](http://127.0.0.1:8000/hello-magic)<!doctype html>
<html>
  <head></head>
  <body>
    <div id='app'><div data-reactroot="" data-reactid="1" data-react-checksum="1580012444">Hello, Magic!</div></div><script src='/static/app.js'></script>
  </body>
</html> 
```

![](img/2c066ccf74a8428d80886ce49337a10b.png)

It takes a magician to know a magician.

如果您只想签出代码，请使用以下命令:

```
$ git reset --hard 2959593fe217abada30d6ebe2c510e07a477c76b
$ npm install
$ npm start
```

# 结论

已经有很多文章讨论了服务器端渲染的利弊(例如[你忽略了服务器端渲染 JavaScript 应用](http://tomdale.net/2015/02/youre-missing-the-point-of-server-side-rendered-javascript-apps/))。在我的具体案例中，我需要服务器端渲染来支持边缘包含(ESI)。我通过首先编写客户端应用程序，然后使用[同构 webpack](https://github.com/gajus/isomorphic-webpack) 来呈现服务器端应用程序，实现了这一点。

评估服务器端呈现的利弊，如果利大于弊，那么考虑使用同构 webpack 使您的应用程序呈现服务器端。

# 接下来去哪里？

*   拉动[https://github.com/gajus/isomorphic-webpack-demo](https://github.com/gajus/isomorphic-webpack-demo)
*   阅读 `[isomorphic-webpack](https://github.com/gajus/isomorphic-webpack#isomorphic-webpack-faq)`的[常见问题解答。有一些有用的提示，比如当`isomorphic-webpack`没有完成编译时，如何停止 HTTP 请求处理。](https://github.com/gajus/isomorphic-webpack#isomorphic-webpack-faq)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)