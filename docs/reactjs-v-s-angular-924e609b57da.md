# 反应堆 v/s 角度

> 原文：<https://medium.com/hackernoon/reactjs-v-s-angular-924e609b57da>

![](img/4c3a7c3d5c416019367e28ecb52602ef.png)

如题，故事战斗即将开始，两个最强大的前端[工具](https://hackernoon.com/tagged/tools)。

***Angular*** 是 Google 的一个前端框架，基于 MVC，基本上用于 SPA(单页应用)，建议与 typescript 一起使用(根据官方说法)，基于组件。

而 ***Reactjs*** 是[脸书](https://hackernoon.com/tagged/facebook)提供的前端库，一般用于用户界面。它也是基于组件的，与 JSX (Javascript 语法扩展)一起使用。

正如软件开发者西奥多·罗斯福曾经说过的，

> ***“除非意味着努力、痛苦、困难，否则世界上没有什么值得拥有或值得做。”***

所以经过大量的谷歌搜索和努力，这里有一些要点给你。

基本上两者在他们自己的术语上有很大的不同，但是仍然有相似之处，在这篇文章中我们将讨论他们是如何彼此不同的。

C 部件结构

毫无疑问，两者都是基于组件的。

> ***组件结构*** 意思是说一段独立的代码，你可以通过调整一些风格等在多个项目中使用。例如，每个项目的登录/注册部分几乎相同，所以我们将为两者创建单独的组件，拥有自己的 HTML/CSS/JS 代码，稍后将在多个项目中使用这一点。

两者的区别在于，

*   **Angular** 将推荐在单独的文件中使用 HTML、CSS、JavaScript，而在 **React** 中，我们必须使用 JSX 以及 ES6 方法变量等来代替 HTML。
*   在 **Angular** 中，我们将在 HTML 文件中编写单独的代码，而在 **React** 中，我们必须在 **return** 方法中编写 **JSX** 代码，该方法稍后将模板返回到浏览器。

> 换句话说，你可以说 ReactJs 将 HTML 嵌入到 JavaScript 中，而其他人继续将 Js 放入 HTML 中。

C SS 类绑定。

由于 React 使用 JSX 而不是纯 HTML，所以它不允许我们直接使用一些关键字，如 ***class，for*** 等，所以我们必须分别使用 *className* 和 *htmlFor* 来代替。为了使用这些，JSX 为我们提供了一些不同的关键字。

因此，当我们必须在 angular 中定义 CSS 类名时，我们简单地定义如下

> 你好棱角分明的道

但是我们不能在 JSX 中直接使用 class 关键字，否则 Reactjs 会将其与 javascript 的类混淆，所以当我们必须在 Reactjs 中定义类名时，我们会像这样使用 class name

> 你好反应过来的方式

可变装订。

Angular 和 Reactjs 中的变量绑定几乎相同，只是在语法上有一些变化。

在 Angular 中，我们使用`{{}}`绑定到模板中的某个变量

在 React 的情况下，我们使用`{}`来绑定到变量。但是有时我们也在 Reactjs 中使用`{{}}`,例如我们必须在元素中定义内联 css，然后我们会像这样使用。

`<p style={{color:"grey"}}>Hello Variable Binding</p>`

C 备注。

在 JSX 中有不同的方式来注释代码

`{/* This is JSX comment */}`

但是在 angular 中，我们可以像这样使用简单的 HTML 和 JS 的注释

`/* This is angular's comment */`

B 不规则尺寸

***React*** 捆绑大小比 angular*小*(按社区，我还没测试过)。
然而，*要比*大得多，这有时会导致更长的加载时间和手机性能问题。但是也有很多方法可以减少 angular 应用程序的包大小，比如你可以使用 ***AOT，树摇动*** 等等，这些方法可以将包大小减少到实际大小的一半。**

> ****…..未完待续！****

***如果你喜欢这个，* ***请鼓掌，*** *让别人也能喜欢。在 Twitter 上关注我@*[***p*ardeepjain _ 90**](https://twitter.com/Pardeepjain_90)*获取最新更新:)***