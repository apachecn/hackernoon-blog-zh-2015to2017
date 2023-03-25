# js 2017——发生了什么，❤需要关注什么

> 原文：<https://medium.com/hackernoon/js-2017-what-happen-what-to-look-out-for-8d47a06d8788>

哦，不，又是那个家伙。

去年，我写了一篇[的文章](/@sakulstra/from-php-to-node-js-meteor-a94d18e6511b#.9wllbh8zr)，讲述了为什么我很高兴——***或者至少不沮丧:P***——不知何故在 web/js 领域安顿下来。所以现在，一年后，是时候重温我的帖子了，看看我是否还抱有这种心态。

# TL；dr；

> 我认为 node.js/javascript 生态系统仍然是网络开发的一个好选择。

## **NPM**

我还是 npm 和 node.js 包管理的粉丝。Npm [v4](https://github.com/npm/npm/releases) 几周前刚刚发布，facebook 发布了 [yarn](https://code.facebook.com/posts/1840075619545360) ，它已经非常稳定，实际上是 Npm 的一个很好的替代品(包括更快的+~收缩)。

## 节点. js

这里不多说:)Node.js 和 v8-engine 还在快速进化中。看看发行说明就知道了。Node.js [v7](https://nodejs.org/en/blog/release/v7.0.0/) 现在有将近 98%的 es6 特性支持，并且 [v8](http://v8project.blogspot.de/) -engine 为 es6 操作者获得持续的更新/优化。

## 流星

一年前，我称赞了 Meteor，并把它用在了几乎所有事情上——从小原型到真正的应用程序。今天，我基本上只在别无选择的情况下使用 meteor，但不要误解我的意思:

> ***那不是“我要离开流星”的帖子。***

Meteor 仍然和一年前一样伟大(说实话现在甚至更好)——只是它跟不上快速发展的 js 世界。我仍然维护着流星项目，我没有像一年前那样有更多的(基本上没有)问题。

Arunoda Susiripala 宣布离开 meteor 令人难过，我认为这对 meteor 社区是一个沉重的打击——我个人非常钦佩他的工作。

也就是说，你真正需要的东西都还在。故事书仍然受支持，react-composer 也是。 [React-router-ssr](https://github.com/thereactivestack-legacy/meteor-react-router-ssr) 被[分叉](https://github.com/jdconley/meteor-react-router-ssr/)并且仍然工作良好。[的军服](https://github.com/vazco/uniforms)依然保养得很好。简单-模式像一年前一样工作。所以总而言之，对于 node.js 初学者，我实际上仍然会建议将其作为一个完整的堆栈来开始，但每个学习了 webpack 和 hmr 的好处的人可能都应该考虑使用其他东西。我真的很好奇他们是否会在不久的将来发布 [npm 过渡](https://github.com/meteor/meteor/blob/devel/Roadmap.md#full-transition-to-npm)。如果是的话，我认为这将再次产生重大的推动作用。想象一下像创建-反应-应用程序这样的东西，但对于 minimongo 和 co .的 meteor 来说，这实际上仍然是一流的。

## 值得一看的酷东西:

[**创建-反应-应用**](https://github.com/facebookincubator/create-react-app) **:** 已经有一段时间了:仍然是一个非常棒的入门基础。目前我最常用的选择——即使你必须在安装后立即弹出。

[**next . js**](https://github.com/zeit/next.js/releases)**:**实际上是 create-react-app 的一个非常好的替代方案，自带路由器和代码分割，简单的 ssr 支持等等。我只是在小项目上测试了它，但它已经很棒了…我只是真的错过了动态路线，但 V2 测试版刚刚发布，让我们看看会有什么。

**firebase:** 起初我持怀疑态度，但过去几个月我在各种较小的应用程序中使用了它，它实际上已经很酷了，而且发展很快。对于大多数 firebase web 项目来说，您仍然不得不离开“**忘记基础设施**”的舒适区，这有点可悲(因为您最有可能需要 ssr 或至少一个小型服务器组件来处理电子邮件或类似的东西)。不管怎样，我会把它推荐给每个人，我很高兴看到它的下一步走向——即使在网络上，与 iOS 和 Android 版本相比，它感觉有点像不被喜欢的孩子。

[](https://github.com/material-components)****:****MDL 的 v2 基本上有了它的第一个 alpha 版本，我很好奇它会如何发展，如果[做出反应，mdl](https://github.com/react-mdl/react-mdl) 会设法跟上新版本。****

## ****材料****

****当然，还有很多更酷的事情正在发生——关于大多数我不太了解或者一无所知的事情。因此，在不久的将来，我想尝试以下几件事……例如[preact](https://github.com/developit/preact)——一种快速、简单的反应方式。仍然不确定我对 graphql 的想法，但我肯定会试一试。同样在 react world 之外:angular[v4 的第一个测试版已经发布。](https://github.com/angular/angular/releases)[聚合物](https://github.com/Polymer/polymer/releases) [v1.7](https://github.com/angular/angular/releasesz) 已经发布。浏览器对[web 组件](http://webcomponents.org/)的支持已经有了很大的发展。IE 紧挨着[死](http://www.w3schools.com/browsers/browsers_explorer.asp) (≤v10)，Safari 也是慢慢死(≤8):)！****

****感谢阅读，新年快乐:)****

****只是为了让这一点非常清楚——我故意在这里错过了很多很酷的东西，因为我只写下了我短期内感兴趣的东西。例如 webpack v2 和 react-router v4 已经非常酷了，但我还没有任何使用体验。****

****[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)********[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)********[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)****

> ****[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿美族家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。****
> 
> ****如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！****

****![](img/be0ca55ba73a573dce11effb2ee80d56.png)****