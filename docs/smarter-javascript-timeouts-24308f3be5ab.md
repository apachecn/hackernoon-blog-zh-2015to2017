# 更智能的 JavaScript 超时(v2)

> 原文：<https://medium.com/hackernoon/smarter-javascript-timeouts-24308f3be5ab>

## 具有清除、挂起、执行和暂停状态的计时器

![](img/97f11cb41ab49eff6bd588d0478bfaf0.png)

# 基元

如果你曾经花时间编写过 JavaScript，你可能会非常熟悉这种语言内置的一些最古老的功能，即`setTimeout()`和`clearTimeout()`。

如果您不太熟悉 JS 计时器，`setTimeout()`允许您安排一个毫秒级的延迟，过了这个时间它将触发回调函数(或其他任意代码块，尽管出于本文的目的，我们将重点关注回调)的执行。它返回一个唯一的定时器标识符，允许您在超时被触发之前通过将它传递给`clearTimeout()`来取消超时。

```
function greetWorld() {
  alert('Hello, world!');
}var timerId = setTimeout(greetWorld, 2000);
```

这是一个尽可能简单的例子。在执行`setTimeout()`语句两秒钟后，运行`greetWorld()`回调。

在这两秒钟过去之前，您可以通过拨打`clearTimeout(timerId)`取消未决呼叫。除了设置和清除超时，没有提供其他有用的功能。

# 限制

当您为简单的需求编码时，内置的功能可能就足够了，但是它的局限性还有待改进。具体来说，有些无法回答的问题你可能需要回答:

1.  我的回拨超时了吗？
2.  我的超时回调的执行是否仍处于挂起状态？
3.  我的超时回调被执行了吗？

通常，如果开发人员需要在他们的项目中回答这些问题，他们可以编写一些特定于任务的实用逻辑来帮助实现这一目标。我的方法是创建一个通用的解决方案，提供这里列举的缺失功能，而不妨碍或复杂化内置原语提供的易用性和特性。

# Timeout.js

[](https://github.com/rommelsantor/Timeout) [## Rommel santor/超时

### 超时-交互式、有状态 JS (ES6)超时接口

github.com](https://github.com/rommelsantor/Timeout) 

`npm install smart-timeout`

`Timeout`对象是一个交互式的、有状态的接口，旨在实现上述目标。使用显示模块模式，它公开了以下功能:

*   `Timeout.set(callback, delay = 0, param1, param2, ...)`
    `Timeout.set(customId, callback, delay = 0, param1, param2, ...)`
*   `Timeout.clear(key, delete = true)`
*   `Timeout.exists(key)`
*   `Timeout.pending(key)`
*   `Timeout.remaining(key)`
*   `Timeout.executed(key)`
*   `Timeout.pause(key)`
*   `Timeout.paused(key)`
*   `Timeout.resume(key)`
*   `Timeout.restart(key)`

设置新超时时，您可以选择定义一个自定义字符串标识符(`customId`)来唯一标识它。如果省略这个参数，那么`callback`本身将作为超时的唯一标识符。在每个被描述为接受`key`作为其参数的函数中，`key`代表`customId`或`callback`，无论哪个在对`Timeout.set()`的相应调用中被使用。

`Timeout.set()`返回一个函数，该函数在执行时返回一个布尔值，表明延迟时间是否已过以及回调是否已被触发。相当于调用`Timeout.executed()`。如果相同的标识符在对`Timeout.set()`的调用中重复出现，前者将在后者被添加之前被清除。(如果您有意为同一个回调设置多个并发超时，只需为每个设置一个不同的`customId`。)

`Timeout.clear()`将简单地清除与指定的`key`相关的超时(如果有这样的超时)并清除任何超时曾经存在的证据。它不返回值。

无论其`delay`是否已经过去，如果已经为指定的`key`设置了超时并且没有清除，则`Timeout.exists()`返回 true。

如果指定的`key`超时，且其`delay`尚未结束(即其`callback`尚未被触发)，则`Timeout.pending()`返回 true。

`Timeout.remaining()`【在 v2 中增加】返回执行前倒计时剩余的毫秒数。

如果指定的`key`超时且其`delay`已过(即其`callback`已被触发)，则`Timeout.executed()`返回 true。

`Timeout.pause()`【在 v2 中增加】允许您暂停尚未执行的计时器的倒计时。

`Timeout.paused()`[在 v2 中添加]如果挂起的超时当前暂停，则返回 true。

`Timeout.resume()`【在 v2 中增加】允许您恢复暂停计时器的倒计时。

`Timeout.restart()`【在 v2 中增加】允许您用原来的`delay`时间重新开始等待或暂停的计时器的倒计时。

## 基本功能

考虑到前面提供的非常简单的回调示例，这里演示了最基本的功能。(注意，本文中剩余的代码示例将使用 ES6。)

```
const didGreet = Timeout.set(greetWorld, 2000)if (Timeout.exists(greetWorld)) {// true
  console.log('greeting has been scheduled')
}if (Timeout.pending(greetWorld)) {// true
  console.log('greeting is waiting to be issued')
}// ...wait for 2 seconds to elapse...if (didGreet()) {// true
  console.log('the greeting was issued')
}// ^that is identical to calling this:
if (Timeout.executed(greetWorld)) {// true
  console.log('as I said, the greeting was issued')
}Timeout.pending(greetWorld) // false - it ranTimeout.exists(greetWorld) // true - it still existsTimeout.clear(greetWorld)Timeout.exists(greetWorld) // false - it has been cleared
```

除了使用回调作为唯一键，您还可以指定一个自定义标识符:

```
const didGreet = Timeout.set('myGreeting', greetWorld, 2000)if (Timeout.exists('myGreeting')) {// true
  console.log('greeting has been scheduled')
}// etc.
```

## 看看它的实际效果

## 节流示例

基本用法本身是有帮助的，但是让我们考虑一个更复杂的用例:抑制过多的窗口事件。在这种情况下，我们将不会使用延迟来触发超时回调，而只是作为时间跟踪器来测试指定的延迟是否已经过去。

假设我们的需求规定，每当窗口从页面顶部向下滚动任意距离时，我们都要将类`is-scrolled`添加到`<html>`元素中。问题是当窗口滚动时，大量的`scroll`事件被触发，我们不想因为对每个事件都做出反应而使页面陷入困境。这可以通过 lodash 等外部库的 throttle 函数来实现，但是对于这个例子，我们将使用`Timeout`来限制`onScroll`回调，这样它就可以定期执行。

```
const throttle =
  (delay, callback) =>
    (...args) =>
      !Timeout.pending(callback) &&
      Timeout.set(callback, () => {}, delay)
        ? callback.apply(this, args)
        : nullconst onScroll = () => {
  const isScrolled = $(window).scrollTop() > 0
  $('html').toggleClass('is-scrolled', isScrolled)
}const onScrollThrottled = throttle(100, onScroll)
$(window).scroll(onScrollThrottled)
```

`throttle()`函数接受以毫秒为单位的延迟和回调作为其两个参数，如果*自上次执行以来至少经过了*延迟，则执行回调。它返回一个适合用作事件回调的函数。

这显然是对`Timeout`对象的一个非常具体(尽管有点复杂)的使用，但是它展示了它通过简洁、清晰的操作所提供的灵活性，这是单独使用`setTimeout()`和`clearTimeout()`所不可能的。

# 结论

可能还有其他库提供类似的功能，但是，如果是这样，它们一定隐藏得很好，因为我还没有找到它们，这就是我决定写这篇文章的原因。我希望它对你有用，就像我希望的那样！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)