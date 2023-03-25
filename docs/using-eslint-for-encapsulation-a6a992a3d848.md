# 使用 ESLint 进行封装

> 原文：<https://medium.com/hackernoon/using-eslint-for-encapsulation-a6a992a3d848>

Linters 可能没有编译器强大——因为它们不能添加新的语言特性——但是它们很擅长*去掉*特性。有时，删除功能和添加功能一样强大。

当我们谈论**封装**时，这个观察尤其正确。封装是关于[信息隐藏](https://en.wikipedia.org/wiki/Information_hiding)，又名*取走*信息。许多封装特性都是在语言级别实现的，比如函数范围和模块范围。但是当我们想要比语言提供的更多的封装时，linters 是一个很好的选择。

让我们看一些例子，ESLint 为我们提供了封装，而 ES6 却没有。

# 封装浏览器 API

如果你需要支持服务器端的渲染，那么你必须确保你的代码可以在服务器环境中运行，这里不会有任何特定于浏览器的全局变量，比如`window`、`location`等等。幸运的是，ESLint 允许我们为给定的文件配置全局变量:

```
*ReactDOM.render(
  <App />,
  document.getElementById('root')
); // flagged by linter* */* eslint-env browser */**ReactDOM.render(
  <App />,
  document.getElementById('root')
); // good to go!*
```

这样，我们可以*对所有模块隐藏*浏览器 API，除了需要它们的特定模块。我们也可以对其他环境做同样的事情！

# 封装模块

假设我们想将代码库中所有的[网络](https://hackernoon.com/tagged/network)请求集中到一个名为`api.js`的文件中。我们可能会为此使用一个单一的网络客户端，例如 Axios 。我们可以通过确保除了我们的`api.js`文件之外，代码库中没有人可以导入 Axios 来加强这一点。

我们将使用[no-restricted-modules](http://eslint.org/docs/rules/no-restricted-modules)lint 规则来禁止其他模块导入 Axios，方法是将其添加到我们的`.eslintrc`中:

```
{
    "no-restricted-modules": ["error", "axios"]
}
```

然后只为我们的`api.js`文件禁用它:

这样，我们可以对所有模块隐藏联网功能，除了我们列入白名单的特定模块。

# 封装类

假设我们想要在我们的 [Javascript](https://hackernoon.com/tagged/javascript) 类中有一个私有方法。不幸的是，我们没有关键字`private`来强制执行这条规则，但是我们可以使用 ESLint 的[无下划线悬挂符](http://eslint.org/docs/rules/no-underscore-dangle)规则来达到类似的效果:

```
class MyClass {
  _myPrivateMethod() {}

  myPublicMethod() {}
}const instance = MyClass()
instance._myPrivateMethod(); // flagged by linter
```

# 线头规则是交通锥

以上所有的封装都很容易破。像 linters 这样的静态分析工具无法抵御像`require('axios')`或`instance['_myPrivateMethod']()`这样的动态语言特性，这没什么。林特规则更像交通锥，而不是混凝土墙。

这个想法并不是为了防范对这些规则的故意违反。这是将阻力最小的路径与最佳实践结合起来，这通常就足够了。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！