# 保险丝盒与动态模的史诗性

> 原文：<https://medium.com/hackernoon/fusebox-and-the-epicness-of-dynamic-modules-b179373b803e>

![](img/e09847eccfdd6dcc3f4ceda202ae2054.png)

如今对动态模块的需求非常大。[保险丝盒](https://github.com/fuse-box/fuse-box)为后者提供了巨大的支持，提供了无限的可能。

为了让你们马上感兴趣，我准备了一个整洁的[演示](https://fuse-box.github.io/dynamic-modules-playground/)，

在此之前，我先解释一下 [FuseBox](https://github.com/fuse-box/fuse-box) 的内部，以及为什么框架最擅长处理动态模块。

# 生态系统

[FuseBox](https://github.com/fuse-box/fuse-box) 为浏览器提供 100%兼容的 **commonjs** 生态系统。您捆绑的每个文件都可以通过 FuseBox API 访问。它不会以任何方式修改您的代码，因此可以在运行时进行试验、合并包并修改它们。

我们来看一个例子。打开这个 angular2 [todo-demo](https://fuse-box.github.io/angular2-example/) 。它由保险丝盒供电。打开一个开发者控制台，尝试导入这个[文件](https://github.com/fuse-box/angular2-example/blob/master/src/todo/todo-filter.pipe.ts)，如下所示:

```
FuseBox.import("./todo/todo-filter.pipe")
```

您将获得 *todo/todo-filter.pipe.* 的导出

现在想象一下这会如何改变你的编码方式。

与 WebPack 不同，FuseBox 创建虚拟文件:

```
_s.file("foo/bar.js", function(exports, require, module, __filename, __dirname){
});_s.file("foo/woo.js", function(exports, require, module, __filename, __dirname){
})
```

它保持了包中的原始结构。因此，融合 2 束将是无痛的。创建运行时插件也很有趣。

# 访问任何国家预防机制模块

现在，让我们回到[操场](https://fuse-box.github.io/dynamic-modules-playground/)

我特意包含了 NodeJS 的**路径模块，**，正如你所看到的，你可以导入并使用它，就像你在服务器上运行代码一样。

```
FuseBox.import("path")
```

现在是有趣的部分。动态模块尊重它们的位置。因此，一个`require`函数将相应地处理您的导入。

```
FuseBox.dynamic("foo/moduleB.js", "")
```

就好像你有一个文件夹“foo”里面有“moduleB.js”

现在来点魔法。让我们注册`index.js`

```
FuseBox.dynamic("foo/index.js", "module.exports = require('./*')")
```

在这个例子中，我们使用了一个[通配符导入](http://fuse-box.org/#wildcard-import)。好像我们没有必要。只是为了好玩。像这样要求文件夹:

```
FuseBox.import("./foo")
```

它将在那个文件夹中触发`index.js` ，就像在 NodeJS 中一样。

带保险丝盒的动态模块很有趣。你可以覆盖模块，删除它们，注册新的，创建虚拟包，还有更多等着你去发现。

了解[保险丝盒](https://github.com/fuse-box/fuse-box)！

[](https://hackernoon.com/fusebox-bundle-your-project-within-a-fraction-of-a-second-f2360ba95727) [## FuseBox —在几分之一秒内打包您的项目

### FuseBox 是一个捆绑器/模块加载器，结合了 webpack、JSPM 和 SystemJS 的强大功能。需要 50-100 毫秒来恢复…

hackernoon.com](https://hackernoon.com/fusebox-bundle-your-project-within-a-fraction-of-a-second-f2360ba95727) 

如果你喜欢这个项目，别忘了在 [github](https://github.com/fuse-box/fuse-box) 上给它起个名字！当然，点击♥来传播这个消息

加入我们的 [gitter](https://gitter.im/fusebox-bundler/Lobby) 频道，我们很活跃也很友好！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)