# 角度中的条件模块导入

> 原文：<https://medium.com/hackernoon/conditional-module-imports-in-angular-518294aa4cc>

每当您向您的 [Angular](https://hackernoon.com/tagged/angular) 应用程序引入第三方服务时，总会有这样的情况，您只需要那些服务用于开发、测试或生产。为了使您的构建尽可能的快速和小，您希望有条件地导入那些服务。

![](img/f8390be3e9aeb77635bd3844433a2b80.png)

Photo by [WEB AGENCY](https://unsplash.com/photos/KDYcgCEoFcY?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

最好的例子是:

*   您正在使用 ngrx/store 和 ngrx/store-devtools [模块](https://hackernoon.com/tagged/module)。您希望只在开发中使用 ngrx/store-devtools，并防止它被导入到生产中。
*   您正在使用 logrocket 之类的生产日志服务，并且希望仅在生产时启动该服务，而不是在开发时。

由于启动日志服务是特定于您正在使用的服务的，所以我不会深入讨论。然而，您可以遵循与仅在开发中导入 ngrx/store-devtools 相同的模式。仅在开发中导入 ngrx/store-devtools 可以这样完成:

# 诀窍是

我们定义一个名为`dev`的数组，并在该数组中导入/配置我们的`storeDevtoolsModule`。然后我们使用`NODE_ENV`变量检查我们是否在生产中。如果我们在生产中，我们再次清除`dev`数组。使用 spread 操作符将这个`dev`数组导入到模块声明中。在数组中使用 spread 运算符时，它允许您轻松地将一个数组的扩展版本放入另一个数组中。下面的示例演示了这一点:

希望这个小技巧对你做条件模块导入有帮助。

感谢阅读。如果你喜欢这篇文章，请点击拍手按钮。有反馈吗？让我知道。也查看我的其他文章:

*   [https://hacker noon . com/manage-your-observable-subscriptions-in-angular-help-of-rx-js-f 574 b 590 a5cb](https://hackernoon.com/manage-your-observable-subscriptions-in-angular-with-help-of-rx-js-f574b590a5cb)
*   [https://hacker noon . com/understanding-creating-and-subscribe-observables-in-angular-426 DBF 0 b 04 a 3](https://hackernoon.com/understanding-creating-and-subscribing-to-observables-in-angular-426dbf0b04a3)
*   [https://hacker noon . com/managing-large-s-CSS-projects-using-the-inverted-triangle-architecture-3c 03 e 4 B1 e 6 df](https://hackernoon.com/managing-large-s-css-projects-using-the-inverted-triangle-architecture-3c03e4b1e6df)
*   [https://hacker noon . com/understanding-map-filter-and-reduce-in-JavaScript-5 df 1c 7 eee 464](https://hackernoon.com/understanding-map-filter-and-reduce-in-javascript-5df1c7eee464)
*   [https://hacker noon . com/an-angular-2-web pack-setup-for-development-and-production-3 ea 8 bcc 35 e 24](https://hackernoon.com/an-angular-2-webpack-setup-for-development-and-production-3ea8bcc35e24)

*关注我上* [*中*](/@luukgruijs) *或* [*推特*](https://twitter.com/luukgruijs) *让我们连线上*[*LinkedIn*](https://www.linkedin.com/in/luukgruijs/)