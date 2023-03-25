# 一个测试的好坏取决于它在必要时失败的能力

> 原文：<https://medium.com/hackernoon/a-test-is-as-good-as-its-ability-to-fail-when-it-needs-to-b4b8f212119a>

## 有时仅仅编写测试是不够的

![](img/2b27fc3ed4067c56d766b8dbfceb4d7a.png)

The picture of a notebook and a female hand writing something on it

有一种组织内部测试代码的模式叫做 [Arrange Act Assert](http://wiki.c2.com/?ArrangeActAssert) (AAA)。其结构是:

> 1.**安排**所有必要的前提条件和输入。
> 
> 2.**对[测试](https://hackernoon.com/tagged/test)下的对象或方法进行**动作。
> 
> 3.**断言**预期的结果已经出现。
> 
> —从[wiki.c2.com](http://wiki.c2.com/)安排动作断言定义

假设我们想测试一个组件。假设我们使用测试驱动的方法和基于组件的架构，实现可能是这样的:

[https://jsfiddle.net/qegb2xan/10/](https://jsfiddle.net/qegb2xan/10/)

默认情况下，模型是打开的，因为这个测试只是为了确保我们创建了一些东西并添加到 DOM 中。现在让我们创建一个测试，它将驱动我们创建关闭它的功能:

[https://jsfiddle.net/wotyLkLw/6/](https://jsfiddle.net/wotyLkLw/6/)

这个失败的测试迫使我们创建关闭模态的功能。让我们使用一个使用`display`来显示/隐藏内容的`.active`类来构建它(参见`compiledComponent`函数):

[https://jsfiddle.net/e6bvt39b/7/](https://jsfiddle.net/e6bvt39b/7/)

但是我们只能在事件发生时打开模态，它不应该默认对用户打开。让我们编辑现有的测试，以便默认情况下不打开模态(参见测试`the modal is NOT open by default`上的更改):

[https://jsfiddle.net/1ytcfx1s/4/](https://jsfiddle.net/1ytcfx1s/4/)

现在问题来了:在开发模型的某个时候，我们很可能犯了一个错误，从 DOM 中删除了关闭模型的按钮。这将破坏功能，我们预计至少会有一个测试失败(见`compiledComponent`功能，按钮已被移除):

[https://jsfiddle.net/8braft4t/3/](https://jsfiddle.net/8braft4t/3/)

然而，没有测试失败。

这是因为默认情况下，jQuery API 不会为不存在的元素抛出任何东西。它会自动失败，并且不会将目标元素添加到 jQuery 集合中。因为我们正在检查模态是关闭的，并且默认情况下是关闭的，所以测试永远不会失败，即使代码被破坏。

```
// This doesn't break, it just runs and do nothing
$('.modal').find('.close').click();
```

如果我们有一个在元素不存在的情况下失败的 API，那么在测试中就不会有假阳性:

```
document.querySelector('.modal .close').click();
```

然而，当我们使用`querySelector`时，一个不存在的元素的误差是不够描述的:

> 未捕获的类型错误:无法读取 null 的属性“click”

我们可以通过将操作包装在一个自定义函数中，用一个更清晰的消息让它失败:

```
// This wrapper throws if the element is not there
findElement($('.modal'), '.close').click();
```

> 错误:未找到选择器的元素。关闭"

[https://jsfiddle.net/4cvprc9m/2/](https://jsfiddle.net/4cvprc9m/2/)

在不应该通过测试的时候通过测试的问题是，当特定的 bug 发生时，开发人员不知道要调查哪些测试。一个通过的测试很可能被忽略，尤其是如果模态测试更复杂，并且它处于其他几个测试的中间。

> 在测试环境中，假阴性比假阳性好。如果测试失败，它会被突出显示，如果没有，它会被开发人员忽略，因为它被认为是有效的。

这是一个当[嘲笑会导致假阳性](/@fagnerbrack/mocking-can-lean-to-nondeterministic-tests-4ba8aef977a0)的同类问题。不同之处在于，我们没有嘲笑，而是使用不正确的 DOM API 进行[测试](https://hackernoon.com/tagged/testing)。

作为正式的问题陈述，我们可以说一组测试在下列情况下会导致假阳性结果

*   默认情况下某些东西是否定的(模态是封闭的)
*   测试需要一个动作(关闭模态)
*   这将我们正在测试的组件的状态更改为初始状态(模态是关闭的)

在这种情况下，我们不能保证功能真的被覆盖。即使有为此编写的测试，更改组件代码仍然可以使测试通过。

有几个选项可以防止这种情况发生:

1.  在确保模态可以被关闭的测试中，断言模态应该在关闭模态之前打开。不幸的是，这违反了 AAA，因为我们将在 Arrange 和 Act 之间有一个断言来检查模态是否是开放的。[这里有一个例子](https://jsfiddle.net/q4bnqepw/1/)。
2.  使用当元素不存在时抛出的 API。这样，如果我们从模态中移除目标元素，测试将会失败，我们将会确切地知道原因。[如上图](https://jsfiddle.net/4cvprc9m/2/)的例子。

仅仅编写测试不足以确保应用程序正常工作。您需要正确地编写它们，这样就不会发生像这样的假阳性。这意味着使用合适的 API 来实现你的目标。

单独的 jQuery 不适合这类测试。

还有你。你踩过类似的东西吗？当测试一个对象或组件的默认状态时，你有什么建议来防止这个问题发生？

感谢阅读。如果你有一些反馈，请在 Twitter、脸书或 Github 上联系我。