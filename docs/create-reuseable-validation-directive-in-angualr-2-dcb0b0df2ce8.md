# 创建在带角度的表单中显示错误消息的指令

> 原文：<https://medium.com/hackernoon/create-reuseable-validation-directive-in-angualr-2-dcb0b0df2ce8>

![](img/c3c37f9c19734131c814986337382850.png)

表单和验证是网站的基础。Angular 提供了一种简单方法来处理表单中的验证，但是我们仍然可以用指令来简化更多的验证工作。

在这篇文章中，我将向你展示如何轻松地显示没有*ngIf 和长表达式的验证错误消息。

我假设你已经熟悉了 Angular，如果你不知道的话，我建议你读一下这些主题。

*   [结构指令](https://angular.io/docs/ts/latest/guide/structural-directives.html)
*   [反应形式](https://toddmotto.com/angular-2-forms-reactive)

## **验证中的问题。**

向用户显示消息会使我们的代码看起来很难看。例如

*在添加验证消息之前*

*添加验证信息后*

呃。现在太乱了。😵

所以我试图用指令魔法来消除重复的代码。这是我的成就。

看起来干净多了。😻

这是通过两条指令实现的。**无效消息**和**无效类型。**

# **无效消息指令**

**invalidmessage** 指令通过名称接收表单控件，并根据表单控件的错误设置托管元素的可见性。

下面是 **invalidmessage** 指令的代码。

别担心，我很快会解释代码。

**invalidmessage** directive

首先，我从最近的父注入器(在上面的例子中是`myForm`)注入`ControlContainer`来获取`Formgroup`实例。

get FormGroup with getter(excerpt from **invalidmessage** directive)

现在我们可以很容易地通过名字来控制(例如，`this.form.get('yourcontrolname')`)。😎

FormControl 实例可以获取值、错误等。实际上这只是你在反应形态中使用的形态控制。例如，`this.myform.get('name');`

我创建了`setVisible()`方法，用于在控件有效时设置托管元素 visble，在无效时隐藏。

set visble based on control error (excerpt from **invalidmessage** directive)

一开始显示错误信息是很糟糕的(很多用户体验网站都这么说)，所以如果它只是在用户与控件交互或点击提交按钮时显示，会让用户不那么生气。我用上面的`this.control.dirty`和`this.hasSubmitted`来检查。

`setVisible()`当控件的值被更改并且用户单击提交按钮时，将调用方法。

setVisible will be caledd when controlValue$ emit (excerpt from **invalidmessage** directive)

*通知* `*Observable.of*` *，它会立即发出，所以* `*setVisible()*` *会在初始时被调用。*

提交表单时发出的`formSubmit$`是可观察的。它会将`hasSubmitted`设置为`true`。

**invalidmessage** 为 **invalidType** 指令提供`match`方法，以调用检查是否匹配控件的错误类型。

match take argument which it’s error type and check if control has that error or not(excerpt from **invalidmessage** directive)

`this.control.errors`是属性名为错误类型的对象。比如说，表单控件需要电子邮件验证，而用户破坏了它。*form control*对象会这样。

```
{
   required:true,
   email:true
}
```

您将在下一节看到如何使用它。

# 无效的类型指令

该指令用于根据 **invalidmessage 指令中定义的控件的错误类型设置主机元素的可见性。**

在上面的例子中，你可能注意到了`***invalidmessage**` 。有星号前缀。它叫做[结构指令。](https://angular.io/docs/ts/latest/guide/structural-directives.html)简而言之，Stuctural 指令将在主机元素周围创建`ng-template`,使得主机元素直到你用`ViewContainerRef`显式渲染后才会被渲染

让代码自己解释

**invalidType** directive set visible error message based on control error

该指令被注入了 3 个依赖项——**InvalidmessageDirective**、 **TemplateRef** 和 **ViewContainerRef** 。

**TemplateRef** 和 **ViewContainerRef** 用于创建和移除主机元素(这是错误消息)。

**的角度注入实例 InvalidmessageDirective** 离父注入器最近。

```
<div invalidmessage="name">
        <p *invalidType="'required'">Please provide name</p></div>
```

**InvalidTypeDirective** 在`<p>`上，通过在 invalidTypeDirective 中注入 **InvalidmessageDirective，可以得到**invalid message directive(**在`<div>`上)的实例。**

所以 **invalidType** 指令可以使用 **invalidmessage 指令**实例**的属性和方法。**

setVisible() for set visible of host element based on invaludmessage’s match medthod (excerpt from **invalidType** directive)

**invalidmessage 的 match** 方法通过参数获取错误类型(用`type`属性定义)并检查错误对象是否具有指定的错误类型。

如果 **invalidmessage 的匹配**返回 true，*视图容器*将从`TemplateRef`创建视图(它是`ng-template`围绕的托管元素)，并且*视图容器*将在它为 false 时清除。通俗地说， **invalidType** 指令根据 **invalidmessage 的 match** 方法的返回值显示和移除主机元素。

*需要通过检查* `*hasView*` *属性来检查视图容器是否还没有创建，这样就不会重复创建。*

setVisible() is called when invalidmessage’s controlValue$ emit (excerpt from **invalidType** directive)

`setVisible()` 在 **invalidmessage 的 controlValue$** 发出时调用(控件值改变，用户点击提交按钮)。

仅此而已。这两条指令可以简化我们的表单验证。它也许并不完美，但它可以给你一个想法。

如果您有任何问题或建议，请在下面评论。

这是扑通一声

 [## 扑通一声

### Plunker 是一个在线社区，用于创建、协作和分享您的 web 开发想法。

plnkr.co](https://plnkr.co/edit/JIwB1WeftdEfa0G2vRze) 

*如果你喜欢这个帖子，请留下一个喜欢的* ❤️ *并关注我在* [*中*](/@Elec_crafter) *或* [*推特*](https://twitter.com/Elec_crafter) *上阅读更多关于 Angular、Javascript 等内容。*

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)