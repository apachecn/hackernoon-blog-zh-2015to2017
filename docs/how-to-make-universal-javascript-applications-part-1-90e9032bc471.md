# 如何制作通用 JavaScript 应用程序—第 1 部分

> 原文：<https://medium.com/hackernoon/how-to-make-universal-javascript-applications-part-1-90e9032bc471>

## 体验什么是可能的。

![](img/e2fc012e3a019bff1111be43f538fc39.png)

你可能听说过“服务器端渲染”或“渐进增强”这样的术语，甚至可能对它们感到兴奋。如果你在[反应](https://facebook.github.io/react/)场景中，尤其如此。否则，我强烈推荐[看看 StrongLoop](https://strongloop.com/strongblog/node-js-react-isomorphic-javascript-why-it-matters/) 的这篇好文章，让你跟上进度。

然而，如果你和我一样，在走上服务器端渲染的光辉道路后不久，你就碰了壁。事实证明，虽然 [JavaScript](https://hackernoon.com/tagged/javascript) 可以[通用](/@mjackson/universal-javascript-4761051b7ae9#.iddd7qgij)，但许多 JavaScript api 肯定不能。

让我向您展示我的意思——让我们比较一下 node 中的 http 请求和浏览器中的请求。

An example http request using node’s [http module](https://nodejs.org/api/http.html).

An example http request using [XMLHttpRequest](https://developer.mozilla.org/en-US/docs/Web/API/XMLHttpRequest/Using_XMLHttpRequest#Analyzing_and_manipulating_a_responseText_property_containing_an_HTML_document).

诸如此类的东西很快就会在通用应用程序中堆积起来，最终会让你陷入无论如何都要重复编写代码的境地。你甚至可能想回到 PHP 或 LanguageX+ Jquery。这在几年前可能是一个巨大的问题，但是由于社区的一些努力工作，这些 api 中的大部分已经被消除了。快进到今天，编写通用 JavaScript 变得比以往任何时候都容易。

让我们来看看一个现代的、通用的 http 库— [同构获取](https://github.com/matthew-andrews/isomorphic-fetch)

An example http request using [Isomorphic Fetch](https://github.com/matthew-andrews/isomorphic-fetch).

现在这很美好，一旦[异步函数](https://pouchdb.com/2015/03/05/taming-the-async-beast-with-es7.html)获得更多的市场份额，它会变得更好。将其与[同构表单数据](https://github.com/form-data/isomorphic-form-data)配对，您就成功了。不过最好的部分是，由于 package.json 中的[“browser”字段和现代 JavaScript 捆绑器，如](https://github.com/defunctzombie/package-browser-field-spec) [Browserify](http://browserify.org/) 、 [Webpack](https://webpack.github.io/) 和 [Rollup](https://rollupjs.org) ，这两种 API 可以在服务器或浏览器中无缝工作。甚至那些实现大相径庭的库，比如 HTTP 和 XHR，现在也可以通用了！

这种技术允许库作者为不同的环境(服务器/浏览器)构建完全不同的库实现，同时保持 api 基本不变。在这种情况下，它允许您在服务器中再次使用您在浏览器中了解到的关于 [fetch api](https://developer.mozilla.org/en/docs/Web/API/Fetch_API) 的所有内容。如今几乎任何东西都可以通用，[甚至数据库](https://pouchdb.com/)！这些库让我们更接近真正的通用 JavaScript，它不再局限于共享工具，如 [Lodash](https://lodash.com) 。你可以分享任何东西。

# 好吧，外面还有什么？

有很多很好的框架可以帮助你更进一步，但是 [React](https://facebook.github.io/react/) 是第一个，可能你已经看过了。React 的美妙之处在于，它将 HTML 抽象成 JSX，允许你根据环境以不同的方式渲染 JSX。在浏览器中，您希望更新现有的 dom，而在服务器中，您只想发送一个 html 字符串。幸运的是，有了 react，您可以用同一个 api 完成这两项任务。

看看上面的 JSX。请注意，不管是在服务器上还是在浏览器中呈现，第 1-10 行都是一样的；这太棒了！无论您多快注意到第 10 行之后的混乱——构建 DOM 的 API 是共享的，但渲染肯定不是。但是情况变得更糟了…在一起破解了上面的方法之后，你会意识到下一步通常也很糟糕——路由！

[同构](https://hackernoon.com/tagged/isomorphic)自 React 问世以来，路由实际上已经走过了漫长的道路。事实上，你只要看看[反应路由器](http://(And it doesn’t have to be difficult))本身就够了。如果你从未看过与 [react-router](http://(And it doesn’t have to be difficult)) 的同构路由，我推荐看看 Claudio Savino 的这篇优秀的[文章](/@foxhound87/server-side-rendering-with-react-router-we-must-react-ep-04-ad03b6b9e05d)。老实说，api 相当不错。但我知道我们可以更进一步。这篇多部分文章的计划是看一看一个框架，它去掉了所有神奇的非通用 API，如`match`和`RouterContext`，并且看一看在这个空间中有什么可能。

# 就这样开始了。

这一系列文章将向您展示当今通用 JavaScript 的确切潜力。我们将首先深入探讨路由，我将主要关注 [Rill](https://github.com/rill-js/rill) 和它的一些竞争对手。稍后我们会谈到今天正在做的更广泛的事情，包括数据库(离线应用非常酷！)，文件操作和大量的渐进增强。

最终，我相信通用 JavaScript 就是现在，我的目标是向你们展示我们已经走了多远。为了快速了解 [Rill](https://github.com/rill-js/rill) ，你可以看看[我的第一篇关于同构 JavaScript 的文章](/@pierceydylan/isomorphic-javascript-it-just-has-to-work-b9da5b0c8035)——还有更多！

*如果您对 Rill、同构/通用 JavaScript 或如何以这种方式构建真正的应用程序有任何疑问，请访问*[*gitter page*](https://gitter.im/rill-js/rill)*。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)