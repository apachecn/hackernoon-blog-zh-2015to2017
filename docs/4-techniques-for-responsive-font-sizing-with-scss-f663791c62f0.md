# 使用 SCSS 调整字体大小的 4 种技巧

> 原文：<https://medium.com/hackernoon/4-techniques-for-responsive-font-sizing-with-scss-f663791c62f0>

在继续阅读之前，你需要知道这些是什么:

*   [SCSS-米欣斯](http://sass-lang.com/guide#topic-6)
*   [流畅的排版](https://css-tricks.com/snippets/css/fluid-typography/)

# 1.手动重新缩放

```
.title {
  font-size: 25px; @media (min-width: 800px) {
    font-size: 30px;
  } @media (min-width: 1200px) {
    font-size: 40px;
  }
}// And continue doing this for every element
```

缺点:
-大量的工作/编码/测试
-很难保持所有字体大小之间的关系(标题可能会变得太小，以至于在大小上与文本没有区别)

优点:
+完美控制每种屏幕宽度的字体大小

# 2.雷姆单位重新标度

**2.1 响应 REM-单位重新调整**

```
html {
  font-size: 12px; @media (min-width: 800px) {
    font-size: 14px;
  } @media (min-width: 1200px) {
    font-size: 16px;
  }
}.title {
  font-size: 1.5rem;
}.body {
  font-size: 1rem;
}// And continue working with rem-font-sizes
```

**2.2 流体 REM-单位重新调整**

```
html {
  font-size: 16px; // Magic:
  @media (max-width: 1000px) {
    font-size: calc(12px + .4vw);
  }
}.title {
  font-size: 1.5rem;
}.body {
  font-size: 1rem;
}// And continue working with rem-font-sizes
```

缺点:
-人们倾向于用`px`来思考，而不是用`rem`-单位，然而你可以用 mixin 把`px`转换成`rem`。
-(有限的)需要测试来检查字体缩放是否正确
-字体大小之间存在线性关系，这在小屏幕上可能会有问题，正文可能会变得过小，或者标题缩放得不够小。

优点:
+实现速度非常快
+流畅的字体大小令人印象深刻，开发者会一边听 Louis Armstrong 的[多么美妙的世界](https://www.youtube.com/watch?v=A3yCcXgbKrE)一边整天调整屏幕大小

# 3.基于最小-最大值的混合

恬不知耻地从[https://codepen.io/dbox/pen/meaMba](https://codepen.io/dbox/pen/meaMba)(对不起[大牛盒子](https://codepen.io/dbox/))那里偷来的

```
@import 'path/to/fluid-type-mixin';.title {
  [@include](http://twitter.com/include) fluid-type(28px, 52px);
}.body {
  [@include](http://twitter.com/include) fluid-type(14px, 20px);
}
```

这会生成以下 css:

```
.title {
  font-size: calc(28px + 24 * ( (100vw - 420px) / 480));
}
[@media](http://twitter.com/media) screen and (max-width: 420px) {
  .title {
    font-size: 28px;
  }
}
[@media](http://twitter.com/media) screen and (min-width: 900px) {
  .title {
    font-size: 52px;
  }
}.body {
  font-size: calc(14px + 6 * ( (100vw - 420px) / 480));
}
[@media](http://twitter.com/media) screen and (max-width: 420px) {
  .body{
    font-size: 14px;
  }
}
[@media](http://twitter.com/media) screen and (min-width: 900px) {
  .body{
    font-size: 20px;
  }
}
```

缺点:
-开发者必须将每种字体大小的最小值和最大值传递给 mixin。
-很难知道下限和上限之间某个点的字体大小

优点:
+简单易用

# 4.RFS-mixin 的自动字体缩放

恬不知耻地指着自己做的一个 mixin:[https://github.com/twbs/rfs](https://github.com/twbs/rfs)

```
@import "~rfs/scss";p {
  [@include](http://twitter.com/include) rfs(20);
}h1 {
  [@include](http://twitter.com/include) rfs(64);
}
```

这会生成以下 css:

```
p {
  font-size: 1.25rem;
}h1 {
  font-size: 4rem;
}

@media (max-width: 1200px) {
  h1 {
    font-size: calc(1.525rem + 3.3vw);
  }
}
```

缺点:

*   你依赖于像 Sass、Less 或 Stylus 或 PostCSS 这样的前处理器或后处理器。

优点:

*   字体大小将为每个屏幕或设备重新调整，这可以防止长单词在小设备上被截断
*   RFS 将防止字体大小变得太小，从而保证可读性
*   超级容易使用，只要使用`font-size` mixin(或者 PostCSS 的`responsive-font-size`属性)而不是`font-size`属性
*   所有文本元素的字体大小将始终保持相互关联

在 [github](https://github.com/project-rfs/rfs) 上下载 RFS 或者用 [npm](https://www.npmjs.com/package/rfs) 安装。