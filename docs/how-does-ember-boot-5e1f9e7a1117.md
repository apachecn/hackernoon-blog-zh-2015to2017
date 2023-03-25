# Ember 怎么开机？

> 原文：<https://medium.com/hackernoon/how-does-ember-boot-5e1f9e7a1117>

![](img/52c3a5e1d5cb22596453975295b035d3.png)

自诞生以来，Ember 已经从一个 [SproutCore 2.0](https://en.wikipedia.org/wiki/SproutCore) 框架发展成为一个现代可信的 Javascript 框架，被苹果、LinkedIn 和 Vine 等主要科技公司使用。它是由 Rails 开发人员 Yehuda Katz 开发的，所以它相信约定胜于配置。Ember 社区不仅非常支持，而且遵循六周发布周期，确保安全性和功能，使其成为最佳考虑框架。它试图尽可能地保持向后兼容，这样每一个新的主要版本你都不必浪费大量的时间去学习新的突破性的东西和语法。

我正在做我的 Ember 项目，它花了很多时间来启动，这不是一件平常的事情，在挖掘其原因之前，这个问题击中了我“Ember 是如何启动的？”。虽然这额外时间的原因是我的 API 上的一个调试器。知道 ember 是如何启动的很有启发性。

我做了一些研究，主要是查看 Ember git 资源库来寻找我的答案。了解余烬启动过程，不仅让我的根变得强壮，也改变了我看待它的方式。

涵盖整个 Ember 启动过程超出了任何文章的范围，要做到这一点，需要有一本书，但我可以明确地简要介绍对我有意义并且值得一提的事情。所以让我们开始…

当您启动任何 Ember 应用程序时，发生的第一件事就是创建`Ember.Application`实例，这是由

`window.App = Ember.Application.create();`

保存此实例的对象是一个全局对象，并且只创建一次。该类中的任何方法或变量都有一个全局名称空间`Ember.Application`。虽然您可以将`Ember.Application`视为保存应用程序中其他类的容器，但是还有其他一些职责，比如初始化、事件委托和路由。

创建实例时调用的第一个方法是`_bootSync()`。当 Ember 执行所需的所有资源都被下载并解析后，触发`domReady()`时调用该函数。

下面的代码列出了`Ember.Application`引导过程中涉及的所有方法。

```
Ember.Application{
  //other methods and variables
  ....
  _bootSync(){
    this.runInitializers();
    this.advanceReadiness(){
      this.didBecomeReady();
    }
  }
  ....
  //other methods and variables
  ....
  didBecomeReady(){
    if(this.autoboot){
    //instantiate Ember.ApplicationInstance
      let instance = this.buildInstance();
      instance._bootSync();
      intance.startRouting();
      //or instance.handleURL(url)
    }
  }
  ....
  //other methods and variables
}
```

`Ember.Application`有以下职责:

1.  **初始化**

初始化器的目标是注册依赖和注入。它们提供了对内部注册表的访问，内部注册表组织了 Ember 应用程序的不同组件。此外，它们还提供了访问实例化应用程序的机会。

它只运行一次。因为这些初始化器可能会加载代码，所以它们被允许推迟应用程序准备就绪时间并提前。当某个异步任务需要在某个初始化器之前执行时，可以使用`deferReadiness()`来控制`advanceReadiness()`，只有当`deferReadiness()`计数器设置为零时，才会调用`advanceReadiness()`。当我们调用`deferReadiness()`时，计数器被设置为递减，变成-1。路由停止，直到`deferReadiness()`计数器变为零。一旦异步任务执行完毕，使计数器增加 1，如果计数器=0，则调用`advanceReadiness()`。

可以在 Ember 上添加自定义初始化器，如下所示:

```
Ember.Application.initializer({ 
  name: ‘api-adapter’, 
  initialize: function(application) { 
    application.register(‘api-adapter:main’, ApiAdapter); 
  } 
});
```

2.**事件委托**

Ember 使用了一种叫做`_event delegation_`的技术。这允许框架建立一个全局的、共享的事件监听器，而不是要求每个视图手动完成。

例如，Ember 在`<body>`上设置了一个`mousedown` 监听器，而不是每个视图在其相关元素上注册自己的`mousedown`监听器。如果一个`mousedown`事件发生，Ember 将查看事件的目标并开始遍历 DOM 节点树，找到相应的视图并调用它们的`mouseDown`方法。

`Ember.Application`有许多它监听的默认事件，以及从小写事件到大小写视图方法名的映射。例如，`keypress`事件导致视图上的`keyPress`方法被调用，`dblclick`事件导致`doubleClick`被调用，等等。

如果有一个默认情况下 Ember 不监听的冒泡浏览器事件，您可以通过设置应用程序的`customEvents`属性来指定自定义事件及其相应的视图方法名称:

```
let App = Ember.Application.create({
    customEvents: {
      // add support for the paste event
      paste: 'paste'
    }
});
```

要防止 Ember 为默认事件设置侦听器，请在“customEvents”属性中用“null”值指定事件名称:

```
let App = Ember.Application.create({
    customEvents: {
      // prevent listeners for mouseenter/mouseleave events
      mouseenter: null,
      mouseleave: null
    }
});
```

默认情况下，应用程序在文档正文上设置这些事件侦听器。但是，在现有页面中嵌入 Ember 应用程序的情况下，您可能希望它在主体内的元素上设置侦听器。

例如，如果只有 ID 为`ember-app`的 DOM 元素中的事件应该被委托，那么设置应用程序的`rootElement`属性:

```
let App = Ember.Application.create({
    rootElement: '#ember-app'
});
```

`rootElement`可以是 DOM 元素，也可以是 jQuery 兼容的选择器字符串。`Ember.EventDispatcher`负责将事件委托给应用程序的视图。事件调度程序由应用程序在初始化时创建，并在应用程序的`rootElement`属性描述的 DOM 元素上设置事件侦听器。

注意，追加到根元素之外的 DOM 的*视图不会接收事件*。如果您指定了一个定制的根元素，请确保您只在其中添加了视图！

3.**路由**

`Ember.Application`不仅创建您的应用路由器，还控制路由。默认情况下，一旦浏览器发出`DOMContentReady` 事件，路由器将开始尝试将当前 URL 转换为应用程序状态。如果需要推迟路由，可以调用应用程序的`deferReadiness()`方法。一旦路由可以开始，调用`advanceReadiness()`方法。如果在路由开始之前需要任何设置，您可以在您的应用程序上实现一个`ready()`方法，该方法将在路由开始之前立即被调用。

这些是由`Ember.Application`初始化执行的主要任务。

一旦完成，我们的应用程序就处于`advanceReadiness()`状态，下一个被调用的方法是`didBecomeReady()`。这让我们想到了下一件重要的事情，那就是`Ember.ApplicationInstance`初始化。

当我们谈论`Ember.Application`初始化时，我们主要是处理 Ember 的注册，这发生在容器被创建之前。一旦完成，初始化`Ember.ApplicationInstance`。它封装了正在运行的`Application`的所有有状态方面。

在一个高层次上，我们将应用程序引导分为两个不同的阶段:
*定义时间，在此加载所有的类、模板和其他
依赖项(通常在浏览器中)。
*运行时，一旦所有的东西
都已加载，我们就开始执行应用程序。

**注册中心**类似于应用程序的类级(静态)成员，需要在实例创建之前进行处理。另一方面，单例驻留在**容器**中。Registry 用于保存无状态或静态部分，而 container 用于保存应用程序的有状态或动态部分。

请注意，每个应用程序实例都维护着自己的注册表/容器，因此默认情况下它们将完全独立运行。

现在我们知道了`_bootSync()`如何处理`this.didBecomeReady()`的时间。

```
Ember.Application{
  //other methods and variables
  ....
  didBecomeReady(){
    if(this.autoboot){
    //instantiate Ember.ApplicationInstance
      let instance = this.buildInstance();
      instance._bootSync();
      intance.startRouting();
      //or instance.handleURL(url)
    }
  }
  ....
  //other methods and variables
}
```

在`didBecomeReady()`，我们检查`this.autoboot`。它用于检查应用程序是否应该自动开始向 DOM ready 上的“rootElement”发送和呈现模板。虽然默认为 true，但其他环境(如快速启动或测试工具)可以将此属性设置为“false ”,并控制启动过程的精确计时和行为。

快速启动是一个特殊的环境，通常用于服务器端渲染。这种设置允许您使用 Node.js 在服务器环境中运行 Ember 应用程序，并将其内容呈现为静态 HTML 以用于 SEO 目的。

1.**自动引导**

当我们想在渲染过程开始前等待一些资源获取时,`this.autoboot`也很方便。正如`advanceReadiness()`用于控制路由，同样的方式`this.autoboot`用于控制渲染。

2.`**Ember.ApplicationInstance**` **初始化**

一旦`this.autoboot`被设置为真，接下来发生的事情就是`this.buildInstance()`。用于初始化`Ember.ApplicationInstance`。与在引导过程中仅运行一次的`Ember.Application`不同，`Ember.AppplicationInstance`被多次创建。正如我们有每次创建实例都会被调用的构造函数一样，`Ember.ApplicationInstance`也会被多次调用。它确保所有的注册表都已加载，所有的实例变量都已初始化。它们是根据每个请求创建和销毁的。因此，这是我们可能希望保存与我们的应用程序相关的敏感数据的地方，因为如果保存在注册表中，它可能会暴露出来，并且所有实例都可以不必要地访问它。

3.`**instance._bootSync()**`

就像它`Ember.Application`一样，我们有`_bootSync()`方法来设置`Ember.ApplicationInstance.`的实例，它有如下代码:

```
Ember.ApplicationInstance{
  //other methods and variables
  ....
  _bootSync(){
    this.setupRegistry();
    //define root element
    //define location
    this.runInstanceIntializers(){
      if(isInteractive){
        this.setupEventDispatcher();
      }
    }
  }
  ....
  //other methods and variables
  ....
  startRouting() {
    let initialURL = get(this, 'initialURL');
    if (this.setupRouter()) {
      if (initialURL === undefined) {
        initialURL = get(this, 'location').getURL();
      }
      let initialTransition = this.handleURL(initialURL);
    }
  }
  ....
  //other methods and variables
}
```

`this.setupRegistry()`确保实例所需的所有工厂都已正确注册。如果任何需要的工厂没有注册，那么它会在这个过程中注册。

`this.runInstanceInitializers()`用于定义根元素和实例将被呈现的位置。我们还可以指定监听什么类型的事件来检测导航。一旦知道了呈现元素和根元素的位置，就可以使用实例初始化器来初始化实例。

`isInteractive`是应用程序准备好监听**事件委托中提到的事件的阶段。**如果应用程序仍在获取一些资源，则其`isInteractive`被设置为假，因此没有事件调度程序在工作。当我们的应用程序准备好监听事件时，它的`isInteractive`被设置为 true，并且使用`setupEventDispatcher()`设置事件调度程序。它设置了全局事件调度程序，能够监听所有事件。

至此`_bootSycn()`完成，接下来发生的事情是路由。

4.`**startRouting()**`

在这里，URL 的路由开始。在此阶段，在`Ember.Application`初始化期间为路由设置的内容实际上在浏览器上可见。应用程序能够根据应用程序状态(router.js)更改 URL。

它初始化当前路由器实例，并设置由实例“位置”实现使用的改变处理事件监听器。名为`initialURL`的属性将用于确定初始 URL。如果找不到值，将使用`/'。在任何状态改变时，`get(this, ‘location’).getURL()`获取浏览器需要指向的新 url，而`this.handleURL(initialURL)`更新该 url。

这是所有的乡亲，希望这使了解余烬启动更好。

感谢阅读。

来源: [Ember github 页面](https://github.com/emberjs/ember.js/)和[穿越 Ember.js Glue 的旅程:由 Mike North 启动](https://www.youtube.com/watch?v=BEteW2srG0w)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)