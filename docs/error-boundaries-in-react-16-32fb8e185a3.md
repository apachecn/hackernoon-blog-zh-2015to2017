# React 16 中的错误边界

> 原文：<https://medium.com/hackernoon/error-boundaries-in-react-16-32fb8e185a3>

[React 16 来了](https://reactjs.org/blog/2017/09/26/react-v16.0.html)它带来了很多激动人心的变化。让我兴奋的特性之一是更好的错误处理。以前，渲染期间的运行时错误可能会使 React 处于中断状态。

有了 React 16，你可以使用错误边界，而不是每次出现错误就卸载整个应用。将错误边界想象成 try-catch 语句，但是是针对 React 组件的。

> 错误边界是 React 组件，它捕捉子组件树中任何地方的 JavaScript 错误，记录这些错误，并显示一个后备 UI，而不是…