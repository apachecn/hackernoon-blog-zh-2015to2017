# 💅风格化的组件在运行

> 原文：<https://medium.com/hackernoon/styled-components-in-action-723852f2a93d>

Styled-Components 是一个新的 CSS 工具，由 [Max 斯托伊伯](https://medium.com/u/908fb8fea30c?source=post_page-----723852f2a93d--------------------------------)和 [Glen Maddern](https://medium.com/u/59124a41bb46?source=post_page-----723852f2a93d--------------------------------) 创建，帮助你在 [React](https://hackernoon.com/tagged/react) 项目中组织 CSS。它还可以很好地与 React Native 配合使用。在本文中，我将带您了解样式化组件的主要概念以及编码示例。

## **样式组件的三个主要目标**

首先，我们来谈谈 Styles-Components 的用途，以及使用它你将获得的好处。

1.  **消除样式和组件之间的映射**——大多数时候，一个哑组件总是有它自己的相关的小`style.css`文件。因此，每次想要创建哑组件时，都需要创建两个文件。这在开始时似乎没问题，然而，当你的项目越来越大时，你最终会得到一大堆文件。Styled-Components 允许您直接在组件内部编写 CSS，完美地解决了这个问题。
2.  **构建小而可重用的组件** —小组件可以很容易地被重用和测试。通过使用 Styled-Components，您可以轻松地构建一个小组件，并使用`props`扩展其功能。
3.  **降低特异性冲突的风险** —每个人之前都可能遇到特异性冲突问题。例如，您只想为特定段落添加边距，但这无意中影响了其他段落。您可以通过只应用一次 CSS 类来轻松解决这个问题。Styled-Components 实际上正在为我们做这件事。它会自动生成一个惟一的类名，并将其传递给我们的组件。

# 让我们把手弄脏吧

是时候看看它是如何工作的了！您将创建一个非常简单的图库应用程序，姑且称之为`Mystagram`。

![](img/516cbbc01ede85f352428085a823825d.png)

**Mystagram — styled-components tutorial**

如果你想跟随这个教程，这是真正推荐的，请克隆[样式-组件-教程](https://github.com/lvarayut/styled-components-tutorial)项目:

```
$ git clone [https://github.com/lvarayut/styled-components-tutorial](https://github.com/lvarayut/styled-components-tutorial)
$ cd [styled-components-tutorial](https://github.com/lvarayut/styled-components-tutorial)
$ git checkout scratch
```

安装所有依赖项并开始开发

```
$ yarn install
$ yarn start
```

## **可重复使用的标题组件**

让我们从使用`h1`标签的简单的`Mystagram`标题开始。

您刚刚创建了带有一枝黄花颜色的`Title`组件。需要时，您可以轻松地重用该组件。注意我们是如何在我们的 [JavaScript](https://hackernoon.com/tagged/javascript) 文件中用反勾号包装完整的 CSS 的。这是 ES6，又名 ES2015 中的一个新功能，称为[模板文字符号](https://www.google.de/url?sa=t&rct=j&q=&esrc=s&source=web&cd=1&cad=rja&uact=8&ved=0ahUKEwjxlbiZsKzTAhXDkywKHWQzDdkQFgglMAA&url=https%3A%2F%2Fdeveloper.mozilla.org%2Fen%2Fdocs%2FWeb%2FJavaScript%2FReference%2FTemplate_literals&usg=AFQjCNFQQVqrp3g0gIiAquF4iP90l1yY4g&sig2=Wkb84o4cH-i3D-qSXHEkPQ)。

> 正如您从上一节中了解到的，样式化组件自动生成一个惟一的类名，并将其传递给我们的组件。这是最后的结果`<h1 class="4fKfRK">Mystagram</h1>`，你可以打开你的 inspect 元素自己看看！

现在，你有了`Title`组件，但是，它不太可重用，因为颜色是硬编码的。如果组件可以接收属性并动态设置颜色会怎么样。绝对可以！

您只是修改了`Title`组件来接受`color`属性。然后我们可以传递一个可爱的颜色给组件，否则颜色将被默认设置为`goldenrod`。

## **第三方组件**

到目前为止，您已经看到了如何设计一个原始元素`h1`的样式。您可能会问，如果我们已经有了一个组件，可能来自第三方库或您自己的库，并且想要对它进行样式化，该怎么办？这里我们将设计我们的`App`组件。

`styled`接受组件作为参数。您刚刚用`width`、`height`和`text-align`属性设计了应用程序组件。我们也给`Title`组件添加了更多的样式，让它看起来更漂亮。

> 注意，当您设计一个组件的样式时，您需要确保您的组件将`this.props.className`附加到它的 DOM 上。因为在 Style-Components 生成一个惟一的类名之后，它会把这个类名传递给你的组件。如果您不确定第三方组件是否有`this.props.className`，您可以简单地将它包装在您自己的`<div className={this.props.className}>`标签中。

## **全局风格**

我们有时需要为整个应用程序设置一个全局样式，比如`font-family`、`background-color`等等。Style-Components 提供了一个`injectGlobal`函数来帮助我们进行全局样式化。

## **图库和缩略图组件**

让我们通过创建图库和缩略图组件来更熟悉样式化组件。

您刚刚创建了一个包装了五个缩略图组件的图库。

## **可选缩略图边框**

同样，我们可能不需要一直显示缩略图边框。我们需要一种能力来说，哪个缩略图应该有边界。让我们创建一个名为`showBorder`的属性。

为了简单起见，我们显示了索引为偶数的缩略图的边框。

## 使用“css”重用样式

想象一下，如果您需要在其他地方重用边框样式，将样式分离到它自己的文件或变量中是一个好主意。你可以通过使用`css`功能来做到这一点。

> 如果你用一个普通的字符串声明了`border`变量，而没有使用`css`，那么 props 将不起作用，因为样式化组件将通过使用`toString()`函数包含你的样式，而没有任何**字符串插值**特性

# 包扎

我们刚刚构建了一个简单的应用程序，它为我们提供了样式组件的概览。现在，您已经了解了样式组件的主要用途，以及如何在自己的应用程序中使用它。还有一些函数在本文中没有涉及到，例如`keyframes`和`ThemeProvider`，但是在你理解了主要概念之后，它们就非常容易理解了。我鼓励你去看看；-)

*   [styled-components](https://github.com/styled-components/styled-components)repository——有非常好的所有功能和用例的文档。
*   [styled-components](https://mxstbr.blog/2016/11/styled-components-magic-explained/)背后的魔力——解释`styled-components`如何在幕后使用模板文字符号。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)

[https://upscri.be/hackernoon/](https://upscri.be/hackernoon/)