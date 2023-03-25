# FuseBox —在几分之一秒内打包您的项目

> 原文：<https://medium.com/hackernoon/fusebox-bundle-your-project-within-a-fraction-of-a-second-f2360ba95727>

![](img/c3bd584d87c67523306c8ac6a2a15b16.png)

FuseBox 是一个捆绑器/模块加载器，结合了 webpack、JSPM 和 SystemJS 的强大功能。重新捆绑一个繁重的项目需要 50-100 毫秒。

[Github 页面](https://github.com/fuse-box/fuse-box)

FuseBox 是一个年轻但非常雄心勃勃的项目。社区正在成长，我非常感谢所有让 FuseBox 更好、更快、更强的贡献者。

我将强调一些功能，剩下的就交给你了[这里](http://fuse-box.org/)

# 配置

FuseBox 的主要目标是尽可能避免配置。一切都应该开箱即用。与 WebPack 不同，您不需要担心您的项目是否会被捆绑。一定会的。您可以用这么多行代码捆绑像 babel-core 这样的怪物:

```
FuseBox.init({
    homeDir: "src",
    outFile: "_build/out.js"
}).bundle("> index.js")
```

生产力是关键，FuseBox 为您挑起重担

# 表演

FuseBox 非常快，它可以在不到一秒的时间内重新打包你的项目，还提供了一个全面的 API。处理 typescript(编译+捆绑)和 SASS 只需要 50-100ms

一个令人信服的基准是这里的。1200 个文件需要一次/保险丝盒 **0.234** s /网络包 **1.376** s

# 一流的类型脚本支持

Typescript 只适用于 FuseBox。

```
FuseBox.init({
    homeDir: "src",
    outFile: "_build/out.js"
}).bundle("> index.ts")
```

改变。js 到。ts 并开始使用 typescript。这里有一个顶级的 angular 2 TODO app充分展示了 FuseBox 的强大。现场演示可在[此处](https://fuse-box.github.io/angular2-example/)获得

# 加载器 API

这里有几个亮点:

*   使用[通配符](http://fuse-box.org/#wildcard-import)导入，
*   动态模块
*   [波浪号](http://fuse-box.org/#point-to-the-root)支架
*   [捆成一捆](http://fuse-box.org/#bundle-in-a-bundle)

# 插件

FuseBox 有大量的[插件](http://fuse-box.org/#built-in-plugins)可以帮助你入门。感谢我们的贡献者；-)，而且 API 使用起来绝对简单方便。

还有很多。[多得多](http://fuse-box.org/)

如果你喜欢这个项目，别忘了在 [github](https://github.com/fuse-box/fuse-box) 上开始！当然，点击♥来传播这个消息

加入我们的 [gitter](https://gitter.im/fusebox-bundler/Lobby) 频道，我们很活跃也很友好！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)