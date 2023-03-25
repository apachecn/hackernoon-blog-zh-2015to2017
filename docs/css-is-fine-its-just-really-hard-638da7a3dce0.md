# CSS 很好，只是真的很难

> 原文：<https://medium.com/hackernoon/css-is-fine-its-just-really-hard-638da7a3dce0>

大家又对 CSS 不爽了。现在是我稳坐高位，写一些讽刺文章让自己感觉更好的时候了，但是这里有一些热门镜头。

[CSS](https://hackernoon.com/tagged/css) 没问题。它真的很难，所以我编译它。

—

我叫 Jordan，我写了很多 JavaScript 和 CSS。我真的，*真的*擅长那两件事。如果 CSS 是奥运会项目，我会轻松晋级，但可能拿不到奖牌。但我不需要奖牌，因为我有一个上面有电脑的奖杯。

![](img/945daca77ab199f60cfcee2c03cbe5fc.png)

Come to my house and tell me I’m bad at CSS

我在电脑和电话屏幕上画矩形已经很久很久了。我写过许多糟糕的 CSS，数千行糟糕的 Less 代码，以及数兆字节的可怕的 Sass。这是我的天性。

但是我也写了很多好的 CSS！我制作了带边框的三角形、带 CSS 变换的贝塞尔曲线、60fps 的滚动动画和会让你大吃一惊的工具提示。

这是一项伟大的技术。给我 30 秒，我可以写一些普通的 HTML，并得到一些漂亮的 Times New Roman 文本和你所见过的最糟糕的蓝色超链接。再给我 30 秒，我会把蓝色调得更好，我会用漂亮的字体。

很直观！如果我想让我所有的链接看起来一样，我可以做到这一点！有漂亮边框和边距的图片？没问题。这就是这东西的用途。

这是表演！许多人已经花了几个世纪来使 CSS 变得快速、可调试、美观。CSS 已经存在，我们能够免费使用如此复杂的工具是一件很棒的事情。更不用说我们可以通过快速的谷歌搜索获得无数的博客文章和很酷的演示。

—

当我年轻的时候，我意识到我希望我的边框和链接文本是相同的颜色——每次在两三个不同的地方改变它是非常糟糕的。后来我发现少了。现在我可以做一个`@wonderfulBlue`并在任何地方使用它。*实际上乔丹 CSS 现在有变量了…*

然后我开始猜测我留下的所有长评论，为什么`#left-section`正好是 546px 宽。(250 * 2 + 23 * 2 咄)。然后我开始用更少的文字写我的数学:`2 * @sectionWidth + 2 * @sectionPadding`。*我猜你不熟悉 calc()，Jordan，因为它有惊人的浏览器支持…*

当`border-radius`必须被多填充时，我开始在任何地方包含所有的前缀，然后将东西转移到一个`border-radius()` mixin 中，我可以在需要的时候直接插入。如果你只用了组件类，伙计，你能停下来吗？让我在这里完成我的文章。抱歉，没事的，别担心，坚持住。

当 CSS 不再适合我的时候，我写得更少了。我向你保证，CSS 仍在编写中，并且在我的用户机器上运行得很好。我只是不是写它的人——我太忙了。

—

我开始在团队中工作，在这些有很多类和变量的大页面上。然后，我的工作包括导航现有的标记、重用变量、将常见的模式重构到它们自己的实用类和混合中，以及开发人员获得报酬要做的所有其他事情。

其中一些页面变得非常大，所以我们经常将 CSS(嗯，更少)和 JavaScript 分成单独的文件，这样用户就不必下载练习页面的代码来观看视频。

有时候，我们删除了太多代码，结果看起来不太对。因为主页菜单期望`.left-arrow`在旁边，但是现在那个类的样式在`exercise.css`里。有时我们不会注意到，因为`.left-arrow`会被整齐地隐藏在导航栏中的几个鼠标点击之后。*嗯，你应该有截图测试或者更严格的质量保证程序—* 我刚才**怎么说的来着**？

唷，这是一项艰苦的工作！但是嘿，代码有时会有错误，这很酷。修好它们，然后继续前进。

这个问题的解决方案以 [BEM](http://getbem.com/) 和 [SMACSS](https://smacss.com/) 的形式存在。你知道，你看到的那些时髦的带有破折号和下划线的类名。是的，它们很可爱，是组织代码的一种非常好的方式。

但是，嗯，这很奇怪。为什么我要花时间手动将我们的 CSS 重构为这些时髦的类名呢？这是自动化的，是繁重的工作，但是充满了人为错误。

—

现在我要讲一个我祖母的个人故事，她用手在穿孔卡片上写机器代码。但是，我奶奶没有那样做。她在福利委员会为我们的参议员工作，没有时间玩电脑，尽管她确实足够聪明。我可以撒谎，但为什么要跳舞？

无论如何，想象一个世界，我的祖母*和*在打孔卡上手写机器代码。再次，充满人为错误！有虫吗？再揍他们一顿。把卡片掉在地板上？把它们都捡起来重新分类，或者重新开始。很奇怪吧？我们不能让机器人为我们做这些吗？

这正是我理论上的祖母所做的，她造了一台机器为她打孔。好吧，她没有，但是别人有！我们得到了很酷的东西，比如汇编代码、FORTRAN 和 c 语言。人们每走一步都用上了相当于 twitter dot com 的东西，并批评这种新技术。**用打卡就行！就用 FORTRAN 吧！就用 C —** 好吧，我想人们还是会这么做。

—

这让我想到了这篇文章。

CSS 很好，很快，并且已经为各种各样的应用程序微调了 20 多年。

但是我真的不喜欢写。很多人不知道，所以我们开发了这些很酷的模式来写它。我也不喜欢用那些模式写作，我有更好的事情要做。JavaScript 很酷。*实际上 JavaScript 还有更多问题—**咳咳*。[所以我用 JavaScript](https://github.com/khan/aphrodite) 写我的 CSS。

转动这个:

```
const Example = () => (
  <h1 className={css(styles.heading, styles.callout)}>
    Hello, world!
  </h1>
)const styles = StyleSheet.create({
  heading: {
    fontFamily: "Comic Sans MS",
  },
  callout: {
    color: "tomato",
  },
  unused: {
    width: 600,
  },
})
```

变成这样:

```
<h1 class="heading_1flg42u-o_O-callout_1ih983s">Hello, world!</h1>....heading_1flg42u-o_O-callout_1ih983s {
    font-family: Comic Sans MS;
    color: tomato;
}
```

看到了吗？还是 CSS。干净的 CSS。完美的 CSS。To-the- **书** CSS。但不是我写的。机器人做到了。没用的东西没了，我可以在任何地方渲染`<Example>` *并且知道它会是什么样子。*

[](/@jdan/rendering-khan-academys-learn-menu-wherever-i-please-4b58d4a9432d) [## 在我喜欢的地方渲染可汗学院的学习菜单

### React 和 CSS-in-JS 的结合节省了我大约两天的工作时间。

medium.com](/@jdan/rendering-khan-academys-learn-menu-wherever-i-please-4b58d4a9432d) 

—

我和你是一个队的。CSS 很棒，如果把它全部替换掉就太傻了。正如 FORTRAN 甚至没有取代低级汇编代码一样， [aphrodite](https://github.com/khan/aphrodite) 和 [styled-components](https://github.com/styled-components/styled-components) 也没有取代 CSS。他们在写 CSS。

答应我你不会再告诉我“学习 CSS”我知道 CSS。向上滚动，我有一个上面有电脑的奖杯。我的 CSS 很棒，但它现在更好，因为我尽可能多地从中删除了人为错误。我们不应该庆祝一下吗？

嘿，我向你保证我不会再说“CSS 不好”了。只是比这篇博文少了那么多字符。它适合一个标签！所以我们还是做朋友吧，好吗？

—

继续吧，在 twitter 上关注我，这样我们就可以在那里互相咆哮了。我主要在推特上发布笑话，偶尔也谈论我的新颜色对比工具，[阴影](http://halogensoftworks.com/shade/)——银河系中最棒的对比工具🚀