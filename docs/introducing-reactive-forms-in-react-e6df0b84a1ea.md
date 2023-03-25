# 在 React 中引入反应形式

> 原文：<https://medium.com/hackernoon/introducing-reactive-forms-in-react-e6df0b84a1ea>

## *有角的像反应性的形式在起反应。*

表单是 web 应用程序的重要组成部分，管理表单状态尤其是在像 react 这样不支持双向数据绑定的库中并不是一件容易的事情。

> angular 中创建表单有两种方式，一种是`template-driven-form`，一种是`[reactive-form](https://angular.io/guide/reactive-forms)`。模板表单很好，但只适用于较小的表单，但当你必须处理较大的表单时，反应式表单就像魔法一样有效。

angular 中的反应式表单或数据驱动表单非常流行，它提供了一种更好的结构化方式来构建和控制你的表单。

现在来看 react，它有许多第三方库来控制表单，但它们都没有太多丰富的功能。

# 当前图书馆的问题

当前库的一些常见问题是

*   **依赖问题**
    为 ex。`redux-forms`，取决于 redux。所以，如果我正在使用另一个状态管理库或者甚至没有使用任何一个。
*   **样板文件** 很多样板文件代码只写一个两个字段的登录表单。
*   **UI 独立** 不完全独立于 UI。
*   **嵌套表单** 很多流行的库都不支持嵌套表单。
*   **动态变化** 很难使窗体状态动态变化。例如，如果我必须根据另一个字段的值禁用一个字段。
*   **监听器
    监听器**用于窗体值和状态的改变。
*   **更新选项** 受限制的选项，用于定义控件何时应更新其状态，即更改、模糊和提交。

# 解决办法

当我第一次在 angular 中使用反应形式时(在使用 react 之后)，我很惊讶。它超级简单&太令人印象深刻了，然后我想在 react 中应该有类似的东西。

所以，等待终于结束了。🎉

# [**React-Reactive-Forms**](https://github.com/bietkul/react-reactive-form.git)

![](img/e5d7e8ba22d7d794d211c1671d215315.png)

今天我推出了`[react-reactive-forms](https://github.com/bietkul/react-reactive-form.git)`，一个自带 [**零依赖**](https://hackernoon.com/tagged/zero-dependency) 的库，和 angular 的反应式一样强大。它允许在组件类中创建一个表单控件对象树，并将它们与本机表单控件元素绑定在一起。

它解决了 react 中当前可用表单库的大部分问题。

*   **零依赖**
    它的重量只有 **4.6k** gzipped，唯一的对等依赖是 react。❤️️
*   **UI 独立** 完全独立于 UI，你只需要使用处理程序来绑定输入元素。
*   **嵌套窗体** 它支持嵌套窗体，可以使用`Form Group`和`Form Array`API 创建嵌套控件。
*   **动态变化** 反应式表单提供了订户的值和状态变化，因此非常容易进行动态变化。你也可以随时添加&移除控件。
*   **监听器** 它为每个控件提供值和状态变化监听器。
*   **更新选项** 

# **怎么用？**

因此，来自 angular 背景的人会有宾至如归的感觉，因为所有 api 的命名约定都是相同的。

A simple form using react-reactive-form.

看看这个例子，一个复杂的用户注册表单，带有同步、异步验证器、嵌套表单和`updateOn`特性。

A complex user registration form with react-reactive-forms.

更多详细信息，请参见[文档](https://github.com/bietkul/react-reactive-form/blob/master/docs/GettingStarted.md)。

# 路标

这只是开始，更多的还在后面。
一些即将推出的功能包括:

*   值规格化器
*   更多`updateOn`选项，例如`onKeyPress`
*   你有什么好的建议。

# 结论

这个库致力于在 react 中引入 angular 的一个最好的特性，现在我正在等待来自社区的反馈和建议。

让我们让 React 反应形式更好！如果你有兴趣帮忙，欢迎并感谢所有的贡献。

感谢阅读！请试一试👏如果你喜欢这篇文章，不要忘记开始回购，我会保证更频繁的更新！🙏

[](https://github.com/bietkul/react-reactive-form) [## bietkul/react-reactive-form

### 反应-反应-形式-角状反应形式。

github.com](https://github.com/bietkul/react-reactive-form)