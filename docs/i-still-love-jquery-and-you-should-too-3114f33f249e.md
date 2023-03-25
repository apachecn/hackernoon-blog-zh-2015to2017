# 我仍然喜欢 jQuery——你也应该喜欢。

> 原文：<https://medium.com/hackernoon/i-still-love-jquery-and-you-should-too-3114f33f249e>

*2022 编辑:这篇文章写于 2017 年，当时我正在使用 Wordpress 做简单的营销网站。阅读这篇文章时应该牢记这一点。在 2022 年，jQuery 对于简单的事情仍然有用，但是如果您正在做任何稍微复杂的事情，本文不适合您。*

![](img/72b85844919b1724081dd2fd666af94e.png)

最近使用 jQuery 有一个很大的耻辱。似乎每个人都坚持在现代 JavaScript 开发中避免它。最近，开发人员都在谈论 TypeScript、ECMA2015+和当前流行的框架。

我觉得这很傻。jQuery 在 2017 年仍然非常重要——只是没有人谈论它。

# jQuery 不会让你的网站变慢

![](img/1f6cce8137c902ef2a3578e69b7475bc.png)

我最近看到的避免 jQuery 的最大原因之一是它给你的站点增加了不必要的文件大小。然而，事实是它给你的站点增加的文件大小通常是微不足道的，或者根本不存在。

很大一部分访问者[已经从 CDN](https://trends.builtwith.com/cdn/jQuery-CDN) 缓存了 jQuery。这意味着就您的带宽而言，使用 jQuery 基本上是免费的。它不会以任何有意义的方式减慢你网站的加载时间。

即使他们没有缓存， [jQuery Slim Minified + Gzipped 也只有 27KB 左右](https://code.jquery.com/jquery-3.2.1.slim.min.js)。您的 HTML 标记可能比这个大。如果您想要 jQuery 的全功能版本，只需要 6KB。

# webpack 外观很棒

webpack 是现代 JavaScript 开发的主要工具，您应该使用它或您选择的另一个模块捆绑器。使用 jQuery [作为外部库](https://webpack.js.org/configuration/externals/)，这样您就可以在需要的地方将它导入到您的模块中。其他工具，像 [Rollup](https://github.com/rollup/rollup/wiki/JavaScript-API#external) ，也有类似的东西。

# jQuery 使得编写代码更快

时间就是金钱。您的时间是宝贵的，客户认为您可以在几个计费小时内成功实现的大量生产功能是有价值的。如果你[可以节省 30 秒到处打字的时间](https://gist.github.com/joyrexus/5322252)，节省几分钟在别处写代码的时间，那么你无疑会开始展示出与其他厌恶使用 jQuery 的同事相比显著的生产力优势。

即使你只是在做一个个人项目，你也应该把自己当成你的客户，尽可能争取最好的价值。

# 您仍然应该注意缓慢的代码

![](img/78a31155456b490326356ada420d83e7.png)

在 jQuery 中编写非常低效的代码是非常容易的,。极低的准入门槛意味着存在大量非常糟糕的代码，即使在谷歌搜索的热门 StackOverflow 结果中也是如此。

这里有一个简短的(但绝对不是详尽的)你应该知道的事情列表:

*   jQuery 动画几乎不再是正确的答案。CSS 更适合这种东西，除非 IE7 支持是你关心的事情——在这种情况下，我经常会说没有动画比慢动画好。jQuery Slim 中移除了动画。
*   `$.each()`或`$(someSelector).each()`可以经常用普通回路替换。
*   `$.ajax()`不太好。如果你还没有用过 Axios 的话，它还是很不错的。jQuery Slim 中删除了这个功能。
*   如果重用选择器，将结果存储在一个变量中以备后用。查询 DOM 很慢。[stack overflow](http://stackoverflow.com/a/3947364)上的这个回答有个很好的例子。
*   类似地，如果你需要在某件事情上执行多个选项，利用[链接](https://www.w3schools.com/jquery/jquery_chaining.asp)。

# 编写可伸缩的 jQuery

![](img/03a435744e128d6f6394464558bc79a4.png)

我绝对不是建议你使用你能找到的每一个 jQuery 扩展来把特性组合在一起。对于预算很低、流量很低的站点，这对于几个特性来说可能是一种合理的方法，但是您在代码中使用的每个 jQuery 扩展都有可能导致显著的性能降低。您还需要知道您正在添加的库的年龄。他们可能会增加对 IE8 甚至 IE7 (*gasp*)支持的膨胀。

可伸缩 jQuery 的主题非常庞大，可能需要专门写一篇文章，但是简短的版本很简单:注意您使用的任何东西的性能影响。如果您正在使用 jQuery 扩展来添加一些功能，请做一些测试，看看它对您的网站或应用程序的影响有多大。如果您使用的是从 StackOverflow 复制并粘贴的代码，请确保在使用它之前了解它在做什么。

# 说真的，用 webpack

现在是 2017 年，如果你没有使用 webpack 这样的模块捆绑器，你就有点落后了。一个[非常基本的 webpack 配置](https://gist.github.com/sam-candeocreative/3103e19808ed1c75c54a9edd331f1e99)会给你 ECMA 2015+，这意味着模块的可扩展性。这也将使你容易丑化生产环境。

如果你在 2017 年反对 jQuery，并且不使用某些东西来缩小/丑化你的生产代码，我不太确定你的优先事项是直的。

# 在其他框架中使用 jQuery

我现在支持将 Vue 作为我的首选框架，其他许多人也是如此。2016 年，Vue 成为发展最快的 Javascript 框架。Vue 非常容易使用，命名约定简单，入门门槛非常低。

当然，将 jQuery 与 Vue 结合使用(如上所述，通过外部 webpack ),并不能真正增加很多额外的功能。但是，如果您对 jQuery 有一定程度的了解，那么用普通 JS 需要一个小时才能完成的工作可以在几分钟内完成。

# 何时真正避免 jQuery

![](img/7f01a03bce20cdbe3466f692864d2a00.png)

在我看来，在大多数情况下，是否使用 jQuery 是一个预算问题，而不是一个实际问题。如果您是一名非常熟悉 jQuery 的开发人员，并且您正在开发中小型网站，那么使用 jQuery 通常可以为您节省大量时间。

对于大规模的站点，您仍然可以考虑 jQuery，但是复杂性问题将会出现。如果你在一个网站上的多人团队中工作，jQuery 经常会使你的代码过于复杂，导致一个不可维护的网站。在这些情况下，你可能想考虑一个更严格的框架，比如 [Vue](http://vuejs.org/) 或 [Angular](https://angular.io/) ，它将提供一个更有主见的构建基础，而不是基于 jQuery 开发的自由竞争。

jQuery 很棒。我仍然喜欢每天使用它，我确实发现它为我的项目节省了大量时间。但是，您应该意识到它对您的网站或应用程序的任何性能影响。jQuery 遭受了年复一年的不良信息和经常被滥用的基于扩展的生态系统，如果你不小心的话，会让你的网站感觉像是在 2007 年而不是 2017 年建立的。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)