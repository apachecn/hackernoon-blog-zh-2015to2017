# Angular2 是如何工作的？第 1 部分—应用参考

> 原文：<https://medium.com/hackernoon/a-deep-dive-into-angular2-part-1-application-refs-b15d62a86246>

# **前进**

我是一名 [Angular](https://hackernoon.com/tagged/angular) 1.x 开发者。我每天都在 Angular 1.x 中工作，我可以说我了解消化周期。对于 Angular 1.x，我采取了一种“边学边用”的方法，而对于 Angular 2，我会更加务实一些。

为了从 [javascript](https://hackernoon.com/tagged/javascript) 到屏幕上的 DOM 元素，我将尝试分离并记录 Angular 2 应用程序经历的每个步骤和部分。

# 设置

我的应用程序，被亲切地称为“客户端”，是从哪里开始的。我安装了 angular-cli 包并简单地调用了

```
> ng init
```

相当复杂的设置，实际上我真的很喜欢这个初学者应用程序。与耗时费力的手动设置相比，这是一种非常好的快速入门方式。

现在，大多数编译/打包步骤都包含在 angular-cli 附带的 blackbox webpack 系统中，所以我们将从 src/main.ts 开始。

main.ts 导入了一些 polyfills 和基本内容，typescript 编译器和 angular 需要这些内容才能在浏览器中正常运行。

现在让我们进入一些真正的肉。

# 就这样开始了……一路都是海龟。

```
platformBrowserDynamic().bootstrapModule(AppModule);
```

这到底是什么意思？让我们开始挖掘，首先我们进入[这里](https://github.com/angular/angular/blob/2b20db6c5acd60721e8a5dae2b50cdb1292f8db1/modules/%40angular/platform-browser-dynamic/src/platform-browser-dynamic.ts)，这是 platformBrowserDynamic 的声明。在里面，我们发现我们看到了

```
export const platformBrowserDynamic = createPlatformFactory(        
  platformCoreDynamic, 
  'browserDynamic',
  INTERNAL_BROWSER_DYNAMIC_PLATFORM_PROVIDERS
);
```

现在我们已经确定了有一种东西叫做平台(以及平台工厂)。)在这个调用之后，我们被重定向到一个名为 [application_ref.ts](https://github.com/angular/angular/blob/2b20db6c5acd60721e8a5dae2b50cdb1292f8db1/modules/%40angular/core/src/application_ref.ts#L84) 的文件。我们看到参数名依次是

*   parentPlaformFactory:(extra providers？:Provider[]) => PlatformRef
*   名称:字符串
*   提供者:提供者[] = []

这些名字似乎不言自明。首先要注意的是 INTERNAL _ BROWSER _ DYNAMIC _ PLATFORM _ PROVIDERS。你可以在这里找到所有提供[的东西](https://github.com/angular/angular/blob/979657989bab4fb93faf6a207d30760faa9bd696/modules/%40angular/platform-browser/src/private_export.ts#L18)，或者如果你只是想让我告诉你，它是做**浏览器特定渲染**所必需的所有模块。这基本上是这些调用的平台特定部分。其他的都是核心。

让我们深入研究第一个论点，“parentPlatformFactory”。就 starter 应用程序而言，我们可以看到这是 platformCoreDynamic，我们从[编译器模块](https://github.com/angular/angular/tree/2b20db6c5acd60721e8a5dae2b50cdb1292f8db1/modules/%40angular/compiler)获得。你可能会问，这里躺着什么？[更多平台工厂](https://github.com/angular/angular/blob/42a287fabf6b035d51e00cf3006c27fec00f054a/modules/%40angular/compiler/src/compiler.ts#L138)！一路上都是乌龟。

```
export const platformCoreDynamic = createPlatformFactory(
  platformCore, 
  'coreDynamic', 
  [{
    provide: COMPILER_OPTIONS, 
    useValue: {}, 
    multi: true
  }, {
    provide: CompilerFactory, 
    useClass: RuntimeCompilerFactory
  }, {
    provide: PLATFORM_INITIALIZER, 
    useValue: _initReflector, 
    multi: true
  }]);
```

因此，现在我们有了另一个基于“平台核心”的平台，我们将在稍后深入研究。请注意，看起来我们提供了很多与编译器相关的模块。这些都与获取角度代码和模板并将其编译成可渲染的组件有关。这里最值得注意的是，RuntimeCompilerFactory 是作为 CompilerFactory 提供的。这意味着我们正在进行 JIT 编译，而不是提前编译 angular 应用程序。

platformCore。你是什么？让我们看看。它来自角/核心，我们发现…

```
export const platformCore = createPlatformFactory(
  null, 
  'core', 
  _CORE_PLATFORM_PROVIDERS
);
```

不过，我们已经走到了尽头，爷爷的平台工厂。核心。它甚至有一个伟大的名字，它是核心。最后一个。中心。如果你看看什么是 _ CORE _ PLATFORM _ PROVIDERS，它们看起来是一堆非常普通的东西，反射器，控制台，通常是为任何应用程序提供基本功能的提供者。

太好了，让我们放松一下，回到我们最初对 createPlatformFactory 的调用，弄清楚这一切意味着什么。createPlatformFactory 的第一行如下所示；

```
const marker = new OpaqueToken(`Platform: ${name}`);
```

OpaqueToken 只是一个名称容器，基本上是 Angular2 版本的符号。到目前为止还不错。

```
return (extraProviders: Provider[] = []) => {    
  if (!getPlatform()) {      
    if (parentPlaformFactory) {        
      parentPlaformFactory(               
        providers.concat(extraProviders).concat({
          provide: marker, useValue: true
        }));      
    } else {                
      createPlatform(ReflectiveInjector.resolveAndCreate(            
        providers.concat(extraProviders).concat({
          provide: marker, useValue: true
        })));      
    }    
  }      return assertPlatform(marker);  
};
```

第一条语句 getPlatform()检查 createPlatform()是否被调用过，即是否有一个被分配的平台。我们第一次调用 createPlatform(回到核心)，我们根据创建的注入器创建一个平台。[我们再看看那个文件](https://github.com/angular/angular/blob/566d4361e22a3d8a4ce8619a762115abf97aaee2/modules/%40angular/core/src/platform_core_providers.ts#L20)。正如我们所看到的，PlatformRef 已经从 application_ref 文件中分配给了 PlatformRef。回到那里，[我们看到](https://github.com/angular/angular/blob/df4254ae89ccc6c3f358e4722ecaf8643cc320e2/modules/%40angular/core/src/application_ref.ts#L238)是抽象类[平台参考](https://github.com/angular/angular/blob/df4254ae89ccc6c3f358e4722ecaf8643cc320e2/modules/%40angular/core/src/application_ref.ts#L342)的实现。

> Angular 平台是网页上 Angular 的入口点。每个页面只有一个平台，页面上运行的每个 Angular 应用程序共有的服务(比如反射)都被绑定在它的范围内。* *当调用{@link bootstrap}()时，页面的平台被隐式初始化，或者*通过调用{@link createPlatform}()显式初始化。

我们终于实现了角度应用。在下一部分中，我们将研究当您调用 bootstrapModule 时实际发生了什么。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！