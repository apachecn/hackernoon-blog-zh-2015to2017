# 用于本地包的 Node.js 纱线

> 原文：<https://medium.com/hackernoon/node-js-yarning-for-local-packages-9a7970edea7>

![](img/890b2568024741a6d3332fcdaa322c90.png)

这并不是对使用 Yarn 的 npm 包管理的又一次赞扬，而是一个使用本地开发的包的简明方法。

当您在本地开发机器上初始化 npm 模块时，它们就开始了它们的生命，但是当您想要测试它们或者简单地将它们与您拥有的其他 [Node.js](https://hackernoon.com/tagged/node-js) 项目一起使用时，就会出现这种情况。

使用 npm 客户端，我们将在文件系统中创建一个链接，但是使用 Yarn，您可以真正管理它们的版本，并像使用任何其他依赖关系一样使用它们，所有规则都应用于依赖关系。

# 文件系统上的模块:yarn add file://

使用以下命令从文件系统添加软件包:

```
yarn add file:/Users/lirantal/code/my-npm-module
```

这样做时，请记住:

*   因为 Yarn 认为这是一个真正的依赖，这意味着它将真正地安装到您的本地 *node_modules/* 目录，所以如果您对 npm 模块进行更改，它们将不会反映到您在 Node.js 项目中安装的版本上。
*   Yarn 喜欢缓存，它也缓存那些本地 npm 模块，您可以通过执行以下操作强制重新安装:

```
// Remove the package and clean local cache
yarn remove my-npm-mdule
yarn cache clean// Re-install it
yarn add file:/...
```

# Github / Git 上的模块

Yarn 带来的另一个便利是，您可以将 npm 模块推送到 Git 存储库，并告诉 Yarn 使用它。如果您不想将 npm 包提交给 npm 存储库，这很有用。

```
this is not specific to Yarn and the npm client can do the same
```

如果你在 GitHub 上有一个包，你可以告诉 Yarn 使用:

```
yarn add git+ssh://git@github.com/lirantal/my-npm-module.git
```

这里应用了与文件系统安装相同的缓存和版本控制。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)