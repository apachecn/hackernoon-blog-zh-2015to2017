# 2017 年的 JavaScript:需要多大的脚手架？

> 原文：<https://medium.com/hackernoon/js-in-2016-how-much-scaffolding-do-you-need-bdbf857af766>

你想为一个简单的 web 应用程序构建前端，接下来你知道你担心的是 React、Redux、Thunk、Babel、ES6、Flow、Browserify、BrowserSync、Gulp、SASS、Radium、Karma、Jasmine 和 Nightwatch.js(例如)。

比我聪明的人在[大](https://hackernoon.com/how-it-feels-to-learn-javascript-in-2016-d3a717dd577f#.8qjzx6zhx) [长](/@ericclemmons/javascript-fatigue-48d4011b6fc4#.jou9zokmt)的时候抱怨过这个，所以我不会。

相反，最近我自己也在纠结这个问题，我想教你如何不搭棚子。

## 如何不搭棚子

> 声明:我从未搭建过棚子

建造棚子时，你不需要大量的脚手架。

不需要起重机(2017 版或其他)。

你甚至不需要一条现代光滑的运输管道，它会自动运送你的原材料，因为你需要用最闪亮的现代酷车运送它们。

其实这些东西不只是矫枉过正，实际上会碍事。在你可能已经完成建造你的小棚子之后，你将会在你的起重机的指令手册中深陷。

这同样适用于用 JS 构建。

那些刚起步的人，不是在建造火箭或摩天大楼；我们在搭建棚子。所以问题是，你需要多少脚手架才能做到这一点？

人们很容易犯谨慎的错误，仅仅包括所有的东西。脚手架发电机(例如约曼)使我们从第一天开始就很容易做到这一点，但这通常是无益的*。结果往往是一个臃肿的项目，很难开始。根据我的经验，一个项目中有太多的东西比没有足够的东西更具破坏性。

*尽管我很喜欢它们，但我建议在你清楚自己需要什么之前，不要购买脚手架发电机。

## 根据需要添加脚手架

试着用最少的工具开始建造你的棚子。你会进步很快，棚子可能会倒，但没关系。

这给了你时间去了解你正在使用的[工具](https://hackernoon.com/tagged/tools)，并帮助你了解你需要做什么来防止它在未来倒塌。然后，根据需要添加额外的工具。冲洗，重复。

例如，很难知道是否/何时包含 flux (redux 或其他)。

引用早期 React 成员之一的皮特·亨特的话:

> *你会知道什么时候需要助焊剂。如果你不确定你是否需要它，你就不需要它*

作为回应，[丹·阿布拉莫夫](https://twitter.com/dan_abramov)，Redux 的创造者:

> 我想修改一下:除非你对香草反应有问题，否则不要使用 Redux。

我想这两个人都意识到了这一点，尤其是对于初学者来说，仅仅为了使用它而使用它是弊大于利的。

[JavaScript](https://hackernoon.com/tagged/javascript) 宇宙使得插入新工具和切换部分堆栈变得容易。利用这一点！你不需要购买一个铁板一块的固执己见的方法。

例如，不喜欢 TypeScript、Browserify 或内联样式？别担心，带点别的东西去兜一圈。

## 有用的提示 1:留意哪些问题已经被解决了

当时髦的开发者说出最新的名词时，问问为什么需要它，并记下来。你将来很可能会发现它很有用。即使你不是从使用 redux 开始的，知道它解决什么问题也是有用的。这样，当你遇到那个问题时，你就不会重新发明轮子(或者放弃尝试)。

## 有用的技巧#2:参考项目模板和初学者工具包

当根据需要添加脚手架时，您不希望花费太多时间来手动配置您的基础设施。如果你发现你的轮子在旋转，参考一些[多](https://github.com/kriasoft/react-starter-kit) [牛逼](https://github.com/erikras/react-redux-universal-hot-example) [模板](https://github.com/GordyD/3ree)用于你的堆栈。然后无耻地偷他们的东西。这些是由一些聪明的家伙做的，他们可能会帮助你在这个过程中学习一些最佳实践。

# 包装它

看看 Pete Hunt 关于如何以迭代的方式与 React 和朋友相处的精彩指南。重要的是:

> "只有当你有问题需要解决时，才进入下一步."

> [https://news.ycombinator.com/item?id=12677640](https://news.ycombinator.com/item?id=12677640)HN 在这里讨论
> 
> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！