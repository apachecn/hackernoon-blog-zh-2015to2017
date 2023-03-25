# 在 javascript 中使用 apply()

> 原文：<https://medium.com/hackernoon/its-not-magic-using-apply-in-javascript-75bbf5da6b9a>

在写完 [bind()](/@trekinbami/its-not-magic-using-bind-in-javascript-18834e95cd8e#.rlkcnlwwc) 和 [call()](/@trekinbami/its-not-magic-using-call-in-javascript-640b3e29df35#.ny0afpwzh) 之后，本文将对`apply()`进行更多的解释。那些文章涵盖了一些东西，尤其是关于`this`和上下文，这非常重要。因此，如果你还没有读过这些文章，我真的建议你读一读。

就像绑定和调用一样，`apply()`在你应用到**的函数中改变`this` 的上下文。**如果你想了解更多关于改变`this`的脉络，我推荐你去看看我之前写的关于[绑定](/@trekinbami/its-not-magic-using-bind-in-javascript-18834e95cd8e#.rlkcnlwwc)和[调用](/@trekinbami/its-not-magic-using-call-in-javascript-640b3e29df35#.5uvm73sjh)的文章。

[应用](https://hackernoon.com/tagged/apply)的功能与`call()`完全相同，但有一个细微的区别。它的第二个参数是一个参数数组，而不是`call()`需要的基于逗号的单个参数。第一个参数是，就像`call()`一样，是`this`的新上下文。

这些评论是不言自明的。但是为了清楚起见:我们正在执行 callOut，改变 callOut[和 callOut](https://hackernoon.com/tagged/callout) 的上下文，并将一个数组作为单个参数传递给`apply()`。当然，你可以使用一百万种其他方法来达到同样的结果，但是这只是一个简短的例子来展示`apply()`所能提供的一切。

仅此而已。感谢阅读！有什么问题吗？回复，或者在推特上给我发消息:[http://twitter.com/trekinbami](http://twitter.com/trekinbami)