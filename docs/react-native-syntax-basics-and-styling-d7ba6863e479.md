# React 本机语法基础和样式

> 原文：<https://medium.com/hackernoon/react-native-syntax-basics-and-styling-d7ba6863e479>

我已经写过[安装和开始使用 React Native](https://hackernoon.com/reworking-a-web-app-with-react-native-part-1-troubleshooting-set-up-cff77274a6fa) ，以及[如何对初始屏幕(或“stage”)上的文本进行基本更改](https://hackernoon.com/reworking-a-web-app-with-react-native-part-2-presentational-components-and-static-pages-d517884f53e5)，现在我将写一点关于我在使用我的 [React Native](https://hackernoon.com/tagged/react-native) 应用程序时发现的一些不同于 React 的重要语法概念。

快速回顾一下 React 和 React Native 之间的一些区别

1.  React 是一个库，React Native 是一个框架。
2.  React 原生应用不是移动 web 应用，而是原生应用
3.  React Native 不使用 CSS(稍后会详细介绍！)

## React 本机基本代码结构

像 React 一样，React Native 使用包括箭头功能在内的 ES6 特性，也使用 JSX。

下面是 React Native [文档](https://facebook.github.io/react-native/docs/tutorial.html#content)的一个片段。

```
import React, { Component } from 'react';
import { Text } from 'react-native';export default class HelloWorldApp extends Component {
  render() {
    return (
      <Text>Hello world!</Text>
    );
  }
}
```

是 JSX，就像页面上的 [HTML](https://hackernoon.com/tagged/html) 一样。

使用 React，您可以将 HTML 封装在一个封闭的

标签中，例如:

```
import React from 'react'const Intro = () => {
  console.log("Intro renders!")
  return (
      <div>
        <div className="container">
        <div className="site-title">
          <h2>Home Page</h2>
        </div>
          <p> Lorem ipsum dolor sit amet, consectetur adipiscing        elit. Pellentesque pulvinar euismod commodo. Etiam orci dolor, commodo non sem vitae, ultricies porttitor sem. 
          </p>
        </div>
      </div>
      )
      }export default Intro;
```

在 React Native 中，关键是使用 JSX，而不是 HTML 标签。例如，如果您希望某些内容呈现出您想象中的 HTML 所呈现的效果

```
<p>Hello world!</p>
```

然后你可以用 <text>JSX 标签来写:</text>

```
<Text>Hello world!</Text>
```

如果你不这样做，你会得到一个错误信息，这是我在开始使用[更新我的应用](https://hackernoon.com/reworking-a-web-app-with-react-native-part-2-presentational-components-and-static-pages-d517884f53e5)时遇到的第一个问题。在这种情况下，这个错误消息(通常显示在你用作视图的屏幕上——在这种情况下是在我的 iPhone 上)实际上表明我使用了 HTML 标签，而不是正确的 JSX 标签。

![](img/3ab97358acb257ef4fe21e763c880f33.png)

# 更新屏幕

React 是单页的，这意味着当您在应用程序上导航时，您并没有真正移动，而是不同的组件只是显示在页面上。在 web 应用程序中，每次点击都会加载一个新页面(例如，想想 Rails 应用程序，或者使用[链接](“#”)),这种体验就像是同一概念的一个更平滑的版本。感觉很相似，只是无缝和快得多。

然而，在 React Native 中，您可以使用 React Native 工具在屏幕或“场景”之间导航。文档中建议的最简单的一个是 [React Native Navigation](https://facebook.github.io/react-native/docs/navigation.html) ，这是一个易于使用的库，可以让用户非常快速地设置屏幕。正如在[文档](https://facebook.github.io/react-native/docs/navigation.html)中提到的，你也可以堆叠和覆盖路由器，这意味着你可以在一个地方进行调整，而不会影响你的整个程序。

# 式样

最后，快速概述一下造型。(文档[此处](https://facebook.github.io/react-native/docs/style.html)。)

React Native 不使用 CSS。然而，对于有使用 CSS 或 LESS 或 SASS 等预处理器经验的人来说，自学如何使用 React Native 进行样式设计应该非常简单。许多语法是相似的，尽管结构有点不同。

所有 React 本机组件都有适当的“样式”，这允许我们对组件进行样式更改。

然后可以在组件内调用该样式，比如在一个<text>上。</text>

```
export default class StyleExample extends Component {
  render() {
    return (
      <View>
        <Text style={styles.black}>Black Text</Text>
      </View>
    );
  }
}
```

使用以下格式在文件的其他地方(通常在底部)定义样式

```
const styles = StyleSheet.create({
  black: {
    color: 'black',
  },
});
```

## 接下来

更深入地了解 React Native Navigator 并创建导航栏和表单！

## 资源

[](https://facebook.github.io/react-native/) [## React Native |使用 React 构建原生应用的框架

### 使用 React 构建原生应用的框架

使用 Reactfacebook.github.io 构建原生应用的框架](https://facebook.github.io/react-native/) [](https://stackoverflow.com/questions/34641582/what-is-difference-between-react-native-vs-react) [## React native 和 React 有什么区别？

### 出于好奇，我开始学习 React，并想知道这一点。在谷歌上找不到满意的答案…

stackoverflow.com](https://stackoverflow.com/questions/34641582/what-is-difference-between-react-native-vs-react) [](https://github.com/ReactTraining/react-router/issues/4747) [## 从 Navigator 到 React 路由器文档/示例请求问题# 4747 React 培训…

### 在通读了 native 的文档后，我仍然有几个问题，希望能得到解答…

github.com](https://github.com/ReactTraining/react-router/issues/4747) [](https://www.quora.com/What-are-five-differences-between-React-js-and-React-Native) [## React.js 和 React Native 的五个区别是什么？

### 回答(第 1 题，共 16 题):React.js 是由脸书开发的，旨在满足其对动态和高性能用户的需求…

www.quora.com](https://www.quora.com/What-are-five-differences-between-React-js-and-React-Native)