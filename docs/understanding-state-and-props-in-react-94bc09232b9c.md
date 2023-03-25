# 了解 React 中的状态和属性

> 原文：<https://medium.com/hackernoon/understanding-state-and-props-in-react-94bc09232b9c>

![](img/d62cddb32411c9505d0decb900e2036b.png)

我最近一直在玩 React 和 Redux，并认为我应该开始写一些关于我必须思考的概念的文章。

因此，除非你过去几年一直住在山洞里，否则你会知道 React 是一个很棒的前端库，由脸书的好人们开发，旨在让开发者的生活更轻松。

它不同于 Angular 或其他框架，因为它是纯粹的前端**(尽管见下面的评论对此有很大的澄清)。也就是说，它非常强大。**

在我学习 React 的过程中，我很难理解的一个概念是状态和道具之间的交互。我想其他人可能有同样的问题，所以这里是我的看法。

**道具**

先说道具。这只是属性的简写。道具是组件之间相互交流的方式。如果你熟悉 React，那么你应该知道道具是从父组件向下流动的。

还有一种情况，你可以有默认的属性，这样即使父组件没有传递属性，属性也会被设置。

这就是为什么人们称 React 为具有*单向*数据流。这需要你花点时间思考，稍后我可能会写博客，但现在只要记住:数据从父母流向孩子。道具是不可改变的(不变的花哨说法)

所以我们很开心。组件从父组件接收数据。都整理好了，对吧？

不完全是。当一个组件从父组件之外的其他人那里接收数据时会发生什么？如果用户直接向组件输入数据会怎样？

这就是为什么我们有国家。

**状态**

道具不应该变，所以状态提升。通常组件没有状态，因此被称为无状态组件。使用状态的组件被称为*有状态的*。在聚会上，你可以随意透露一些小消息，看着人们慢慢远离你。

所以使用状态是为了让一个组件可以跟踪它所做的任何呈现之间的信息。当你*设置状态*时，它更新状态对象，然后重新渲染组件。这太酷了，因为这意味着 React 会处理这些困难的工作，而且速度快得惊人。

作为状态的一个小例子，这里有一个来自搜索栏的片段(如果你想了解更多关于 React 的知识，值得看看这个课程)

```
Class SearchBar extends Component {
 constructor(props) {
  super(props);this.state = { term: '' };
 }render() {
  return (
   <div className="search-bar">
   <input 
   value={this.state.term}
   onChange={event => this.onInputChange(event.target.value)} />
   </div>
   );
 }onInputChange(term) {
  this.setState({term});
  this.props.onSearchTermChange(term);
 }
}
```

**总结**

道具和状态做类似的事情，但是用的方式不同。您的大多数组件可能都是无状态的。

*Props* 用于将数据从父组件传递到子组件或由组件本身传递。它们是不可变的，因此不会被改变。

*状态*用于*可变* 数据，或者会发生变化的数据。这对于用户输入特别有用。以搜索栏为例。用户将输入数据，这将更新他们所看到的。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)