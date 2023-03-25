# 做出反应。被认为有害的纯成分

> 原文：<https://medium.com/hackernoon/react-purecomponent-considered-harmful-8155b5c1d4bc>

编辑:我不再袖手旁观这篇文章的观点，但是导致 PureComponent 经常无法阻止渲染的技术问题仍然值得理解。

浪费渲染是 performant React.js 代码中的一个大问题。React 核心团队想出了一个解决方案:React.PureComponent 我们来看看它是如何工作的，解决了哪些问题，没有解决哪些问题。

那是什么？PureComponent 与 React 完全一样。组件有一个不同之处:它实现了 shouldComponentUpdate。这允许它根据一些启发来阻止渲染。PureComponent 实现了对新属性和旧属性的浅层相等性检查，对状态也是如此。因此，如果你有道具“a”和“b ”,它基本上会做以下事情:

这在理论上很棒，因为我们可以避免昂贵的渲染，大大提高我们的性能。但是，等等！“这太好了，不像是真的”，我听到你说。嗯……你是对的。我们遇到了这些问题！==支票。假设 Foo 是一个从 React.PureComponent 扩展而来的组件。

这三个例子有着相同的问题:Foo 将总是重新呈现，因为 shouldComponentUpdate 总是返回 true！在前两个示例中，shouldComponentUpdate 检查 this.props.x！== nextProps.x，而且自从{}！== {}，对于。地图，它将总是看到的道具已经改变。同样的事情也发生在儿童道具上，这里 this.props.children！== nextProps.children。

这里的小问题是，我们正在进行 shouldComponentUpdate 检查，但没有任何收获，这比我上面给出的例子更昂贵，因为它必须迭代当前和下一个 props 对象的键。

更大的问题是，我们假设我们已经解决了一个性能问题，但大多数时候只会让它变得更糟。

嗯…那么我们如何防止不必要的渲染和差异呢？一招！回车: [react-update-if-changed](https://github.com/brigand/react-update-if-changed) 。

在这个例子中，Foo 使用的是 react-update-if-changed，而不是 PureComponent。而不是检查这个。道具。孩子们！== nextProps.children，它只是检查 updateIfChanged 属性是否相等。一次相等检查；就是这样。

还有两个遗留问题。

1.  我们可能有多个可以改变的值，或者一个应该通过深度相等来比较的值。在这种情况下，可以使用 updateIfChangedEqual={[a，b]} prop。
2.  我们仍然需要计算上面例子中的

    # (但不是 diff it)，这实际上可能计算起来很昂贵。请参见解决此问题的 react-update-if-changed 中的 [UpdateIfChanged](https://github.com/brigand/react-update-if-changed#updateifchanged-component) 组件。

做出反应。PureComponent 很少有用，但是不要对 React.js 的出色性能放弃希望！

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！