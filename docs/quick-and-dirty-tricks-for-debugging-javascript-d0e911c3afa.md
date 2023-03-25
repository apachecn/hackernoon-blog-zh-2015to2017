# 调试 Javascript 🕵的快速和肮脏的技巧

> 原文：<https://medium.com/hackernoon/quick-and-dirty-tricks-for-debugging-javascript-d0e911c3afa>

在理想的情况下，每段代码都有多个测试，任何回归都会立即指向一个特定的提交，并且所有的提交都有合理的大小和合理的消息。

我不住在那里(这可能部分是我自己的错)，你可能也不住在那里。这里有一些对我很有用的调试技巧，我希望它们对你也有用…只是不要不小心犯了这些错误。

# 这是什么？

我们都看过混乱的变量、操作和方法调用，并想知道— *#@ &* ？

假设您发现了一个错误，其中`foo`的值有时是不正确的:

我假设每个人都知道如何使用断点和`debugger`。这些都是很好的工具，但有时有问题的代码可能位于很难放置断点的地方，或者您可能不想为暂停脚本执行而烦恼。

一种快速而简单的方法是，在不需要对代码进行重大重组的情况下，记录下这些值:

这是因为`console.log`返回`undefined`，而`undefined || val`返回`val`。可怕，丑陋，但男孩很快！无论如何你都不会签入这些代码，这有什么关系呢？

但问题是，当您调试和调整代码时，您可能会忘记同步原始值和记录的内容。

来了一个效用函数！

现在您可以这样做:

也许您想用一些东西来标记日志，以便区分它们:

# 这叫什么？

在我关于在 React 中使用 AngularJS (Angular 1)组件的文章[中，我写了我们如何遇到浏览器地址栏偶尔被重置的问题。我知道 Angular 最有可能调用的是`history.pushState`，但是在我想出如何修复它之前，我仍然需要找出它在哪里以及为什么要这样做。](/appifycanada/angular-components-in-react-2c929130f995)

我是这样做的:

每当有东西调用`history.pushState`，它就会得到我修改过的版本，打印出堆栈跟踪，让我知道调用者是谁。

你可以把它抽象成一个可以应用到其他地方的效用函数。*(根据* [*格雷格·洛克伍德*](/@greglockwood/nice-succinct-article-with-some-handy-js-debugging-tips-54784d6c4e80) *的建议更新)*

让我们在`console.log`上试试

# 这是什么设置/覆盖的？

有很多次，我不得不处理多个版本的 Lodash 和下划线相互覆盖的情况。

为了弄清楚为什么会发生这种情况，可以使用类似的技巧，通过利用`Object.defineProperty`来查看属性何时被赋予新值。

又来了一个效用函数

现在让我们尝试覆盖`_`

# 错过什么了吗？

请给我 [@CheapSteak](https://twitter.com/CheapSteak) 留言或发推文，让我知道你发现的任何其他有用的调试技巧

非常感谢 [Jeffrey Burt](https://twitter.com/jephuff) 和 [Anne Thomas](https://twitter.com/AlfalfaAnne) 的校对和反馈，以及 [Greg Lockwood](/@greglockwood/nice-succinct-article-with-some-handy-js-debugging-tips-54784d6c4e80) 关于返回`traceFn`中`fn.apply`的值并将这些作为[片段](https://developers.google.com/web/tools/chrome-devtools/snippets)包含在 Chrome Dev Tools 中的建议。

感谢阅读😄

如果你喜欢这篇文章，你可能会发现我的比较 npm 包下载的工具很有用——[npmcharts.com](https://npmcharts.com/)

这是一个比较所有 React UI 组件库的样本图表—

## [注册我的时事通讯，在你的收件箱里收到新文章](http://eepurl.com/cVFJB1)