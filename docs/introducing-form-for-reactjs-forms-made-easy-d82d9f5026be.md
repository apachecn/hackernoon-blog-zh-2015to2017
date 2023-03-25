# 介绍表单-for。ReactJS 表格变得简单

> 原文：<https://medium.com/hackernoon/introducing-form-for-reactjs-forms-made-easy-d82d9f5026be>

## 带有 ReactJS 的简单、声明性和强大的表单

![](img/8b7da2fe4b4794849a442244e7578849.png)

Photo by Mikhail Vasilyev — [https://unsplash.com/photos/NodtnCsLdTE](https://unsplash.com/photos/NodtnCsLdTE/share)

*如果只是为了链接而来，只需* [*点击这里*](https://github.com/form-for) *。*

我做了几年代码，玩过几个技术。在这条路上，我成为了一个叫做 [simple_form](https://github.com/plataformatec/simple_form) 的 Rails gem 的忠实粉丝。我也是 React 的粉丝。考虑到这两点，我决定构建一个具有 simple_form 的简单性和 React 的定制能力的库。它叫做`form-for`

简而言之，这就是带有`form-for`的表单的样子。

起初它看起来像魔术，但是，虽然我认为它很酷，但它并不完全是魔术。Form-for 使用一个`schema`来决定为每个`field`显示什么组件。

现在，您已经从更广阔的角度了解了它的工作原理，让我们一步一步地创建一个模式，绑定我们的组件，并向您展示一个真实的例子。

Source: [https://giphy.com/gifs/bbcamerica-elijah-wood-dirk-gently-l378kNMpVWNmilDLa](https://giphy.com/gifs/bbcamerica-elijah-wood-dirk-gently-l378kNMpVWNmilDLa)

## 1.创建模式

使用`form-for`有几种方法可以实现这一点。对于这篇文章，我将坚持使用更好的。

让我们创建一个用户模型。我们的用户将有一个名字，姓氏，电子邮件，电话号码和访问(用户/客人)。我们将用`@field`注释每个字段，这样`form-for`就知道如何显示它。

## 2.绑定组件

正如您在上面的代码中看到的，每个字段都有一个类型设置；有些甚至有几个额外的属性。

现在，我们需要告诉`form-for`渲染什么组件。我为引导组件创建了一个包，让您的生活变得非常简单。它还旨在促进 ***无障碍*。**

## 3.构建表单

现在，让我们将用户、引导组件以及`Form`和`Field`标签放在一起。

## 4.活着

我创建了我们创建的表单的扩展版本。在这个程序中，用户还有一个待办事项列表，这增加了一种新的乐趣。代码非常简单，就像您在上面看到的那样。

如果你想要更多的沙盒例子，我的简介里还有另外两个:[https://codesandbox.io/u/pedsmoreira/sandboxes](https://codesandbox.io/u/pedsmoreira/sandboxes)

## 4.1 MobX

FormFor 与 MobX 的配合非常好。我强烈推荐试试:【https://codesandbox.io/s/qz087nv8nj

## 5.被卖方收回的汽车

[](https://github.com/form-for/form-for) [## 形式对形式

### 用于构建的表单从您的模型中获取简单的和声明性的 ReactJS

github.com](https://github.com/form-for/form-for) 

看看回购协议，里面有更多的说明。喜欢的话，分享给朋友同事。我期待在下面的评论或 GitHub PR/Issue 中听到你的想法。

如果你也不喜欢评论，让我知道你怎么认为它会更好。

[https://giphy.com/gifs/transparent-GwGXoeb0gm7sc](https://giphy.com/gifs/transparent-GwGXoeb0gm7sc)

*我是* [*佩德罗·莫雷拉*](https://twitter.com/pedsmoreira) *，我为公司和创业公司开发软件。我相信通过开源为社区做贡献，我自己也创建了几个项目。其中之一就是*[*git showcase*](https://www.gitshowcase.com/)*，一个帮助开发者展示才华并找到梦想工作的平台* ❤

https://twitter.com/pedsmoreira

GitHub:[https://github.com/pedsmoreira](https://github.com/pedsmoreira)

[](https://www.gitshowcase.com/) [## GitShowcase

### 开发人员，在即插即用产品组合中展示您的最佳项目。最棒的是，免费的。

www.gitshowcase.com](https://www.gitshowcase.com/)