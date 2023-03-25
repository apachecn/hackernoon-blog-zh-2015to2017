# JSX 是怎么回事

> 原文：<https://medium.com/hackernoon/whats-the-deal-with-jsx-9ab2f862bf2b>

![](img/36ccfd422ce17964fa372b4ee2c31fcd.png)

Photo by [Émile Perron](https://unsplash.com/photos/xrVDYZRGdw4?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

自从我开始学习/使用 React 以来，我遇到了一些新的术语，比如 JSX。JSX(又名 JavaScript 扩展)，是一个 JavaScript 语法扩展，允许我们编写看起来像 XML 的 JavaScript。强烈推荐并普遍使用 React。与 XML 类似，JSX 也有标签名、属性和子标签。使用 transpilers(像 React JSX，jsx-transform，Babel)将 JSX 转换成浏览器兼容的 JavaScript。

让我们来看一个 JSX 的例子:

```
<MyExample className=”example”>
    <p>Hello World! {2+2}</p>
</MyExample>
```

## 分解它

*   它有一个标签名`MyExample`——可以是用户定义的组件，也可以是内置组件，例如`<div>`、`<p>`。用户定义的组件应该大写，因为 JSX 认为小写标签名称为 HTML 标签。
*   它有一个属性`className`——class name 被用来代替传统的 DOM 类，因为`class`和`for`一样，在 Javascript 中是保留的，而 JSX 是 Javascript 的扩展。一个标记可以有 0…多个属性，它们遵循 camelCase 属性命名约定。
*   它有一个子标签`<p>` —一个标签可以有多个子标签，包括其他 JSX 标签。
*   它包含一个 JavaScript 表达式`{2+2}`——你可以在 JSX 中嵌入任何 JavaScript 表达式，方法是用花括号把它括起来`{}`。

[PS:如果它没有子标签，你可以有一个自封闭标签，即`<MyExample />`

考虑用 JSX 来写一个`React.createElement()`声明更短更自然，因为在运行时，上面的例子被转换成:

```
React.createElement(
    MyExample,
    { className: "example" },
    React.createElement(
        "p",
        null,
        "Hello World!",
        2+2
    );
);
```

虽然您仍然可以使用 React.createElement 创建 React 元素，但我发现 JSX 更容易掌握，尤其是如果您来自 HTML + CSS 背景。

过去，专家们不赞成将 JavaScript 和标记结合在一起，我个人起初觉得这很令人不安。然而，自从我开始使用 react，我意识到 JSX 帮助我轻松地将我的 UI 设计翻译成代码，并对 UI 的逻辑有了更好的理解(JSX 的好处之一)。

如果你想深入了解 JSX 在 React 中的用法，请点击[查看](https://reactjs.org/docs/jsx-in-depth.html)。

如果您有任何问题或贡献，请在下面留下评论。