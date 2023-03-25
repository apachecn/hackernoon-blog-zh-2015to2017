# “高性能 JavaScript”综述

> 原文：<https://medium.com/hackernoon/review-of-high-performance-javascript-rendering-script-faster-react-nodejs-es6-155fcb5c820a>

![](img/905ddd4c968f3ad85916cdd951b64819.png)

这是一篇关于 Javascript 性能的笔记，请阅读尼古拉斯·c·扎卡斯所著的《高性能 Javascript[》一书，该书帮助开发人员发现问题并提供更好的解决方案，以改善执行时间、下载、与 DOM 的交互、页面生命周期等。](http://shop.oreilly.com/product/9780596802806.do)

## 数据存取

像 ***范围链*** 和 ***原型链*** 这样的概念会影响你的整体脚本性能。

## DOM 脚本

DOM 交互比 JavaScript 的其他部分要慢，重画和重流是如何让你的代码变慢的。

## 算法和流量控制

当涉及到运行时性能时，循环和递归会对你不利。

## 字符串和正则表达式

浏览器中较差的字符串处理性能，解释了解决方法

## 响应界面

用户体验，讨论了几种技术来确保用户界面始终保持响应。

## 埃阿斯

不同的数据格式会影响 Ajax 的性能，这也是为什么`XMLHttpRequest`并不总是最好的选择。

# 注意

## JS 的演变

*   JavaScript 在 1996 年作为网景浏览器的一部分被首次引入
*   谷歌在 2008 年**推出了名为 **Chrome** 的全新浏览器。**
*   V8 JavaScript 引擎是一个**实时(JIT)** ，从 JavaScript 代码产生**机器码**然后执行。

## 事情影响 JS

*   网络延迟和操作导致的延迟，影响浏览器的外观
*   简单的优化，如**函数内联**、**代码折叠**和**字符串连接算法**很容易在编译器中优化，它只解决了部分性能问题。

## JS 的最初目标

*   JS 最初是为**表单验证**设计的
*   JS 最初是为页面中的**功能直接设计的**

# 参考

——[http://shop.oreilly.com/product/9780596802806.do](http://shop.oreilly.com/product/9780596802806.do)