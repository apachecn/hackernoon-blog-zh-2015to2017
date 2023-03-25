# 将 mongoose CRUD 操作抽象成一个共享文件

> 原文：<https://medium.com/hackernoon/abstracting-mongoose-crud-operations-into-a-shared-file-34374f847f4a>

当用[节点](https://hackernoon.com/tagged/node)构建一个 [API](https://hackernoon.com/tagged/api) 时，Express 和 MongoDB 你可以选择 Mongoose 作为你的 ODM。猫鼬帮助你轻松地做 CRUD 操作。当您的 API 增长时，通常模型的数量会增长，因此快速路线的数量也会增长。在很多情况下，这些路由只做简单的创建、读取、更新或删除操作。如果这些路由不包含任何其他逻辑，那么如果我们能够将请求转发给一个可重用的共享函数来处理该请求就好了，对吗？

![](img/a12811bc6a23a5cf0a76a551879190ae.png)

by [Matteo Catanese](https://unsplash.com/@matteocatanese) on [unsplash](https://unsplash.com/photos/PI8Hk-3ZcCU)

嗯，是的！抽象出简单的逻辑有助于加速开发过程，提高可维护性，并减少潜在的错误或缺陷。假设我们正在开发一个应用程序，让您和您的同事管理他们的午餐订单。为了得到一个订单，如果我们写出所有的逻辑，`GET`路线可能看起来像这样:

这本身就很好。但是除了`model.Order`之外，如果我们创建一个`GET`路由来获取您可以为午餐订购的特定项目，并且如果我们创建一个`GET`路由来检索特定用户，那么这个路由将是相同的。每次为每条路线写出这段代码是可行的，但是不太容易维护。解决这个问题的一个方法是将这个逻辑移到应用程序中的更高层次，将其抽象一点，然后使其对于需要上述逻辑的每个`GET`路线都是可导入的。抽象的逻辑可能如下所示:

既然我们已经抽象了逻辑，我们可以在我们的`GET`路线中使用它，如下所示:

我们传递`request`、`response`、`next`和`model.Order`，其余的由`get`函数负责。通过公开这个函数，我们将加速下一个需要简单`GET`的路线的开发，并且我们增加了我们的可维护性，因为逻辑只在一个地方。

现在当然有更多的常见操作。让我们看看用于创建新资源的`POST`。`POST`操作的路线可以是这样的:

我们再抽象一下:

像这样使用它:

然后我们也可以对`UPDATE`做同样的事情。因为我想你现在明白了。以下是`CREATE`、`UPDATE`、`GET`和`DELETE`的完整 rest.js 文件:

# 结论

我们用可重用的助手函数创建了一个 rest.js 文件，可以处理公共逻辑。您可以根据自己的喜好扩展/更改该文件，并加入诸如填充或查询参数之类的内容。编码快乐！

感谢阅读。如果你喜欢这篇文章，请点击拍手按钮。有反馈吗？让我知道。也查看我的其他文章:

*   [https://hacker noon . com/validating-reactive-forms-with-default-and-custom-form-field-validators-in-angular-5586 DC 51 C4 AE](https://hackernoon.com/validating-reactive-forms-with-default-and-custom-form-field-validators-in-angular-5586dc51c4ae)
*   [https://hacker noon . com/manage-your-observable-subscriptions-in-angular-help-of-rx-js-f 574 b 590 a5cb](https://hackernoon.com/manage-your-observable-subscriptions-in-angular-with-help-of-rx-js-f574b590a5cb)
*   [https://hacker noon . com/understanding-creating-and-subscribe-observables-in-angular-426 DBF 0 b 04 a 3](https://hackernoon.com/understanding-creating-and-subscribing-to-observables-in-angular-426dbf0b04a3)
*   [https://hacker noon . com/managing-large-s-CSS-projects-using-the-inverted-triangle-architecture-3c 03 e 4 B1 e 6 df](https://hackernoon.com/managing-large-s-css-projects-using-the-inverted-triangle-architecture-3c03e4b1e6df)
*   [https://hacker noon . com/understanding-map-filter-and-reduce-in-JavaScript-5 df 1c 7 eee 464](https://hackernoon.com/understanding-map-filter-and-reduce-in-javascript-5df1c7eee464)

*关注我上* [*中*](/@luukgruijs) *或* [*推特*](https://twitter.com/luukgruijs) *让我们连线上*[*LinkedIn*](https://www.linkedin.com/in/luukgruijs/)