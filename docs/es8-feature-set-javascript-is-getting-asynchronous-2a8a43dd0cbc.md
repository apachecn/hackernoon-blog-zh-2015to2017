# ES8 特性集:JavaScript 变得异步

> 原文：<https://medium.com/hackernoon/es8-feature-set-javascript-is-getting-asynchronous-2a8a43dd0cbc>

![](img/909e7dbb5dda2b6df4bf43a014151da5.png)

在大规模 ES6 发布后，这花费了大约 6 年时间(2009 年 12 月-2015 年 6 月)来实现标准化，TC39 委员会已经发出了年度但较小版本的通知，从 ECMAScript 2016 (ES7)开始。ES8 的最终功能集尚未获得批准，截至 2017 年 2 月，尽管我们可以假设第 4 阶段的所有规格和第 3 阶段的大部分都将包含在 ES2017 中。建议的功能集如下所示:

## 核心特性:

*   [**异步功能**](https://github.com/tc39/ecmascript-asyncawait)

异步功能被合理地称为一项重大改进，并引起了 JavaScript 开发人员的极大兴趣。可以理解的是，这是赶上其他编程语言的方法之一，比如开创了异步工程的 C#。此外，根据首席 C#架构师和 JS 的 TypeScript transpiler 的核心开发人员安德斯·海尔斯伯格的说法，因为 JS 中的异步编程与 C#的方法类似。

这一功能将增强用户体验，使 transpilation 避免，促进开发过程，并使您的 JavaScript 应用程序更容易理解。你不会被长时间运行的复杂操作阻塞，浪费时间等待结果。

*   [**共享内存和原子**](https://github.com/tc39/ecmascript_sharedmem)

共享内存和原子或共享数组缓冲区充当顶层并发抽象的底层构建块。它们使开发人员能够在几个工作线程和核心线程之间共享 SharedArrayBuffer 对象的字节。这种方法使您能够更快地共享数据，并提高和简化员工之间的协调。

![](img/4ffae746790a1071d6a2852309ecd017.png)

Photo by [DA-14 Software Development LLC](https://da-14.com/)

## 其他新选项:

*   [对象值和对象条目](https://github.com/tc39/proposal-object-values-entries)
*   [用 padStart 和 padEnd 填充字符串](https://github.com/tc39/proposal-string-pad-start-end)
*   [object . getownpropertysdescriptors](https://github.com/tc39/proposal-object-getownpropertydescriptors)
*   [函数参数表和调用中的尾随逗号](https://github.com/tc39/proposal-trailing-function-commas)

## 总结

这或多或少是 ECMAScript 2017 最有趣的功能，它还没有最终确定。还有更多[功能](https://github.com/tc39/proposals/blob/master/README.md#active-proposals)值得您关注，因为到目前为止它们处于第 3 阶段，也可以包含在 ES8 中:

*   SIMD。JS — SIMD 原料药
*   function . prototype . tostring revisiou
*   取消模板文字限制
*   休息/传播属性
*   异步迭代
*   全球的
*   导入()

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)