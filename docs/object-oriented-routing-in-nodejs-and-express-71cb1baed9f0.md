# 在 NodeJS 和 Express 中更好地处理路由器的简单方法

> 原文：<https://medium.com/hackernoon/object-oriented-routing-in-nodejs-and-express-71cb1baed9f0>

或者……如何把 OOP 酱放到你的功能后端

![](img/d376b7dba972968d719e27828ab88721.png)

当我需要为移动或 web 应用程序提供后端服务时，Node 是我的首选。虽然 JS 不是我最喜欢的语言，但后端开发的总体方法比使用其他工具[如](https://hackernoon.com/tagged/tools) [Java](https://hackernoon.com/tagged/java) EE 或 PHP 要好得多。
所以，当我还在等待 Swift 与官方[服务器 API 组](https://swift.org/server-apis/)一起进行服务器端开发的官方方法时(希望在今年晚些时候看到一些东西，也许是 WWDC？)，当我还在为一个新的副业项目编写后端服务时，我会尝试不同的模式。

在这篇短文中，我想与你分享我用来更好地处理服务路线的一个小模式。
这种模式背后的想法是将每个应用程序上下文(认证、用户配置文件管理和任何其他特定应用程序的功能)分组到不同的类中，这些类继承自一个基本的`Router`类。

此类公开一个构造，该构造将 Express 应用程序和公开路由服务的基本路径作为输入；还有这个名为`services`的阿哈类属性，我将返回这个特定路由公开的所有服务的列表。

让我带你看看基地的`Router`级:

很简单；我们简单地声明一个基本路由需要公开的所有服务的列表，然后分配一个需要调用的函数(以快速路由的形式，所以`function route(req, res, next) {}`)。

例如，我们的 Auth 子类可能是:

正如您可以看到的路径，每个路由路径的形式如下:

*   动词:是公开调用的 HTTP 方法(如果没有指定是`GET`，否则应该指定是不是`POST,PUT`等等)。)
*   路径:是否公开了服务的路径(以任何明确请求的经典形式)

而值是您应该在子类中实现的函数的名称。

最后，您可以按如下方式分配和放置您的路线:

我们的`AuthRouter`公开的每个服务都可以在/users/auth 路径下获得(即`http://server_url/users/auth/login/fb/xxxx`)。

这很容易实现，也是在节点后端进行排序的好方法。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)