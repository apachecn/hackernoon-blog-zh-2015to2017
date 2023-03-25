# 我如何使用代理在 JavaScript 中实现惰性无限列表

> 原文：<https://medium.com/hackernoon/how-i-used-proxy-to-implement-lazy-infinite-lists-in-javascript-5e86879b44d6>

在本文中，我将展示一个延迟评估的[链表](https://en.wikipedia.org/wiki/Linked_list)数据结构的 JavaScript ES6 实现。递归定义的链表是许多函数式[编程](https://hackernoon.com/tagged/programming)语言中重要的集合类型。在启发了这个项目的 [Haskell 语言](https://haskell-lang.org/)中，[列表数据类型](https://en.wikibooks.org/wiki/Haskell/Lists_and_tuples)填充了数组在 [JavaScript](https://hackernoon.com/tagged/javascript) 中所扮演的角色。因为这个列表类型是使用[递归](https://en.wikipedia.org/wiki/Recursion_(computer_science))定义的，所以可以按需生成和评估列表元素，而不是在列表创建时(这是“懒惰”的部分)，从而使得无限列表不仅可行，而且相对简单。

我依靠来自 [ES2015](http://www.ecma-international.org/ecma-262/6.0/) (又名 ES6)的新`[Proxy](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Proxy)` API 作为一种间接方式，在 JavaScript 中生成有限和无限范围列表。[生成器](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Generator)也在这里出现，所以这是一个很好的机会来探索和实际使用该语言的一些最新扩展，同时学习更多关于函数式编程的知识(我在之前的文章中更明确地讨论了这个主题)。这里展示的代码改编自我的 [**懒惰链表**](https://www.npmjs.com/package/lazy-linked-lists) npm 模块。完整的库可以在 [GitHub](https://github.com/sjsyrek/lazy-linked-lists) 上开源获得，而且，由于我们正在探索懒惰，我将本文中专门使用的示例代码放在一个单独的 [GitHub Gist](https://gist.github.com/sjsyrek/3fb2fa03797ae0e89426b484ca8a6dcf) 中。

如果你还没有看`Proxy`，它将会颠覆你的世界。这是不是一件好事取决于你的观点。和它的对手`[Reflect](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Reflect)`，`Proxy`一起，带来了全范围的[元编程](https://en.wikipedia.org/wiki/Metaprogramming)能力，承载着人类最多产的编程语言。顾名思义，*元编程*需要编写能够自我检查和修改的代码。换句话说，它是抽象层次中“上一层”的编程，或者说是编程本身的编程。

函数式程序员可能会认为这种语义上的混乱是一种诅咒，这仅仅是语言设计失败的可能证据。如果函数式编程最大的优点是引用透明，那么`Proxy`就可以任意混淆。尽管以前版本的 JavaScript 允许您在一定程度上进行元编程，但 ES2015 规范通过使其成为一等公民而将这种能力置于前沿和中心。不管这是不是编程语言*应该*拥有的能力(例如 [Brendan Eich](https://en.wikipedia.org/wiki/Brendan_Eich) 认为[代理很棒](https://www.youtube.com/watch?v=sClk6aB_CPk))，讽刺的是，它给了我们这些来自函数世界的人一些额外的能力，可以更容易地将我们最喜欢的语言习惯用法嵌入 JavaScript。

`Proxy` API 提供了拦截正在运行的应用程序的普通进程的方法。这是通过将一个目标对象与*代表其*的另一个对象相关联来实现的，即作为*代理*。反过来，代理对象通过在指定的“处理程序”对象上定义的方法或“陷阱”来过滤目标对象上的传入操作。例如，您可以*捕捉*试图`get`一个对象的属性值:

```
**class** Secret {
  **constructor**(msg) { **this**.info = msg }
}

**let** handler = {
  get: (target, prop) =>
    prop === "info" ? "Too many secrets" : target[prop]
}

**let** makeSecret = msg => **new** Proxy(**new** Secret(msg), handler)

**let** s1 = **new** Secret("Confidential information")**let** s2 = makeSecret("More confidential information")

s1.info *// "Confidential information"*s2.info *// "Too many secrets"*
```

在这个简单的例子中，我们有一个名为`Secret`的小包装器类，作为我们可能用来隐藏敏感信息的对象的原型。我们可以提供一个定制的构造函数，而不是将类本身暴露给 API 的用户。这样做允许我们定制`Secret`在特定环境下的行为方式。这里，`makeSecret`函数并不返回新的`Secret`对象本身，而是返回一个代理对象来代表它。可以说，所有指向给定`Secret`的“消息”都将首先通过各自的代理。我们可以捕获所有、部分或不捕获这些消息。`Secret`代理仅捕获试图读取或`get`属性`info`的值。事实上，所有读取`Secret`属性的尝试都会被拦截，因为 handler 对象定义了一个`get`方法。但是该方法的逻辑只干涉获取`info`属性的请求。代理没有返回那个值，而是返回一个规定的缺省值(这个值是 20 世纪 90 年代间谍活动的狂热爱好者所钟爱的)。当然，你会注意到，这并不是一个完全可靠的数据隐藏机制，但是一个更有野心的程序，也许是一个实现了[膜](http://soft.vub.ac.be/~tvcutsem/invokedynamic/js-membranes)的程序，当然可以使用`Proxy`来实现这个目的。

我们可以应用同样的技术为任意长度的链表创建一个函数接口。既然我们在 ES6 领域，让我们从为列表类型定义一个简单的类开始:

```
**class** List {
  **constructor** (x, xs) {
    **this**.head = () => x
    **this**.tail = () => xs
  }
}
```

这将是一个相当简单的函数数据结构。`List`的构造函数将一个值`x`作为一个新列表对象的第一个元素或头部，将另一个列表`xs`作为尾部，或*作为列表*的其余部分。如果您熟悉链表的底层实现，您可以将 list 对象视为一个由两个单元组成的节点:头部是节点的值(或标签)，尾部是指向下一个节点的指针。任何给定列表中的最后一个值总是空列表。`head`和`tail`属性被定义为返回函数而不是原始值，这与函数范式一致，也是对篡改的一种轻微保护。想象它们不是空函数，而是应用于它们的参数的等价恒等函数(如果你要参数化那个恒等函数，你会有一个[单子](http://stackoverflow.com/questions/44965/what-is-a-monad)，但是我保证我不会在本文中讨论单子)。

事实上，整个[类声明](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/class)只是一种特殊的函数。尽管关于 OOP 概念的有用性还有争论，但是 ES6 类确实拥有一个我们可以利用的显著属性:你不能直接调用它们作为函数。定义为类的对象只能用`new`关键字构造；然后，类`constructor`函数确保`new`做正确的事情。尽管关于`new`本身的适当性还有争论，但是这个属性允许我们隐藏类声明(例如，通过不导出它)并提供我们自己的定制构造函数。在某种程度上，我们使用了 JavaScript 中最有争议的语法。下面是我们*想要导出的接口所需的代码:*

```
**const** emptyList = **new** List()

**const** isEmpty = xs => xs === emptyList

**const** cons = (x, xs) => **new** List(x, xs)

**const** list = (...as) =>
  as.length === 0 ? emptyList : **new** List(as.shift(), list(...as))

**const** head = xs => {
  **if** (isEmpty(xs)) { **throw** Error('EmptyListError') }
  **return** xs.head()
}

**const** tail = xs => {
  **if** (isEmpty(xs)) { **throw** Error('EmptyListError') }
  **return** xs.tail()
}
```

在 Haskell 中，列表是一个需要两个构造函数的 [sum 类型](https://www.schoolofhaskell.com/school/to-infinity-and-beyond/pick-of-the-week/sum-types):一个创建空列表，另一个递归地从一个值(头部)和“更多列表”(尾部)创建一个新列表。因为 JavaScript 没有 sum 类型，所以上面的实现有点草率。但这很有效。对象只是一个符号占位符。它不向`List`传递任何参数，所以空列表的头部和尾部都是`undefined`。既然我们无论如何都不希望直接调用`List`构造函数，这没问题:在我们的程序中，所有情况下只有一个空列表，它被构造一次且只有一次，它就是`emptyList`。我们还提供了一个检查列表是否为空的函数，这是递归和基本情况下经常需要的。

另一个构造者是`cons`。`cons`和它包装的`List`类一样， *cons* 从一个值`x`和另一个列表`xs`构造一个新列表。`x`前置到`xs`上。如果`xs`是空列表，那么`cons`简单地创建一个新的单元素列表。显然，这里有滥用的机会，更全面的`cons`实现将验证甚至可能类型检查`x`。你也可以创建一个[咖喱版本](https://github.com/sjsyrek/maryamyriameliamurphies.js/tree/master/source/list)，并使其真正功能化，但出于解释的目的，我试图尽可能省略掉更多的噪音。

这里的`list`函数是为了方便，因为我们不能为声明多个元素的列表创建自己的语法糖。`list`获取零个或多个参数，并递归地使用它们，直到参数数组(由 rest 操作符干净地提供，另一个 ES6 特性)为空，此时它到达`emptyList`作为基本情况。函数调用`list(1,2,3)`相当于`cons(1, cons(2, cons(3, emptyList)))`。`head`和`tail`是那些各自属性的存取函数。正如在 [Haskell Prelude](https://hackage.haskell.org/package/base-4.9.0.0/docs/Prelude.html) 中一样，它们是[部分函数](https://wiki.haskell.org/Partial_functions)，这并不理想，但同样，它们将足以用于演示目的。

因为 JavaScript 只是将我们的列表当作对象，所以我们想要一种很好地打印列表值的方法。所以让我们添加一个定制的`valueOf`方法:

```
**class** List {
  **constructor**(x, xs) {
    **this**.head = null
    **this**.tail = null
    **this**.head = () => x
    **this**.tail = () => xs
  }
  valueOf() {
    **let** value = xs =>
      isEmpty(xs) ? `[]` : `${head(xs)}:${value(tail(xs))}`
    **return** isEmpty(**this**) ? `[]` : `[${value(**this**)}]`
  }
}
```

现在，当我们显示列表值时，我们得到了一些相当清晰的东西:

```
**let** lst1 = list(1,2,3,4,5)lst1.valueOf() *// [1:2:3:4:5:[]]*

**let** lst2 = cons(0, lst1)lst2.valueOf() *// [0:1:2:3:4:5:[]]* 
**let** lst3 = cons(0, cons(1, cons(2, cons(3, cons(4, cons(5, emptyList))))))lst3.valueOf() *// [0:1:2:3:4:5:[]]*

head(lst1) *// 1*tail(lst2).valueOf() *// [1:2:3:4:5:[]]*isEmpty(tail(tail(tail(tail(tail(lst1)))))) *// true*
```

我使用冒号而不是逗号来避免与数组混淆，并使它的结构更明显和更 Haskell-y，但显然您可以按您想要的方式打印列表值(并查看: [ES6 模板文字](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Template_literals)！).在这些例子中使用`valueOf`只有在你的 REPL 默认不打印对象属性时才有必要(当[节点](https://nodejs.org/dist/latest-v7.x/docs/api/repl.html#repl_default_evaluation)可以打印时，它会打印，但浏览器控制台不会)。

ES6 现在让我们实现的另一个很好的便利是使用生成器和所谓的"[众所周知的符号](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Symbol)，"`Symbol.iterator`:

```
**class** List {
  **constructor**(x, xs) {
    **this**.head = null
    **this**.tail = null
    **this**.head = () => x
    **this**.tail = () => xs
  }
  [Symbol.iterator]() {
    **const** listIterator = **function*** (xs) {
      **do** {
        **yield** head(xs)
        xs = tail(xs)
      } **while** (xs !== emptyList)
    }
    **const** gen = listIterator(**this**)
    **return** {
      next() { **return** gen.next() }
    }
  }
  valueOf() {
    **let** value = xs =>
      isEmpty(xs) ? `[]` : `${head(xs)}:${value(tail(xs))}`
    **return** isEmpty(**this**) ? `[]` : `[${value(**this**)}]`
  }
}
```

现在我们可以像使用数组一样使用列表——如果出于某种原因我们想使用`for`循环或者对可迭代对象有其他需求:

```
**let** lst = list(1,2,3,4,5)**for** (**let** value **of** lst) { console.log(value); }
*// 1
// 2
// 3
// 4
// 5*
```

下一步是通过范围列表表达式重新创建 Haskell 中提供的功能。例如，Haskell 表达式`[1..10]`是构建列表`[1,2,3,4,5,6,7,8,9,10]`的快捷方式。同样，Haskell 也可以推断出当您键入`[1,3..10]`时，您的意思是`[1,3,5,7,9]`。让我们看看在 JavaScript 中我们能做到什么程度。首先，我们将定义一个递归构造连续整数列表的函数:

```
**const** listRange = (start, end) => {
  **if** (start === end) { **return** list(start) }
  **if** (start > end) { **return** listRangeBy(start, end, x => x - 1) }
  **return** listRangeBy(start, end, x => x + 1)
}
```

我们给这个函数两个值`start`和`end`，它返回一个它们之间的数字列表。注意，与 Haskell 不同，结果列表包含起始值，但不包含结束值(如在 Python 中)；这个函数只对数字起作用(然而，设计一个字符版本不会太难——参见[我的库](https://github.com/sjsyrek/lazy-linked-lists))；并且如果`start`大于`end`，则结果列表*向下计数*而不是向上计数:

```
listRange(1,10).valueOf() *// [1:2:3:4:5:6:7:8:9:[]]*listRange(10,1).valueOf() *// [10:9:8:7:6:5:4:3:2:[]]*
```

眼尖的人会注意到，这个函数本身实际上并没有做多少事情。相反，它将列表构造本身的责任交给了另一个更通用的函数:`listRangeBy`。该函数也创建了一个范围列表，但是参数化了用于增加值的“step”函数，使其成为`listRange`的更一般的情况，并且使简单地根据另一个定义一个变得明智。最后，在`listRangeBy`中，我们见到了这件事的核心代理人:

```
**const** listRangeBy = (start, end, step) => {
  **if** (start === end) { **return** list(start) }
  **let** x = start
  **const** xs = list(x)
  **const** listGenerator = **function***() {
    x = step(x)
    **while** (start < end ? x < end : x > end) {
      **yield** list(x)
      x = step(x)
    }
  }
  **const** gen = listGenerator()
  **const** handler = {
    get: **function**(target, prop) {
      **if** (prop === `tail` && isEmpty(tail(target))) {
        **const** next = gen.next()
        **if** (next.done === false) {
          target[prop] = () => **new** Proxy(next.value, handler)
        }
      }
      **return** target[prop]
    }
  }
  **const** proxy = **new** Proxy(xs, handler)
  **return** proxy
}
```

这个函数有点奇怪，对于函数式程序员来说太强制性了，但是它完成了任务。首先，它接受`start`值，并用它创建一个单例列表。接下来，它声明一个内部生成器函数，为参数化的`step`函数定制，该函数将`yield`后续的单例列表，直到它命中`end`。这些新列表在生成时是由`listRangeBy`函数返回的“列表”(实际上是一个代理对象)的“惰性”尾部。头部是该特定节点的评估值，但是列表的其余部分被认为是“空的”，直到对更多列表的进一步调用触发后续评估。尾部的每个元素都是按需一次生成一个，这就是为什么我们可以说这些范围列表是延迟求值的。

接下来是`handler`对象。这个特殊的处理程序，如上面的`Secret`示例，捕获所有试图`get`目标`List`上定义的属性值的尝试。在这种情况下，我们只对`tail`地产感兴趣。当请求“目标”列表的`tail`的值时，处理程序的`get`方法调用生成器函数，而不是生成列表的整个剩余部分，该函数只生成下一个`head`值——打包在另一个单例列表中——并返回一个新的`Proxy`对象，该对象“定位”新生成的单例列表。这个新列表的`tail`仍然是`emptyList`，尽管只要生成器能够产生新值，它就不再是了。但是我们假装这是代理处理器逻辑的一部分。在我们的惰性列表世界中，处理程序计算的任何给定的尾部要么是单例列表，要么是已经部分或完全计算过的列表。在单例列表的情况下，处理程序开始创建一系列代理，这些代理继续捕获调用以评估这些嵌套的尾部，直到列表被完全评估或者不再请求更多的值。

这是可行的，因为列表尾部是递归计算的。任何在列表中递归的函数最终都会在它的基本用例中遇到`emptyList`。对于那些我们定义为惰性的列表，如果列表的尾部为空，代理处理程序会检查生成器是否能产生另一个值。如果可以，它将返回一个新的代理，目标是一个以该值为头的新列表。如果不能，它正常返回`emptyList`,这意味着原始列表已经被完全评估，对其尾部的后续调用将不受干扰地通过代理。在列表已经被部分求值的情况下，处理程序代码被跳过，直到需要生成更多的值——直到，也就是说，目标列表的尾部等于`emptyList`。完全求值的列表的行为与任何其他`List`对象相同。

最后，为了让整个事情顺利进行，`listRangeBy`创建了一个代理对象`proxy`，它指向最初的单例列表`xs`(我们的`start`值的包装器)并返回`proxy`，列表行头部的过滤器，代替了一个“普通的”`List`对象。我们现在已经抽象了懒惰评估。

它起作用了:

```
**let** eagerList = list(1,2,3,4,5,6,7,8,9,10)eagerList.valueOf() *// [1:2:3:4:5:6:7:8:9:10:[]]*

**let** lazyList = listRangeBy(1,100000, x => x + 3)lazyList *// List { head: [Function], tail: [Function] }*lazyList.valueOf() *// RangeError: Maximum call stack size exceeded*
```

不幸的是，在[适当的尾部调用](https://github.com/tc39/proposal-ptc-syntax)最终进入 [JavaScript 引擎](http://kangax.github.io/compat-table/es6/)之前，当你试图执行某些操作时，极长的列表很可能会破坏你的堆栈框架。说到极长的列表…通过使用 JavaScript 的便利的`Infinity`全局变量，我们现在可以实现类似的函数来方便地构造*无限*长的惰性列表:

```
**const** listInf = start => listInfBy(start, x => x + 1)

**const** listInfBy = (start, step) => listRangeBy(start, Infinity, step)
```

因为无限列表是这个小项目的亮点，所以让我们定义几个更基本的列表函数来更好地说明它们是如何工作的:

```
**const** length = xs => {
  **const** lenAcc = (xs, n) =>
    isEmpty(xs) ? n : lenAcc(tail(xs), n + 1)
  **return** lenAcc(xs, 0)
}

**const** index = (as, n) => {
  **if** (n < 0 || isEmpty(as)) { **throw** Error('OutOfRangeError') }
  **const** x = head(as)
  **const** xs = tail(as)
  **if** (n === 0) { **return** x }
  **return** index(xs, n - 1)
}

**const** listAppend = (xs, ys) => {
  **if** (isEmpty(xs)) { **return** ys }
  **if** (isEmpty(ys)) { **return** xs }
  **return** cons(head(xs), listAppend(tail(xs), ys))
}

**const** take = (n, as) => {
  **if** (n <= 0) { **return** emptyList }
  **if** (isEmpty(as)) { **return** emptyList }
  **const** x = head(as)
  **const** xs = tail(as)
  **return** cons(x, take(n - 1, xs))
}

**const** drop = (n, as) => {
  **if** (n <= 0) { **return** as }
  **if** (isEmpty(as)) { **return** emptyList }
  **const** xs = tail(as)
  **return** drop(n - 1, xs)
}

**const** map = (f, as) => {
  **if** (isEmpty(as)) { **return** emptyList }
  **const** x = f(head(as))
  **const** xs = tail(as)
  **return** cons(x, map(f, xs))
}
```

`length`和`index`类似于它们基于数组的对应物。`listAppend`类似于 JavaScript 中的`concat`(而 Haskell 中的`concat`采用多维列表并将其扁平化)。`take`从一个列表中抓取第一个`n`元素，并在一个新列表中返回它们。`drop`删除第一个`n`元素，返回剩下的。毫不奇怪，`map`将函数`f`应用于列表`as`中的每个元素。这些函数既适用于急切求值的列表，也适用于延迟求值的列表，包括无限列表，尽管有些函数显然不适用于后者(并且可能会在您的 REPL 中导致一些奇怪的行为):

```
**let** eagerList = list(1,2,3,4,5,6,7)**let** lazyList = listRangeBy(1,1000, x => x * 2)**let** infiniteList = listInf(1)

head(eagerList) *// 1*head(lazyList) *// 1*head(infiniteList) *// 1*

tail(eagerList).valueOf() *// [2:3:4:5:6:7:8:9:10:[]]*tail(lazyList).valueOf() *// [2:4:8:16:32:64:128:256:512:[]]*tail(infiniteList).valueOf()
*// RangeError: Maximum call stack size exceeded* 
length(eagerList) *// 7*length(lazyList) *// 10*length(infiniteList) *// RangeError: Maximum call stack size exceeded*

index(eagerList, 3) *// 4*index(lazyList, 9) *// 512*index(infiniteList, 10000) *// 10001*

listAppend(eagerList, lazyList).valueOf()
*// [1:2:3:4:5:6:7:1:2:4:8:16:32:64:128:256:512:[]]*listAppend(lazyList, infiniteList).valueOf()
*// List { head: [Function], tail: [Function] }*index(listAppend(lazyList, infiniteList), 100) *// 91*listAppend(infiniteList, eagerList).valueOf()
*// RangeError: Maximum call stack size exceeded* 
take(5, eagerList).valueOf() *// [1:2:3:4:5:[]]*take(5, lazyList).valueOf() *// [1:2:4:8:16:[]]*take(5, infiniteList).valueOf() *// [1:2:3:4:5:[]]*

drop(5, eagerList).valueOf() *// [6:7:[]]*drop(5, lazyList).valueOf() *// [32:64:128:256:512:[]]*drop(5, infiniteList).valueOf()
*// RangeError: Maximum call stack size exceeded*

**let** f = x => x * 10map(f, eagerList).valueOf() *// [10:20:30:40:50:60:70:[]]*map(f, lazyList).valueOf()
*// [10:20:40:80:160:320:640:1280:2560:5120:[]]*map(f, infiniteList).valueOf()
*// RangeError: Maximum call stack size exceeded*map(f, take(10, infiniteList)).valueOf()
*// [10:20:30:40:50:60:70:80:90:100:[]]*
```

在最后一个例子中，我们不能执行惰性映射(虽然原则上是可能的)，但是我们*可以用另一个函数*`map`组合*产生一个有限列表并映射到其上。*

*当我们将 Haskell 函数翻译成 JavaScript 时，为什么不导入基本函数[来生成其他类型的无限列表呢？当您需要处理价值流时，这些会很方便:](https://hackage.haskell.org/package/base-4.9.0.0/docs/Data-List.html#g:8)*

```
***const** iterate = (f, x) => listInfBy(x, x => f(x))

**const** repeat = a => cons(a, listInfBy(a, a => a))

**const** replicate = (n, x) => take(n, repeat(x))*
```

*`iterate`将给定函数`f`反复应用于值`x`。`repeat`返回相同值的无限列表。`replicate`简单地返回一个值为`x`的`n`元素列表。还有一个函数`cycle`值得特别一提，因为它需要一个特殊的实现。`cycle`取一个列表，无限重复。然而，我们的常规`listRangeBy`函数不能处理这种情况，所以我们必须为`cycle`提供一个稍微改变的版本:*

```
***const** cycle = as => {
  **if** (isEmpty(as)) { **throw** Error('EmptyListError') }
  **let** x = head(as)
  **let** xs = tail(as)
  **const** c = list(x)
  **const** listGenerator = **function*** () {
    **do** {
      x = isEmpty(xs) ? head(as) : head(xs)
      xs = isEmpty(xs) ? tail(as) : tail(xs)
      **yield** list(x)
    } **while** (true)
  }
  **const** gen = listGenerator()
  **const** handler = {
    get: **function** (target, prop) {
      **if** (prop === `tail` && isEmpty(tail(target))) {
        **const** next = gen.next()
        target[prop] = () => **new** Proxy(next.value, handler)
      }
      **return** target[prop]
    }
  }
  **const** proxy = **new** Proxy(c, handler)
  **return** proxy
}*
```

*JavaScript 就像生活一样，有时候有点乱。让我们看看这些函数的作用:*

```
***let** lst1 = iterate(x => x + 2, 0)take(10, lst1).valueOf() *// [0:2:4:6:8:10:12:14:16:18:[]]* 
**let** lst2 = repeat(7)take(20, lst2).valueOf()
*// [7:7:7:7:7:7:7:7:7:7:7:7:7:7:7:7:7:7:7:7:[]]*

**let** lst3 = replicate(5, 23) lst3.valueOf() *// [23:23:23:23:23:[]]*

**let** lst4 = cycle(list(5,4,3,2,1))index(ls4, 5) *// 5*index(lst4, 10) *// 5*index(lst4, 100) *// 5*index(lst4, 104) *// 1**
```

*如果我们愿意，我们可以使用无限列表来创建更加专门化的流，或者作为众所周知的算法的实现的一部分:*

```
***const** booleans = cycle(list(false, true))

**const** fib = n => n < 2 ? n : fib(n - 1) + fib(n - 2)
**const** fibs = n => map(fib, take(n, listInf(1)))

**const** fact = n => n === 1 ? n : fact(n - 1) * n
**const** facts = n => map(fact, take(n, listInf(1)))

**const** sqrt = (a0, eps, n) => {
  **const** within = (eps, rest) => {
    **let** a = index(rest, 0)
    **let** b = index(rest, 1)
    **return** Math.abs(a - b) <= eps ? b : within(eps, drop(1, rest))
  }
  **const** next = (n, x) => (x + n / x) / 2
  **return** within(eps, iterate(next.bind(null, n), a0))
}

index(booleans, 20) *// false*index(booleans, 10001) *// true*

index(fibs(10), 9) *// 55*facts(5).valueOf() *// [1:2:6:24:120:[]]*

sqrt(1,0,2) *// 1.414213562373095*sqrt(1,0,144) *// 144**
```

*作为偶数或奇数的过滤器，或者如果您正在创建需要交替模式的其他东西(例如表格行的阴影)，交替布尔的无限列表可能会很方便。有了一个值流，您可以根据需要获取任意数量的值，而不必编写计算它们的逻辑代码。斐波那契和阶乘的例子，除了是有史以来最著名的递归例子外，还展示了如何使用无限列表从算法上产生自定义的值序列。这里显示的`sqrt`函数使用一个无限列表来计算一个数`n`的平方根，有一个容差`eps`，从一个初始估计值`a0`开始。这个计算平方根的[牛顿-拉夫森方法](http://mathworld.wolfram.com/NewtonsMethod.html)的例子改编自约翰·休斯的[为什么函数式编程很重要](http://www.cse.chalmers.se/~rjmh/Papers/whyfp.pdf)——确实是必读书。*

*现在你有了:JavaScript 中懒惰的、函数式的、无限的列表，你可以对它们执行各种操作！再次，请查看 GitHub 上的[我的库，了解更多的`List`函数，包括有条件地获取和删除元素的函数、过滤列表、排序列表、压缩列表等等。完整的类定义还提供了列表相等性检查、所有标准比较方法以及常见代数接口的实现，如幺半群、可折叠、仿函数和单子(oops)。此外，完整的库更好地支持字符串，为了简单起见，我在本文中忽略了这一点。还提供了在列表和 JavaScript 数组以及字符串之间进行转换的函数。对于所有这些函数的类型检查和编译版本，请参见我翻译成 JavaScript 的更全面的 Haskell 函数库:](https://github.com/sjsyrek/lazy-linked-lists)[**【maryamriameliamurphies . js**](https://github.com/sjsyrek/maryamyriameliamurphies)。*

*链表是函数式编程的理想数据结构:它很容易递归定义，并且非常适合函数的组合。函数链表实现起来非常简单，因为它是一个[数学对象](https://en.wikipedia.org/wiki/Geometric_series)，而不是必须通过程序操作的有限计算机内存块的抽象，或者像 JavaScript 那样，是一种特殊的记录类型。用一种更纯粹的函数式语言，我们可以将这种集合类型描述为一个简单的公式，而不必担心内存、指针或其他任何实际机器关心的东西，但我们作为程序员却不用担心。此外，[代数数据类型](https://en.wikipedia.org/wiki/Algebraic_data_type)，尽管听起来很可怕，却可以使我们的数据更容易组织，代码更容易阅读，正如俗话所说，推理。*

*例如，Haskell 中列表的完整定义是`data [] a = [] | a : [a]`。这一小段代码完成了与我的整个类基本相同的事情——这都要归功于[sum 类型的强大功能和表达能力](/@willkurt/why-sum-types-matter-in-haskell-ba2c1ab4e372#.tq1k0tdco)。大多数语言都有产品类型。很少有求和类型。JavaScript `Object`是一个产品类型，因为每个对象都包含一个字段来表示每个可能的值。我们上面定义的`List`不是一个真正的 sum 类型，但是它确实再现了包含*一个值或一组值*或*另一个值或一组值*的类型的功能。其中产品类型代表*和*，总和类型代表*或*。在你的布尔逻辑中没有*或*你不会去做，那么为什么在你的类型系统中没有它们呢？如果不能将类型定义为[不相交的](http://mathworld.wolfram.com/DisjointSets.html)，您就会被困在一个人工层次和过度指定的抽象的世界中。当所有东西都是产品类型时，当你别无选择，只能将自定义类型(即类)定义为值的集合，而不是 T21 值中的选择时，你就会得到这个结果。如果你曾经遇到过将一堆对象拼接在一起以对不一致的数据建模的困难，那么你就会知道我所说的痛苦。现在你有补救方法了。使用 sum 类型，您可以更容易地使您的模型适应混乱的数据世界，而不是试图强迫数据符合您的模型。在函数式编程中，我们更喜欢 [Proteus](https://en.wikipedia.org/wiki/Proteus) 而不是 [Procrustes](https://en.wikipedia.org/wiki/Procrustes) 。我们更喜欢灵活的列表而不是死板的数组。JavaScript 的内置`Array`类型已经朝着功能性更强的方向迈出了一大步，但它本质上仍然是一个黑客。幸运的是，JavaScript 有更高阶的函数，所以我们也可以自己动手。*

## *后记—二叉树*

*我在本文中演示的实现链表的方法原则上也可以用于其他链接结构。如果一个给定的数据类型有一个明显的递归定义，对它应用同样的逻辑不会太麻烦。我们先简单看一下[二叉树](https://en.wikipedia.org/wiki/Binary_tree)。我们可以在 Haskell 中定义一棵二叉树如下:`data Tree a = Leaf | Node (Tree a) a (Tree a)`。就像我们上面的`List`类型一样，`Tree`是一个求和类型，这意味着任何给定的`Tree`都将是*或者是*a`Leaf`或者是 a `Node`。`Leaf`就像`emptyList`一样:它是结构的自然基本情况或终点。然而，一个链表只在一个点终止，一棵树可以有多个分支，每个分支可以有更多的分支，所有的分支都在端点终止。下面是这个相同结构在 JavaScript 中的基本实现，包括用于插入到树中的函数和用于以通常的顺序显示树值的函数(为了方便起见，转换为数组):*

```
***class** Tree {
  **constructor**(left, label, right) {
    **this**.left = () => left
    **this**.label = () => label
    **this**.right = () => right
  }
}

**const** leaf = **new** Tree()

**const** node = (left, label, right) => **new** Tree(left, label, right)

**const** insert = (x, t) => {
  **if** (t === leaf) { **return** node(leaf, x, leaf) }
  **if** (x === t.label()) { **return** t }
  **if** (x < t.label()) {
    **return** node(insert(x, t.left()), t.label(), t.right())
  }
  **return** node(t.left(), t.label(), insert(x, t.right()))
}

**const** preorder = t => t === leaf ? [] : 
  [t.label()]
  .concat(preorder(t.left())
  .concat(preorder(t.right())))

**const** inorder = t => t === leaf ? [] :
  inorder(t.left())
  .concat([t.label()]
  .concat(inorder(t.right())))

**const** postorder = t => t === leaf ? [] :
  postorder(t.left())
  .concat(postorder(t.right())
  .concat([t.label()]))*
```

*现在，让我们构建一个测试树，并尝试我们的小函数库:*

```
***let** tree = leaf**let** squares = listInfBy(1, x => x * 2)**let** cubes = listInfBy(1, x => x * 3)**for** (**let** value **of** take(10, squares)) { tree = insert(value, tree) }

preorder(tree) *// [1,2,4,8,16,32,64,128,256,512]*inorder(tree) *// [1,2,4,8,16,32,64,128,256,512]*postorder(tree) *// [512,256,128,64,32,16,8,4,2,1]* 
**for** (**let** value **of** take(5, cubes)) { tree = insert(value, tree) }

preorder(tree) *// [1,2,4,3,8,16,9,32,27,64,128,81,256,512]*inorder(tree) *// [1,2,3,4,8,9,16,27,32,64,81,128,256,512]*postorder(tree) *// [3,9,27,81,512,256,128,64,32,16,8,4,2,1]**
```

*学完函数链表，二叉树应该是小菜一碟。只是为了好玩，我使用了一个无限的正方形列表来生成上面的示例树，然后将它与立方体流混合在一起。为什么？再一次演示函数组合。函数式编程的强大来自于所有这些小概念的积累。不断学习它们，不断扩充你的工具箱。懒惰二叉树的实现我留给读者作为练习。*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。*
> 
> *要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*