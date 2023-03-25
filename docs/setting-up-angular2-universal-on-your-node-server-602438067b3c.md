# 在节点服务器上设置 angular2-universal

> 原文：<https://medium.com/hackernoon/setting-up-angular2-universal-on-your-node-server-602438067b3c>

截至本文撰写时，最新版本是 2.1.0-RC.1，其中引入了提前编译(AoT)支持。

## 什么是 angular2-universal？

首先，回顾一下 AngularJS 的历史。angular 的版本 1 与 [DOM](https://hackernoon.com/tagged/dom) 紧密耦合。这意味着您编写的任何应用程序都必须在浏览器或至少某种浏览器模拟器中运行。

无法在任何[环境下运行 angular](https://hackernoon.com/tagged/environment)是完全重写 angular 代码库的驱动力之一。他们想抽象出对 DOM 的访问，这样做，他们打开了在服务人员、节点服务器甚至. NET 服务器中运行 angular2 的可能性。枷锁已经被打破。

来自 [AngularClass](https://angularclass.com/) 的 [PatrickJS](https://medium.com/u/eeb0522094ff?source=post_page-----602438067b3c--------------------------------) 是一名非常出色的开发人员，他挺身而出，创建了[*angular2-universal*](https://github.com/angular/universal)，这是位于节点服务器和 angular 2 之间的中间件。

## 我为什么要在乎？

服务器端渲染的一个主要卖点就是它带来的 SEO 价值。尽管 googlebot 正在索引单页应用程序，所以它现在不太受关注，我有一个不同的[帖子关于那个](http://blog.inrhythm.com/the-current-state-of-angular-seo/)。除了 facebook 和 twitter 等社交媒体网站上的富媒体共享，还有其他搜索引擎可以考虑。

另一大卖点是增强的感知性能。由于服务器通过网络发送完全呈现的 html 页面，用户可以看到内容，并能够更快地与之交互。再加上静态页面的 CDN，原始服务器几乎不会受到影响，这降低了服务器的运行成本。

## 设置前端

angular2 有几个问题——当涉及到 angular2 时，这是通用的最佳实践。最主要的一点是，不要直接访问 DOM。在你的代码中，任何对*文档*或*窗口*的直接引用都会导致 universal 崩溃，或者顶多抛出错误。这两个对象在 node 上都不存在，所以不要试图使用它们。

然而…

有一些访问 DOM 的库，在某些情况下它们是不可避免的。一个非常流行的例子是 hammer.js，一个触摸事件库。

Angular2 在其核心用*hammersgetureplugin*支持这个库，本质上是 Angular 的事件绑定系统的扩展。包含 hammer.js 实际上不会崩溃任何东西，而是一旦你将一个触摸事件绑定到一个元素，它实际上就崩溃了。

```
<div (swipe)=”myAction()” /> // Bound touch event
```

*这是我和 PatrickJS* *做的非常粗糙的工作。不保证这将适用于 angular2 或 angular universal 的未来版本。*

```
import { __platform_browser_private__ } from ‘[@angular/platform-browser](http://twitter.com/angular/platform-browser)’;__platform_browser_private__.HammerGesturesPlugin.prototype.supports = function hackySupports(eventName: string): boolean {
  if (!this.isCustomEvent(eventName)) {
    return false;
  }
  return true;
};
```

如果你必须在你的代码中引用*文档*或*窗口*，你必须将代码包装在一个 *isBrowser* 条件中，这个条件可以方便地从 *angular2-universal* 中导入。

```
import { isBrowser } from ‘angular2-universal’;…if (isBrowser) {
 // Do the thing
}
```

或者，您可以在根级别提供 *isBrowser* ， *app.module* ，然后根据需要将其作为依赖项注入到您的组件、指令、管道和服务中。

```
// app.module.ts
import { NgModule } from ‘[@angular/core](http://twitter.com/angular/core)’;
import { isBrowser } from ‘angular2-universal’;[@NgModule](http://twitter.com/NgModule)({
  providers: [
    { provide: ‘isBrowser’, useValue: isBrowser }
  ]
})// my.component.ts
import { Component, Inject } from ‘[@angular/core](http://twitter.com/angular/core)’;[@Component](http://twitter.com/Component)({
  …
})
export class MyComponent {
  constructor(
    [@Inject](http://twitter.com/Inject)(‘isBrowser’) public isBrowser: boolean
  ) { }
} 
```

需要考虑的主要问题是 *angular2-universal* 有自己的模块 *UniversalModule* 。这需要在根级别导入，或者从 shared.module 导入然后导出。

从技术上讲，该模块有两个版本。一个从*angular 2-universal/browser*导入，一个从*angular 2-universal/node*导入。您必须分别创建两个不同的 *app.module* 条目文件 *app.module.browers.ts* 和 *app.module.node.ts* 。

```
// app.module.browers.ts
import { NgModule } from ‘[@angular/core](http://twitter.com/angular/core)’;
import { UniversalModule } from ‘angular2-universal/browser’;[@NgModule](http://twitter.com/NgModule)({
  import: [
    UniversalModule
  ]
})
```

除了将*angular 2-universal/browser*替换为*angular 2-universal/node*之外，文件节点版本完全相同。

*通用模块*已经导出 *JsonpModule* 、 *HttpModule* 和 *BrowserModule* 。所以这些可以安全地从 *app.module* 导入中移除。

## 设置后端

为了使 *angular2-universal* 与 express 一起工作，需要安装*angular 2-express-engine*包。这是位于 universal 和 express 之间的中间件。

不再需要为空的 *html* 文件提供服务的路由，可以从服务器配置中删除。其他途径，像资产一样，可以保持不变。这是服务器文件的基本框架，下面会详细解释。

```
 import { createEngine } from ‘angular2-express-engine’;
import { AppModule } from ‘./app/app.module.node’;// Express View
app.engine(‘.html’, createEngine({
  ngModule: AppModule,
  precompile: true
}));app.set(‘views’, ‘./dist/client’);
app.set(‘view engine’, ‘html’);app.use(‘/*’, (req, res) => {
  res.render(‘index’, {
    req,
    res,
    preboot: false,
    baseUrl: ‘/’,
    requestUrl: req.originalUrl
  });
}); 
```

这里有几件关键的事情。

首先需要设置渲染引擎，那就是 *app.engine* 部分。 *createEngine* 接受一个有两个键的对象， *ngModule* 和*预编译*。前者是 *app.module* 的节点版本，后者是一个标志，告诉 universal 应用程序是处于及时编译模式还是提前编译模式。将*预编译*设置为*真*意味着应用程序正在及时使用。

*应用程序使用*和 *'/*'* 作为第一个参数，以便 universal 引导和渲染应用程序的每个路径

然后，您需要确保 express 确实为您的所有路线提供了预先呈现的页面。确保将其添加到您在 express 中定义的任何其他路线的下方。如果你不这样做，这些路线将优先并覆盖其下的路线。

## 结论

这就包含了在 node/express 服务器上设置 universal 的基本内容。有几件事我没有在这篇文章中讨论，比如在服务器和客户端之间设置缓存。如果您的应用程序依赖于 xhr 请求，那么服务器和客户端都会在页面加载和刷新时发出这些请求。这可以通过缓存来自服务器的响应并在客户端读取它来避免。

另一个难点是用 universal 提前设置编译。这是一个完全不同的帖子，是我的 [Bee Todo 列表](https://medium.com/u/eb2da744f1c4?source=post_page-----602438067b3c--------------------------------)中的下一个

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！