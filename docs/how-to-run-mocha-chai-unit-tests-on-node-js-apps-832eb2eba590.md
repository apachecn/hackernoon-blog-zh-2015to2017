# 如何在 Node.js 应用程序上运行 Mocha/Chai 单元测试

> 原文：<https://medium.com/hackernoon/how-to-run-mocha-chai-unit-tests-on-node-js-apps-832eb2eba590>

![](img/7f2a3dda201cdd9cacda0f1e12cfd722.png)

# 用 Node.js 创建 Hello World 网站

我们将从创建一个简单的 [Node.js](https://hackernoon.com/tagged/nodejs) 应用程序开始，就像我们通常做的那样。本文的后续部分将解释如何用 BuddyWorks 编写、运行和自动化测试。

## 安装 Node.js

如果这是你第一次使用 Node.js，首先安装 npm 管理器:[nodejs.org/en/download/package-manager](https://nodejs.org/en/download/package-manager/)

## 安装 NPM 和摩卡

为应用程序创建一个目录:

```
mkdir myapp && cd myapp
```

现在，应该初始化 npm 了。我们将使用它来创建一个带有 [Mocha](https://hackernoon.com/tagged/mocha) 框架的`package.json`:

```
npm init
```

您将被要求提供应用程序的详细信息。键入以下内容:

*   名称:`hello-world`
*   切入点:`app.js`
*   测试命令:`./node_modules/.bin/mocha`这个框架将用于测试应用程序

按 enter 键确认其余的值。

## 用 Express 框架创建 Hello World

Express Node.js web 应用程序框架将用于构建应用程序:

```
npm install express --save
```

## Hello World 的详细信息

一旦你已经安装好了所有的东西，让我们用一个简单的 HTTP 服务器创建一个`app.js`文件，它将服务于我们的 Hello World 网站:

```
//Load express module with `require` directive
var express = require('express')
var app = express()//Define request response in root URL (/)
app.get('/', function (req, res) {
  res.send('Hello World')
})//Launch listening server on port 8080
app.listen(8080, function () {
  console.log('App listening on port 8080!')
})
```

## 运行应用程序

现在，应用程序可以启动了:

```
$ node app.js
```

在浏览器中点击`http://localhost:8080/`查看。

# 用 Mocha 和 Chai 配置单元测试

每个应用程序都应该在部署到服务器之前进行测试，尤其是当它是一个受欢迎的站点，给人留下第一印象的时候。这里，我们将使用 Mocha 作为测试运行框架，使用 Chai 作为断言库。

## 装摩卡和柴

确保将摩卡和柴包添加到`package.json`:

```
npm install mocha --save
npm install chai --save
```

## 添加测试文件

现在，是时候定义我们的第一个测试了…

# 喜欢你读的吗？[点击此处查看完整指南](https://buddy.works/guides/how-automate-nodejs-unit-tests-with-mocha-chai?utm_source=medium&utm_medium=post&utm_campaign=how-to-run-mocha-chai-unit-tests-on-node-js-apps&utm_content=link)！

![](img/237fecb458d889482966108f09dae68f.png)[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)