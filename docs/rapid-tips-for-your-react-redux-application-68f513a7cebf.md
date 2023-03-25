# React-Redux 应用的快速提示

> 原文：<https://medium.com/hackernoon/rapid-tips-for-your-react-redux-application-68f513a7cebf>

![](img/3038c14b5c20787e9d6e4e875c391af2.png)

在我工作的地方，我们正在开发 [crystal](https://crystal.io/) :数字[营销](https://hackernoon.com/tagged/marketing)的第一个虚拟顾问。
在过去的几个月里，应用程序经历了前端模块的彻底重写，从 angular 迁移到 react-redux 堆栈。

我们对最终的结果感到非常自豪，因为我们已经烘焙出了一个性能和可维护性更好的产品。尽管如此，离这里还有很长的路要走。

在接下来的几个月中，我们计划推出许多新功能，但是，我想与您分享一些有用的技巧，每个人在使用类似的堆栈时都需要记住这些技巧。

经过几个月的工作，我想分享一些关于这个奇妙的堆栈的小技巧，这些技巧很容易采用。

## 立即选择一个可扩展的文件夹结构。

如果你正在开发一个大项目，不要低估这方面。文件夹重构是相当痛苦的，尤其是在 Javascript 中；这肯定会让你付出很大的代价，无论是在时间上还是精神上。有了可扩展的结构，你可以很容易地为团队中的每个人设计和组织你的项目。

我们最终使用了一种混合结构，它混合了功能文件夹(每个应用程序大功能一个文件夹)和类型文件夹(components、redux、services)，用于所有通过应用程序横向共享的文件。

有很多关于这个话题的文章(你可以在 [Redux 文档](http://redux.js.org/docs/faq/CodeStructure.html)中找到一些)，可以帮助你选择最适合你的结构。

编辑:关于文件夹结构的酷演示[此处](https://presentations.survivejs.com/structuring-react-projects/#/?_k=sxx6op)。
感谢 [@hinoczek](/@hinoczek)

## 首先设计你的 redux 商店。

redux-store 是应用程序的焦点。
如果商店设计得好，经营起来就不会那么困难。此外，您还将提高性能(良好的设计——更少的操作)。

请记住，网上有人可能在你之前解决了你的问题，看看任何使用 redux 的大公司，以及他们是如何构建他们的商店的。

> 受他人启发。

同样， [Redux 文档](http://redux.js.org/docs/recipes/reducers/NormalizingStateShape.html)展示了一个很好的例子。
T3【解剖 Twitter 的 Redux 商店】T4

## UI 的状态与道具

在这个主题上确实没有最佳实践。
我们写得非常快，在编写代码的过程中，我们有两种练习。一些开发人员/团队成员/个人可能会使用 redux store 来管理一些 UI，而其他一些人则会使用组件状态。
所以**不要制造混乱**，选择最适合自己的方式，前后连贯。
我需要把这个零件放在 redux-store 上吗？我可以保留组件的什么状态？
在这个特殊的例子中，我们讨论的是组件的状态和属性，但是请记住:

> 事前设计，事后不哭。

好[导](https://github.com/uberVU/react-guide/blob/master/props-vs-state.md)一下吧。

## 如果你用巴别塔，当你有机会写 ES5 的时候，就去做吧。

这看起来很奇怪，但事实并非如此。我梦想有这样一个世界，javascript 代码在编辑器和浏览器控制台中都看起来既流畅又漂亮。嗯，我们(还)没有真正生活在那个世界里。
大家都喜欢写 ES6，但是它强迫我们使用 transpiler。
Babel 非常酷，但是在很多情况下，它增加了你翻译成 ES6 特性的代码量。

> 代码太多=太慢

[展示这方面的精彩文章](https://v8project.blogspot.it/2017/02/high-performance-es2015-and-beyond.html)

## 结论

我希望这篇文章能帮助你避免我们遇到的一些问题。

感谢我的团队，祝编码愉快！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> 黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)