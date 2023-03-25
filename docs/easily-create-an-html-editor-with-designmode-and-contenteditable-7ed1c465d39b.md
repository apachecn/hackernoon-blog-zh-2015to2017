# 使用设计模式和内容可编辑轻松创建 HTML 编辑器

> 原文：<https://medium.com/hackernoon/easily-create-an-html-editor-with-designmode-and-contenteditable-7ed1c465d39b>

![](img/0ddfa42e98623547b528146159217e0c.png)

你有没有想过重新创建一个像我们在 Medium 上使用的文本编辑器有多难？

如果你没有看过 Medium 的文本编辑器，我强烈推荐你去看看。您可以在您的浏览器中编写、编辑和设计您的内容(在 Medium 提供的样式内)。

不需要任何技术知识。文本中不会出现 html 标签。

如果我说你只需要普通的 JavaScript、CSS 和 HTML，你会相信吗？

看一看:

An example of inline editable content with document.designMode

希望这个例子向您展示了一些东西:

1.  你有两个选择。`document.designMode`用于整个文档，而`element.contentEditable`仅用于一个元素。
2.  浏览器给了我们所有需要的方法。任何在`document.execCommand`之外的东西仅仅是为了演示和错误处理。
3.  这些命令通过在内容周围添加新标签来修改 HTML。这允许我们使用 CSS 来设计这些标签的样式。如此简单。
4.  唯一的“问题”是，如果通过点击按钮触发命令，您必须使用`event.preventDefault();`。否则，焦点将转移到按钮上，命令将失败。

如果没有选择任何内容，命令将失败*(有时)*。

如果能更好地满足您的需求，您还可以使用 HTML 创建可编辑的内容。

```
<div contenteditable="true">
  This text can be edited!
</div>
```

这可以很好地与 React、Angular 等框架一起工作。您可以使用组件的状态来控制内容是否可编辑。

# 使用数据

现在，我们必须小心处理这个。这里有几个选择:获取整个编辑过的内容，去掉任何危险的内容(如 JavaScript)并呈现给你的内容，或者你可以设计一个系统来单独抓取更改过的数据。

无论哪种方式，您都需要:

1.  当文本改变时获取新文本。
2.  确保文本是安全的。
3.  渲染回你的整个 HTML。

下面你会发现与上面相同的例子，但是这次当你改变文本时，会有一些信息记录到控制台。我使用`MutationObserver` API 来观察这些变化。

那我做了什么？

*   我给我想观察的每个元素添加了一个属性。您不必这样做，但它可以让您专注于特定的可编辑项目。如果您这样做，请注意不要为您不关注更改的标签打开编辑模式。
*   我检查了这些突变，以发现它们是否包含我正在寻找的属性。如果有，我会将该元素的属性名和新内容发送到输出(在本例中是控制台)。

我认为这很简单。出于您的目的，您可以用保存更改的后端服务替换控制台。你要确保没有添加危险的 JavaScript，这样就不会让你受到 XSS 的攻击。

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不打算以任何方式代表我的雇主*。所有代码样本都是我自己的，与美国银行的代码完全无关。*

我也很想收到你的来信，请随时通过 [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/) 、 [Github](https://github.com/justindfuller) 或 [Medium](/@justindanielfuller) 与我联系。再次感谢阅读！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)