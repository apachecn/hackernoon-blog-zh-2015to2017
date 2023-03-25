# [Webpack] —为生产智能加载资产

> 原文：<https://medium.com/hackernoon/webpack-smart-loading-assets-for-production-3571e0a29c2e>

# **优化**

最近，当我们检查我们的 [javascript](https://hackernoon.com/tagged/javascript) 错误日志时，我们注意到有几个问题与“旧浏览器”有关，比如

```
Uncaught ReferenceError: Promise is not defined
Uncaught ReferenceError: fetch is not defined
```

经过讨论，我们有两种方法:

1.  当用户前往使用“[现代](https://hackernoon.com/tagged/modern)功能的特定路线时。我们检查浏览器是否支持这个特性。如果没有，我们将懒惰加载它。
2.  将 all-in-one polyfill(我们选择`babel-polyfill`)添加到我们的 js 资产中，并在加载任何 js 资产之前先加载它。

这种方法似乎给我们的项目增加了更多的复杂性，我们必须知道“旧浏览器”不支持哪种功能，并且在检查每条路线的功能时要重复。一件更重要的事情是我们当前的代码库，我们必须以这种方式重构工作。因此，我们倾向于方法二。方法二的问题是，即使在“现代浏览器”的情况下，我们也必须加载 polyfill。因此，优化它的方法是跳过 polyfill，我们通过这样的延迟加载来实现:

> `*old_browser*` *取决于你使用的功能。在我们的例子中，它们是*`*Promise*`*`*fetch*`*

*目前，我们使用 webpack 来构建我们的资产。有了上面这样的输出，我们必须在 webpack 中定制构建过程(在本例中是`html-webpack-plugin`插件)，基本上，代码片段如下所示:*

*我提取上面的代码片段并在这里创建一个包[https://github.com/MQuy/html-webpack-condition-assets](https://github.com/MQuy/html-webpack-condition-assets)*

*在加载 js 资产方面，这种方法不如上面的方法好，为了改善这一点，我们可以使用新功能`preload`。在构建过程中，我们通过 [preload-webpack-plugin](https://github.com/GoogleChrome/preload-webpack-plugin) 添加格式为`<link red="preload" ...>`的 js 资产*

*最后，模板将如下所示:*

*关于 webpack 配置，我们可以在这里找到更多的细节*

*[](https://github.com/MQuy/redux-boilerplate/blob/master/cli/webpack.config.js#L99) [## MQuy/redux-样板文件

### redux-boilerplate - redux 样板(Node.js，Express，React，Redux，React-Router，Babel 6，PostCSS，Webpack)

github.com](https://github.com/MQuy/redux-boilerplate/blob/master/cli/webpack.config.js#L99) 

# 基准

之后，我们用 https://www.webpagetest.org/的 T21 来测试我们的结果。我们为“现代”浏览器节省了 0.3 秒。更多细节，你可以查看我们的基准

1.  加载同步

[https://www.webpagetest.org/result/170504_HD_15BD/](https://www.webpagetest.org/result/170504_HD_15BD/)https://www.webpagetest.org/result/170504_QN_15EJ/T2[https://www.webpagetest.org/result/170504_KT_15HP/](https://www.webpagetest.org/result/170504_KT_15HP/)

2.负载条件异步

[https://www.webpagetest.org/result/170504_H3_15MN/](https://www.webpagetest.org/result/170504_H3_15MN/)https://www.webpagetest.org/result/170504_VR_15Q1/https://www.webpagetest.org/result/170504_38_15VF/

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*