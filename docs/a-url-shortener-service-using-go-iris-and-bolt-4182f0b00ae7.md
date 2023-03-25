# 使用 Go、Iris 和 Bolt 的 URL 缩写服务

> 原文：<https://medium.com/hackernoon/a-url-shortener-service-using-go-iris-and-bolt-4182f0b00ae7>

如果你关注社交媒体网站，比如推特、谷歌和 T2，你会注意到我们没有使用完整的网址，而是使用了一个友好的缩写，比如 t.co/EvDSyOOPTH 的 T4。

在自己的域名中拥有自己的缩写网址不是很好吗？

我们先来复习一下基础。

# 工具

编程语言对我们来说只是工具，但我们需要一种安全、快速且“跨平台”的编程语言来为我们的服务提供动力。

[Go](https://golang.org/) 是一种发展迅速的[开源编程语言，旨在构建简单、快速、可靠的软件。看看](http://www.tiobe.com/tiobe-index/)[这里](https://github.com/golang/go/wiki/GoUsers)哪些伟大的公司使用 Go 来增强他们的服务。

## 安装 Go 编程语言

关于下载和安装 Go 的详细信息可以在[这里](https://golang.org/dl/)找到。

Maybe [Windows](https://www.youtube.com/watch?v=WT5mTznJBS0) or [Mac OS X](https://www.youtube.com/watch?v=5qI8z_lB5Lw) user?

> 这篇文章不包含对语言本身的介绍，如果你是一个新手，我建议你把这篇文章收藏起来，[学习语言的基础知识，以后再回来。](https://github.com/golang/go/wiki/Learn)

## 依赖关系

在过去，已经有很多文章引导用户不要使用 web 框架，因为它们“不好”。我必须告诉你，没有这样的事情，它总是取决于你将要使用的(web)框架。在生产层面，通常，我们没有时间为大公司的应用程序编写所有需要的代码。简而言之:**好的框架对任何开发者、公司或初创公司都是有用的工具，坏的是浪费时间，简单明了**。

您只需要两个必需的依赖项和一个可选的依赖项:

*   Iris ，我们的网络框架
*   [Bolt(现在的 bbolt)](https://github.com/etcd-io/bbolt) ，一个嵌入式的键/值数据库
*   [UUID](https://en.wikipedia.org/wiki/Universally_unique_identifier) 库，将帮助我们生成短网址

使用 go 软件包安装是一项简单的任务，只需打开您的终端并执行以下命令:

```
$ go get -u github.com/kataras/iris/v12@latest
$ go get -u github.com/etcd-io/bbolt
$ go get -u github.com/satori/go.uuid
```

# 最大的部分

很好，如果我们都在同一个页面上，那么是时候学习如何创建一个 URL Shortener server 了，它将易于部署并且可以扩展得更多！

为了创建一个短链接，我们生成一个随机字符串，并使用 Bolt 存储原始 URL，并将我们的随机字符串作为键。当一个“GET”请求被发送到缩短的 URL 时，我们从 Bolt 中检索原始的 URL。如果这样的值存在，我们重定向，否则我们用相应的消息响应。

> 为了简单起见，我们假设项目位于*$ GOPATH/src/you/shortener*目录下，包名为 *main* 。

我们的**前端**简单得可笑，它只包含一个索引页面及其“样式”，模板位于`./templates`文件夹，样式位于`./resources/css`文件夹。

./templates/index.html

./resources/css/style.css

直接转到**数据库**级别，我们将创建一个简单的实现，它将能够**保存**一个缩短的 URL(key)及其原始/完整的 URL(value)，**基于 key 检索**一个完整的 URL，并将所有注册的 URL 的总数返回给数据库。

让我们创建我们的**工厂**，它将**为我们生成**缩短的 URL！

我们应该创建一个**主文件**，它将连接并组合所有组件来运行一个 http 服务器，该服务器将为我们的小型 URL Shortener 服务提供服务。

还有…让我们构建并运行我们的应用程序吧！

```
$ cd $GOPATH/src/github.com/myname/url-shortener
$ go build # build 
$ ./url-shortener # run
```

# **奖金**部分

测试我们的应用程序总是一件有用的事情，因此我会给你一个如何用 Iris 测试这类网络应用程序的初步想法，直接看下面的代码。

./main_test.go

正在运行测试…

```
$ cd $GOPATH/src/github.com/myname/url-shortener
$ go test -v # test
```

# 就这样，很简单。对吗？

分享你对这篇文章的想法，让我知道你打算用 Go + Iris 构建什么样的应用程序！

感谢您花时间阅读整篇文章，我很佩服您的耐心:)

> 完整的源代码位于[这里](https://github.com/kataras/iris/tree/master/_examples/tutorial/url-shortener)。如果您有任何进一步的问题，请随时在下面留言或在这里提问。

## 上次更新时间

我在 2019 年 11 月 03 日**更新了源代码和文章的链接**，以便基于[https://github.com/etcd-io/bbolt/releases/tag/v1.3.1-etcd.7](https://github.com/etcd-io/bbolt/releases/tag/v1.3.1-etcd.7)(2018 年 8 月 29 日发布)和 Iris 导入路径基于在[https://github.com/kataras/iris/issues/1370](https://github.com/kataras/iris/issues/1370)的投票，用 CoreOS 的新[**【https://github.com/etcd-io/bbolt】**](https://github.com/etcd-io/bbolt)替换(存档的)【https://github.com/boltdb/bolt