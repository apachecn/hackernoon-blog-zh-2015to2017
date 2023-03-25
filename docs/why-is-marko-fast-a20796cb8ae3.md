# 为什么马尔科很快？

> 原文：<https://medium.com/hackernoon/why-is-marko-fast-a20796cb8ae3>

![](img/94c5d9586d7e6723430e15cd3b4ce206.png)

在易贝，我们每天使用 [Marko](http://markojs.com/) 来提交超过 10 亿个请求，这要求我们对我们的开源 UI 库 Marko 进行微调。我们对快速渲染的 Marko、[高级性能技术](http://www.ebaytechblog.com/2014/12/08/async-fragments-rediscovering-progressive-html-rendering-with-marko/)进行了大量优化，以实现最小的页面重量(约 10kb gzipped)。性能只是一个问题，因为我们还必须扩展 Marko 来支持跨越数百个团队的开发，以允许开发人员高效地创建可维护和健壮的 web 应用程序。

我们已经创造了[我们自己的基准](https://github.com/marko-js/isomorphic-ui-benchmarks)，我们已经[将 Marko 添加到其他基准](https://github.com/raxjs/server-side-rendering-comparison/pull/11)中，但是基准并不总是可信的。虽然我们尽一切努力公平对待我们的基准，但最重要的是现实应用中的性能，而不是关注微观基准。这是 V8 团队转向[一种新的方法来衡量和理解真实世界的 JavaScript 性能](https://v8project.blogspot.com/2016/12/how-v8-measures-real-world-performance.html)的原因之一。

类似地，我们看了看我们的开发人员是如何*实际上*编写他们的 Marko 组件的，并且发现了可以进一步优化的模式。在这篇文章中，我不想把重点放在基准测试上，而是想把重点放在我们对 Marko 应用的优化的细节上。

# 多个编译输出

Marko 是一个同构的 UI 库，既可以在服务器上运行，也可以在浏览器中运行。正如 [Michael Rawlings](https://medium.com/u/9d30414878d1?source=post_page-----a20796cb8ae3--------------------------------) 在“[服务器端渲染枪战](https://hackernoon.com/server-side-rendering-shootout-with-marko-preact-rax-react-and-vue-25e1ae17800f)”中提到的，当在服务器上渲染时，Marko 直接渲染为文档的字符串表示(HTML)，可以作为 HTTP 响应发送。

当在浏览器中呈现时，必须解析 HTML 字符串才能更新 DOM。由于这个原因，Marko 编译了一个程序的视图，该视图直接渲染到一个虚拟文档(VDOM)树，该树可用于在以浏览器为目标时有效地更新真实的 DOM。

给定以下模板:

## 为服务器编译

编译后的输出为服务器上的流 [HTML](https://hackernoon.com/tagged/html) 输出进行了优化:

## 为浏览器编译

编译后的输出针对浏览器中的虚拟 DOM 呈现进行了优化:

# 模块化运行时

Marko 运行时并不是作为一个单独的 [JavaScript](https://hackernoon.com/tagged/javascript) 文件发布的。相反，Marko 编译器生成一个 JavaScript 模块，该模块只导入运行时中实际需要的部分。这允许我们在不增加现有应用程序的情况下向 Marko 添加新功能。例如，给定以下模板:

在上面的例子中，需要额外的运行时代码来根据提供的 JavaScript 对象呈现`style`属性。导入`styleAttr`助手的编译代码如下所示:

# 高性能服务器端渲染

与基于 JSX 的专门进行虚拟 DOM 渲染的解决方案相比，Marko 在服务器端渲染方面有着巨大的优势。当渲染到服务器上的虚拟 DOM 树时，渲染 HTML 需要两个步骤:

*   在内存中生成整个虚拟 DOM 树的第一步
*   第二步是将虚拟 DOM 树序列化为 HTML 字符串，然后可以通过网络发送(这需要遍历整个树结构)

相比之下，Marko 只需一次操作就可以直接渲染成 HTML 流。没有中间树数据结构。

# 静态子树的编译时优化

给定以下模板:

Marko 将认识到模板片段每次都产生相同的输出，因此它将创建一次虚拟 DOM 节点，如以下编译后的输出所示:

渲染静态子树的成本几乎为零。此外，Marko 将跳过区分/修补静态子树。

类似地，在服务器上，Marko 将把模板的静态部分合并成一个字符串:

# 静态属性的编译时优化

Marko 还会优化动态元素上的静态属性。

给定以下模板:

Marko 将生成以下编译后的输出:

请注意，属性对象仅创建一次，并且用于每次渲染。此外，静态属性不会发生差异/修补。

# 智能编译器

对于 Marko，我们倾向于在编译时做尽可能多的事情。这使得我们的编译器更加复杂，但是它在运行时给了我们很大的好处。我们有大约 90%的代码覆盖率和超过 2000 次测试，以确保编译器正常工作。此外，在许多情况下，Marko 编译器为给定模板的运行时提供提示，以便运行时可以针对特定模式进行优化。例如，Marko 识别 HTML 元素是否仅定义了`class` / `id` / `style`，并且在进行区分/修补时，运行时针对这些虚拟 DOM 节点进行优化(Marko 编译器生成代码，为目标区分/修补逻辑标记简单的虚拟 DOM 节点)。

# 事件委托

如果你正在构建一个 UI 组件，你可能需要编写代码来处理各种 DOM 事件(`click`、`submit`等)。).开发人员通常使用`el.addEventListener(...)`或 jQuery 之类的库来编写添加 DOM 事件侦听器的代码。在使用 Marko 构建 UI 组件时，您仍然可以这样做，但是在初始化大量组件时，附加侦听器会产生开销。相反，Marko 建议使用如下所示的声明性事件绑定:

当使用声明性事件绑定时，实际上没有为冒泡事件附加任何 DOM 事件侦听器。相反，Marko 为每个冒泡的 DOM 事件在页面的根 DOM 元素上附加一个监听器(在启动时完成)。当 Marko 在根处接收到一个事件时，它处理将该事件委托给对该事件感兴趣的适当组件。这是通过查看`event.target`属性来查看事件的来源，然后沿着树向上查找需要通知的组件来完成的。因此，当在根位置捕获 DOM 事件时，要做的工作稍微多一些，但是这种方法使用的内存要少得多，并且减少了初始化过程中的工作量。将事件委托给组件的额外开销不会很明显，因此这是一个非常有益的优化。

有兴趣了解更多关于 Marko 的信息吗？如果是这样，您可以在 [Marko 网站](http://markojs.com/)上获得更多信息。加入对话，在 [GitHub](https://github.com/marko-js/marko) 上投稿，在 [Twitter](https://twitter.com/MarkoDevTeam) 上关注我们。

*封面图片鸣谢:* [*超级英雄由 Zech Nelson 从名词项目*](https://thenounproject.com/search/?q=superhero&i=990792)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)