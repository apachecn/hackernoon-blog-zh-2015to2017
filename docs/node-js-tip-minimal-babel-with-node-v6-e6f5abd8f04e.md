# Node.js 提示-带有节点 v6 的最小巴别塔

> 原文：<https://medium.com/hackernoon/node-js-tip-minimal-babel-with-node-v6-e6f5abd8f04e>

![](img/7de24ac30cb4d6fc368f3496c6f867b0.png)

**TL；DR** 如果你碰巧使用的是[节点 v6 LTS](https://hackernoon.com/node-js-v6-transitions-to-lts-be7f18c17159#.hwnsy6yuh) 你应该考虑用 [babel-preset-node6](https://github.com/Salakar/babel-preset-node6) 替换 babel-preset-es2015，以最大限度地减少 *transpiler* 的工作量并更多地使用节点 v6 对 ES6/ES2015 的原生支持。

# ES 2015 和巴别塔

众所周知，JS 社区认为 [ES2015](http://www.ecma-international.org/ecma-262/6.0/) 是该语言自创建以来得到的最广泛的更新。它带来了人们期待已久的特性，比如作用于运行执行上下文的[变量、](http://www.ecma-international.org/ecma-262/6.0/#sec-let-and-const-declarations)[默认参数](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Functions/Default_parameters)和[承诺](http://www.ecma-international.org/ecma-262/6.0/#sec-promise-objects)等等。

[Babel](https://babeljs.io/) 使开发者能够在浏览器和 [Node.js](https://hackernoon.com/tagged/nodejs) 中使用所有很酷的 ES2015 更新，就好像规范已经得到 100%的支持一样。大致来说，他们是通过使用[预置](http://babeljs.io/docs/plugins/)将新 [JavaScript](https://hackernoon.com/tagged/javascript) 转换成旧 JavaScript 来实现的。

# 节点 v6 LTS

随着一年左右的时间过去，Node.js 更新逐渐增加了对 ES2015 功能的原生支持，Node v6 LTS 最近发布，它支持 ES2015 的 97%，正如你在这里看到的。

不幸的是，ES2015 [导入](http://www.ecma-international.org/ecma-262/6.0/#sec-imports)和[导出](http://www.ecma-international.org/ecma-262/6.0/#sec-exports)模块语法属于节点 v6 遗漏的特性。

因此，如果你想在 Node v6 中编写 100%符合 ES2015 的代码，你仍然需要 Babel。你不需要的是让 Babel 将你的代码转换成旧的 ES5，因此 [babel-preset-node6](https://github.com/Salakar/babel-preset-node6) 。

## **预设-es2015 vs** 预设-节点 6 **对比**

下面是使用这两种不同预设的变换结果的简单比较。

作为基线，使用 ES2015 的一些功能给出了基本的 [express](https://github.com/expressjs/express) 路线定义:

使用巴别塔官方预设-es2015 时，产生的代码将是:

```
//.babelrc
{
 “presets”: [“es2015”]
}
```

正如你所看到的，预置将代码转换成了旧的 ES5，而没有利用 Node v6 的任何原生支持。

当使用非官方的 babel-preset-node6 时，产生的代码将是:

```
// .babelrc
{
 “presets”: [“node6”]
}
```

这次生成的代码利用了 Node v6 本身支持的特性。只有节点 v6 不支持的部分像模块导入和导出一样被转换。

这个小样本可能看起来不够有意义，但在真正的生产规模的应用程序中，使用 V8 比围绕它工作更好。用插件创建者"*的话说，最终结果几乎总是更快的构建和脚本执行时间* " [李·本森](https://github.com/leebenson)。

**结论**

Babel 是一个令人惊叹的工具，它允许我们立即利用语言中的更新，但检查您实际需要的预置部分是有益的，您可能会避免不必要的构建时间，并利用 Node.js 原生实现的更多优势。

就我个人而言，在我的应用程序中，我将努力在我真正想要使用的新 JavaScript 特性和保持运行的最小巴别塔预设量之间取得平衡，因此有了最小巴别塔名称。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)