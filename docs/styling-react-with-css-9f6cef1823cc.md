# 样式与 CSS 反应

> 原文：<https://medium.com/hackernoon/styling-react-with-css-9f6cef1823cc>

使用 CSS 来设计 React 应用程序/组件的样式有很多种方法。根据不同的用例，每种方法都有其优缺点。这是一篇用简洁的代码/输出语法/模式来比较选项的文章。

如果你有*页面加载时间*意识，为你的用例选择正确的方法是一个相当复杂的问题，因为你必须整体考虑许多方面，例如，页面加载时间、加载环境(浏览器、应用内浏览器、聊天平台 Webviews…)、CSS 代码分割、Webpack、CommonsChunkPlugin、ExtractTextWebpackPlugin、CSS 树抖动、服务工人、缓存、你的开发工具…等等。不幸的是，我们正处于一个前端开发不必要的复杂的时代，在未来，许多前端开发有望通过构建系统实现自动化。现在，回到现实。

如果您对页面加载时间没有压力，也许您就不会为这些问题而烦恼。然而，如果你接触移动，加载时间是一个至关重要的问题，CSS 是一个重要的元素。

考虑在 Messenger 聊天机器人中加载 Webview，或者在点击广告后在脸书/Instagram 中加载结账页面。你必须有一个快速的*第一次有意义的绘画*和*第一次互动时间*。从 CSS 加载性能的角度来看，必须只加载所需的 CSS(内联或文件)，CSS 缩小/本地化和缓存必须与上述所有绑定设置一起考虑。在本文中，我将重点介绍如何将 CSS 集成到 React 中，将捆绑配置留给以后的文章。但是别忘了，这两者是一前一后的。

以下是在 React 中使用 CSS 的 5 种方法，并附有简洁的示例代码:

1.  普通 CSS 样式表
2.  内嵌样式
3.  CSS-in-JS
4.  CSS 模块
5.  样式组件

## **1。普通 CSS 样式表**

只需导入您的普通旧 CSS 文件，并在您的元素上使用`className`来附加您的样式。

请注意，您的样式不会被本地化到您的组件，它将按原样附加到您的 DOM 元素(全局问题！).

## 2.内嵌样式

将你的样式内联为 JS 对象，并给它们分配`style`标签。注意`style` *键*是驼峰式的。

```
<div style={YourStyle}>
```

您也可以直接在元素上使用内联样式。

```
<p style={{fontSize:'15px', textAlign:'center'}}>
```

最终输出因构建过程而异。

样式可以内嵌(见下文)，

```
<p style="fontSize:15px; textAlign:center">
```

或者拆分成页眉或单独的文件。

## 3.CSS-in-JS

这实际上与*内联样式*相同，除了样式对象保存在一个单独的 JS 模块中。对于 CSS 本地化和其他增强，您可能希望使用一个附加库(例如，Radium)。

关于内联样式和 CSS-in-JS 比较的细节？[https://mxstbr.blog/2016/11/inline-styles-vs-css-in-js/](https://mxstbr.blog/2016/11/inline-styles-vs-css-in-js/)

# 4.CSS 模块

CSS 作为普通 CSS 保存在 CSS 文件中。将你的 CSS 文件导入到 React 组件中，并用`className={YourImportedComponent.YourCSSClass}.`在你的元素/组件上使用

您的样式将被本地化并附加到您的 DOM 元素，如下所示。

```
<p class=”_255BVABjBx_0DfMd8sZNVs”>CSS Modules</p>
```

更多关于 CSS 模块，[https://glenmaddern.com/articles/css-modules](https://glenmaddern.com/articles/css-modules)。

# **5。样式组件**

风格化组件(及其变体)是目前最有争议的方法，并导致了不同阵营的形成。样式组件与 CSS 模块。每一种都有其优点，JS 强大的风格操作是风格化组件(和其他 CSS-in-JS 实现)的主要优点。

与 CSS 模块一样，样式化组件也是 CSS 本地化的。

```
<p class=”sc-bwzfXH iofHNH”>Styled Components</p>
```

**尽情享受！**