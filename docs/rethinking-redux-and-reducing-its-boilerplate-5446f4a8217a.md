# 重新思考 Redux(并减少其样板文件)

> 原文：<https://medium.com/hackernoon/rethinking-redux-and-reducing-its-boilerplate-5446f4a8217a>

![](img/ce4bdbdb7cecfdccce85be756f75cd39.png)

Sunrise in the Sea by Emilio Srougo

*TL；博士:*[*redux-create-module*](https://github.com/Emilios1995/redux-create-module)

假设您想在 react-redux 应用程序中创建一个新模块。你知道该怎么做。创建动作类型常量，make 和动作创建器，并处理 reducer 中的类型。你可能已经注意到，这个过程看起来几乎每次都是一样的*。作为开发人员，我们知道这类事情可以而且应该被抽象和自动化。*

*所以，首先让我们想一想什么是开发者体验的最佳解决方案。或者，既然*我们是*开发者，我们就[重新思考](https://hackernoon.com/tagged/rethink) [Redux](https://hackernoon.com/tagged/redux) 吧。*

*嗯，如果我们只对*映射*动作到状态感兴趣，我的第一个想法是做一个*映射*。或者一个普通的物体。*

*但是我们不想创造减速器。我们只想考虑地图。让我们创建一个函数，它接受一个地图并返回一个归约器*

*这么简单！或者说，太简单了！因为我们这里有个问题。动作创建器的发明是有原因的，那是因为有些动作需要有一个有效载荷来处理…或者是吗？不要！当然，还有另一个原因，那就是将字符串传递给 reducer 是不安全和无效的。如果我们打错字了呢？！所以*现在让我们认真起来。**

*我们仍然不想手动创建动作创建者，而且，我们为什么要这样做呢？想想看，`createReducer`已经拥有了制造它们所需的所有信息。它可以从地图的`keys`中获取我们的动作类型。所以，让我们让它既返回一个 reducer 又返回动作创建者，并将其命名为`createModule`*

*整洁！*

*当然，我们还有一些 todos。比如命名我们的动作以避免冲突，允许缩减器处理来自另一个模块的动作。但是我们不会在这篇文章中讨论这个问题。*

*相反，我会向您介绍我制作的[小模块](https://github.com/Emilios1995/redux-create-module)的源代码。*

*感谢阅读！*

*如果你喜欢这个帖子，请在[https://twitter.com/emilio_srg](https://twitter.com/emilio_srg)关注我*

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*