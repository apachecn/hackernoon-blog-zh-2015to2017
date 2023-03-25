# 功能计算思维——什么是单子？

> 原文：<https://medium.com/hackernoon/functional-computational-thinking-what-is-a-monad-2adea91154e>

如果你正在使用函数式[编程](https://hackernoon.com/tagged/programming)，无论是使用真正的函数式语言还是任何支持一级函数的语言，你可能在某个时候已经使用过单子了。大多数人只是不知道他们是单子。在这篇文章中，我将不用任何数学定理来解释什么是单子。此外，我将演示如何仅使用普通的 [JavaScript](https://hackernoon.com/tagged/javascript) 来派生 monad。

警告:这是一个高级主题。可能不适合初学者。
免责声明:本文不会提供单子的确切定义。所以有关于单子的考试就不要看了。

我学习 Haskell 已经有半年了。我喜欢看 youtube 上的会议/聚会视频。有一天，我看了一个视频，叫《让我们成为主流吧！ELM 的创建者 Evan Czaplicki 说“monad 是回调”。我以为我明白了什么是单子，但我不明白为什么埃文说。这是那个视频的链接，如果你愿意，你可以在完成这篇文章后查看。

几个星期过去了，在阅读了大量的学习材料之后，尤其是在看了德里克·赖特的《单子为什么重要》之后。在这个视频中，他演示了几种不同单子，试图解决一个问题:函数组合。

下面的解释基本上使用了相同的概念，但是我将使用 JavaScript 而不是 Haskell。

首先，作为一名函数式程序员，我们都知道函数组合，这非常非常简单，就像这样:

组合函数 *composeF* 非常简单，它带有两个参数 *f* 和 *g* ，并返回一个新函数，该函数返回应用 *g* 然后 *f* 后的结果值。我们可以用这个函数将 *mul3* 和 *add1* 组合成新的函数 *addOneThenMul3* 。

现在，假设我们有一个场景:

我们所拥有的:

1.  两个文件，第一个文件(*文件 1* )包含指向第二个文件(*文件 2* )的路径，第二个文件包含我们想要的真实内容。
2.  读取文件功能

接下来，我们需要构造获取“真实内容”的函数。

readFileSync 是一个同步 IO 函数，它将路径字符串作为输入，并以字符串形式返回给定路径的内容。所以我们可以重用 *composeF* 来组合我们想要的函数: *readFileContentSync* 。

到目前为止一切顺利！

但是如果 readFile 函数是异步的呢？如果您熟悉 Node.js，那么您可能已经使用回调来进行流控制，它有一个正式的名称:continuation-passing style 或 CPS。

 [## 连续传球风格

### 在函数式编程中，连续传递风格(CPS)是一种将控制传递给

en.wikipedia.org](https://en.wikipedia.org/wiki/Continuation-passing_style) 

那么*读取文件*功能将如下所示:

现在，有一个问题:我们不能用 c*composif*来进行函数合成。因为这一次，函数没有返回任何内容。

但是等等，我们仍然可以做函数合成，只要:

1.  派生的函数采用与第一个函数相同的输入
2.  它按顺序完成这些功能。

我们需要的只是一个新的合成函数:

注意:我修改了参数的顺序:从 *(f，g)* 到 *(g，f)* 。

您可以看到，c*composipse*将返回一个新的函数，该函数将调用 *g* ，在 *g* 的回调中，调用 *f* ，并调用 *cb* ，最终结果值为 *f* 。

万岁。我们又可以做函数合成了！

好吧，让我们试试有趣的东西。

首先将 *readFileCPS* 的功能签名调整为*read file of*:

```
const readFileHOF = path => cb => {
  readFileCPS(path, cb)
}
```

HOF 表示高阶函数。我们只需将一组参数分解为两组参数。使 *readFileHOF* 取*路径*参数，返回一个以 *cb* 为输入的函数，所有参数置位后，实函数体执行。

然后，我们需要一个新的合成函数。

这与*composits CPS*几乎相同，没有太大不同。

其次，我们给以 *cb* 为参数的函数命名，并把它放入一个对象中，如下所示:

同样，因为我们再次更改了函数签名，所以我们需要调整组合函数:

这实际上也和 *composeHOF* 没什么不同，对吧？

我们讨厌重复你自己。让我们创建一个助手函数:

```
const createExecObj = exec => ({exec})
```

该函数返回一个带有 *exec* 字段的对象，也是一个函数。让我们把“带有 *exec 的对象*字段】 *ExecObj*

*createexecutobj*也使 *readFileEXEC* 两行变短:

```
const readFileEXEC2 = path => {
  return createExecObj(cb => {
    readFileCPS(path, cb)
  })
}
```

经过这种改变，您可以看到 *readFileEXE2* 采取了一种路径并返回了一个 exec 对象。除此之外，一切都一样。

**下一步，我们将有一个大的变化，所以要小心！**

到目前为止，复合函数的两个参数都是函数。让我们把第一个参数换成别的东西:exec 对象！

我们将*合成*重命名为*绑定*。这个*绑定*函数将返回一个新的 exec 对象。如果有人调用这个对象的 *exec* ，那么它将对 *execObj* 做同样的事情，然后在回调中，它使用 *f* 。注意，这个要点的第 4 行与 *composeEXEC* 的第 6 行相同。

然后神奇开始了:

```
const readFile2EXEC2 = bindExec(
  readFileEXEC2('./file1'),
  readFileEXEC2
)
readFile2EXEC2.exec(result => console.log(result))
```

不够清楚？让我们去掉常数:

```
bindExec(
  readFileEXEC2('./file1'),
  readFileEXEC2
)
.exec(result => console.log(result))
```

还不明白吗？嗯，我们快到了，耐心点:

让我们尝试将 *bindExec* 函数“内联”到 Exec 对象，如下所示:

请将此与 *bindExec* 进行比较。返回对象是 exec 对象，所以我们简单地用*这个*替换 *execObj* 。当然，由于同样的原因，我们不需要第一个论证(第 3 行)

那么怎么用呢？

```
readFileEXEC2('./file1')
.bind(readFileEXEC2)
.exec(result => console.log(result))
```

你看到了吗？很熟悉的东西？没有吗？

好吧！让我们做一些重命名:

*   readFileEXEC2 -> readFileAsync
*   绑定->然后
*   执行->完成

```
readFileAsync('./file1')
.then(readFileAsync)
.done(result => console.log(result))
```

我的天啊。这是承诺！！！
(不完全是。因为不管你叫 *done，承诺都会执行。*)

或者

*   readFileEXEC2 -> readFile$
*   绑定->平面图
*   执行->订阅

我的天啊。这是 Rx！！！
(对！差不多！当然，我们仍然需要添加错误处理和其他东西)

## 那么 monad 在哪里呢？

答案是:从*到这里。都是单子！！！具体来说:*

*   由于 *composeCPS* ，readFileCPS 成为一个单子。实际上，它在 Haskell 中被称为 [Cont monad](https://en.wikibooks.org/wiki/Haskell/Continuation_passing_style)
*   “执行对象”因为*绑定*而变成单子。同样，它在 Haskell 中被称为 IO monad，检查 Haskell 中的[文件 IO](https://wiki.haskell.org/Tutorials/Programming_Haskell/String_IO#File_IO)

## 那么如何鉴别一个事物是否是单子呢？

首先，也许你问了一个错误的问题。不要问“是不是单子”，应该问“能不能是单子”。

## 单子有什么性质？

1.  它有一个带值的上下文。
2.  该值不一定存在。
3.  它提供了一种获取值的方法(通常通过回调)
4.  一个*绑定*函数，该函数能够从单子(第一个参数)中取出值，并将该值作为参数放入另一个函数 *f* 中。( *f* 应该是返回一个单子的函数)。并且*绑定*函数的返回值应该与第一个参数的类型相同。
5.  *绑定*函数通常也称为*平面图*

## 所以 monad 是用于异步操作的？不要！！！

例如:

检查这些属性:

1.  是啊！方括号[]是上下文。
2.  存在就可以了。
3.  按 *forEach* 。
4.  因为*映射*将返回嵌套数组(第 14 行)，所以映射不符合 4。我们需要创建一个*平面图*函数。
5.  As 4。

所以没错，数组可以是单子。

## 有必要了解什么是单子才能工作吗？

**不！**

例如，许多开发人员使用 array、promise 和 Rx，却不理解 monad。没关系。我也是，用了很久 Rx 也没有真正理解 monad。但是，如果你想创建一个像 Rx 这样的工具，这仍然是有帮助的。(嗯，我们刚刚做到了:p)

## 结论

所以“单子回调”？

是啊！根据物业 3。

然后“回调是单子”？

还没有！仅当定义了*绑定*函数时。

感谢 [Tom Chen](https://medium.com/u/8b8988f09be0?source=post_page-----2adea91154e--------------------------------) 、[Guan-](https://medium.com/u/adb3ebc98ec6?source=post_page-----2adea91154e--------------------------------)、 [Wu、Ching Ting](https://medium.com/u/6bd546313098?source=post_page-----2adea91154e--------------------------------) 对本文的审阅。

更新:修复错误措辞:激情= >病人，感谢[伊恩霍夫曼-希克斯](https://medium.com/u/10cb09327cf6?source=post_page-----2adea91154e--------------------------------)
更新:重命名 readFileFluent，感谢 [wkliang](https://medium.com/u/f6bfec7294e?source=post_page-----2adea91154e--------------------------------)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)