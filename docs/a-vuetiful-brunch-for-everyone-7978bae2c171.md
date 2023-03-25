# 为每个人准备的丰盛的早午餐

> 原文：<https://medium.com/hackernoon/a-vuetiful-brunch-for-everyone-7978bae2c171>

> 我希望如果你读到这个故事，你已经对 Vue 感兴趣了，这样我就不用告诉你我对 Vue 的看法了。

像往常一样，我只是想确保你知道你可能会错过的精彩内容，希望能为你提供一顿美味的早午餐。

尽管我认为 Vue 是开发 web 应用程序的最好的 UI 框架之一，但我也相信今天的 web 开发任务需要一个可靠的构建系统来很好地完成工作，并且不需要我们为它的实际执行做很多工作。当对整个应用程序使用 Vue 时，你需要构建……即使不是这样……如果你希望你的代码是最优的，你也需要构建。

当我开始构建现代 web 应用程序时，我偶然发现了这个问题，并找到了一些有趣的解决方案，如 [GULP](http://gulpjs.com/) ，一些更完整的东西，如 [Webpack](https://webpack.github.io/) ，我说……好吧，这对每个人都有用，让我们继续吧。但是不管我读了多少关于它的书，不管我用了多少预先构建的例子来做几乎任何事情，我都觉得很奇怪。

# 进入早午餐

不知从哪里冒出来一个名字 [**早午餐**](http://brunch.io/) 我甚至不记得是怎么出现的，也不记得我在搜索什么。我可以肯定地告诉你，那天天空为我放晴了。

Brunch 将自己定义为“超快速的 HTML5 构建[工具](https://hackernoon.com/tagged/tool)”，虽然这是真的，但它并没有充分发挥其威力。只需一个小的配置，你就可以基本上满足你的任何自动化需求，因为有最常用的插件，如 Babel，UglifyJS，Pug，HTML 模板，Handlebars，JS，CSS，Images 等等…这样你甚至可以将它用于节点应用程序以及客户端应用程序。

Brunch’s presentation video — Taken from Brunch’s website

Brunch 很容易学习，你几乎不需要做任何工作就可以让它工作，对于大多数插件来说，它提供了超快的承诺…它让你想知道为什么这么多人仍然使用 webpack，Gulp，甚至更糟，Grunt。

# Vue 和早午餐？为什么不呢？

Nathaniel Blackburn 构建了一个简单但很棒的插件，让你用 brunch 构建基于 VueJS 的应用程序，你会很容易感到惊讶。这叫做 [vue-brunch](https://github.com/nblackburn/vue-brunch) 而且效果很好！

Vue 的 Cli 工具很棒，但它使用 webpack，比 Brunch 慢得多，有大量的配置文件(显然不是为了编辑，除非绝对必要，虽然 rails 的感觉对我来说很好……但我觉得它的价格很高。

这就是为什么我想试试早午餐和 Vue-Brunch

# 与 Vuety 共进早午餐

是啊…又是文字游戏…我知道，但是再忍耐一会儿吧。

我想创建一个更新的 Brunch 框架，它将使用 Vue2 堆栈(包括新的路由器和 Vuex)，并以一种自以为是的方式这样做，以便用户不需要太努力地思考如何开始。我从纳撒尼尔·布莱克本的[吃 vue 的早午餐](https://github.com/nblackburn/brunch-with-vue)骨架中获得灵感，并做了一些更新和调整，于是[吃 vue 的早午餐](https://github.com/srodriki/brunch-with-vuety)诞生了。

 [## GitHub-srodriki/Brunch-with-vuety:一个使用 Vue2、Vuex 和…

### 与 vuety 共进早午餐——使用 Vue2、Vuex 和 VueRouter 的自以为是的早午餐框架。配备 axios 和…

github.com](https://github.com/srodriki/brunch-with-vuety) 

看一看它，它易于使用和理解，可以让您使用类似于 vue-cli 的东西，但使用 Brunch 而不是 webpack。对我来说，这是两种技术的结合，实际上值得更多的关注，到目前为止它们已经得到了更多的关注: [VueJS](https://vuejs.org/) 和 [Brunch](http://brunch.io/) 非常棒。

老实说……看看 Vue-cli 的 Webpack 构建脚本，再看看 **brunch-with-vuety 的脚本:**

```
module.exports = {
  files: {
    javascripts: {
      joinTo: {
        'vendor.js': /^(?!app)/, // Files that are not in `app` dir.
        'app.js': /^app/
      }
    },
    templates: {
      joinTo: 'app.js'
    }
  },
  plugins: {
    babel: {
      presets: ['latest', 'stage-3']
    },
    vue: {
      extractCSS: true
    }
  }
}
```

这就是 Brunch 的会议模式和 Webpack 的区别。简单多了，不是吗？

我将创建更多的骨架和模板久而久之，并希望提供一些更完整的。如果您有任何建议，请不要犹豫在这里打开问题或评论。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)