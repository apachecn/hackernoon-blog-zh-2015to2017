# lattice 3.0:Rust 中的函数式编程

> 原文：<https://medium.com/hackernoon/lattice-3-0-functional-programming-in-rust-a9dcc86e281a>

…整个王国有许多偏头痛。

我们的 [FRP 窗口库“Lattice”](https://crates.io/crates/Lattice)终于稳定在 API 上了。灰尘正在沉降，结果出奇地干净。在这个库的开发过程中，这个结果曾多次受到质疑。关于这个库允许我们做什么的例子，请看下面的代码:

```
v.append(Text::new("assets/Macondo-Regular.ttf", "paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified paragraph justified").color([0.4, 0.4, 1.0, 1.0]).shadow([-3,-3,3,3],[1.0,1.0,1.0,1.0]).scale(2.0, "em").width(25.0, "%").align("justify").translate_x(150.0, "px").translate_y(150.0, "px"));
```

这段代码使用自定义字体和大量其他显示属性来呈现一个带有两端对齐文本的长段落。如果你眯着眼睛，你甚至可以看到 HTML/CSS 的影子。由于大量的技术进步，这种类型的代码正在成为本地代码。Rust 处于这一趋势的最前沿，它进一步增强了程序员的能力，同时防止了这种强大的抽象带来的大部分风险。

真正令人兴奋的部分始于玻璃钢风格。例如，ReactJS 是一个受 FRP 启发的框架，在本地我们没有强大的抽象。web 有很好的抽象，但代价是巨大复杂的浏览器需求。目前正在原生编程社区中发生的事情是将 web 前端糖果逐渐分解成更小的不可约原生元素。

我现在正在赞助 Rust，但同样的事情也可以说是针对 Mac 和 iOS 的 Swift。这些语言开始吸收函数式编程社区的许多精神，并将这些能量导入到可组合代码中。如果有一个概念要从功能性中去掉，对我来说，那就是可组合性。能够重用概念而不用样板文件是数学还原论哲学的标志。

要分析上面的例子，为什么 FRP 是接口概念的突破，净化和声明性语义是关键。以主导过程风格中的相同代码为例。

```
[Font font = FontFactory.load_font("assets/Macondo-Regular.ttf");](https://crates.io/crates/Lattice)Vec<Glyphs> glyphs = font.render_glyphs("paragraph justified paragraph justified", 2.0*EM)apply_color(glyphs, [0.4, 0.4, 1.0, 1.0]);
apply_shadow(glyphs, [-3,-3,3,3],[1.0,1.0,1.0,1.0]);
textwrap_justify(glyphs, 25.0, "%");
translate_x(glyphs, 150.0, "px");
translate_y(glyphs, 150.0, "px");
```

这段代码有几个问题。第一，数据从哪里来，去哪里。我们可以理所当然地认为，有一些全球性的国家被所有这些因素所操纵。仅此一点就基本上排除了任何干净的线程代码。第二，这些方法的复杂性激增。他们能很好地一起工作吗，或者有相互不兼容的地方。这些是很难测试的东西，即使它们被集成到图形平台中。

这种爆炸式的复杂性是图形平台如此费时和令任何使用它们的人头痛的原因。不是程序员问题，是语言问题。最后，我们有工具来解决这些设计限制，未来是光明的。为更好更快的用户界面干杯。

![](img/4c684fe9018019b0758f1be224b653db.png)

still the best interface we have

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)