# AMP，iOS，滚动和位置固定重做—包装方法

> 原文：<https://medium.com/hackernoon/amp-ios-scrolling-and-position-fixed-redo-the-wrapper-approach-8874f0ee7876>

## 新解决方案

AMP 目前正在[试验](https://hackernoon.com/tagged/experimenting)一种实现可滚动 iframes 的新方法。这在 [AMP，iOS，滚动重做 2 中有详细描述——阴影包装方法](/@dvoytenko/amp-ios-scrolling-redo-2-the-shadow-wrapper-approach-experimental-3362ed3c2fa2)。

# 问题和原始解决方案

本文是对原帖 [AMP，iOS，滚动和位置固定](/@dvoytenko/amp-ios-scrolling-and-position-fixed-b854a5a0d451)中描述的问题和解决方案的跟进。

回想一下，AMP 文档经常显示在一个可滚动的 iframe 中。该结构通常如下所示:

```
<html>
  <head>
    <title>I'm a Web App and I show AMP documents</title>
    <style>
      iframe {
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
      }
    </style>
  </head>
  <body>
    <iframe … width="100%" height="100%"
      **scrolling="yes"**
      src="[https://cdn.ampproject.org/c/pub1.com/doc1](https://cdn.ampproject.org/c/pub1.com/doc1)"></iframe>
  </body>
</html>
```

这通常在大多数浏览器中都能很好地工作。我们确实尝试了许多其他方法，包括根据内容调整 iframe 的大小和滚动主文档。但是它们都有明显的功能缺陷和性能问题。详见[原帖](/@dvoytenko/amp-ios-scrolling-and-position-fixed-b854a5a0d451)。

但是，iOS Safari 不支持可滚动的 iframes。换句话说，`scrolling="yes"`被简单地忽略了。参见[本演示](http://jsbin.com/gugika/edit?html,css,output)。长期存在的 iOS Safari 问题可以在[bugs.webkit.org/149264](https://bugs.webkit.org/show_bug.cgi?id=149264)找到。

我们在[ViewportBindingNaturalIosEmbed _](https://github.com/ampproject/amphtml/blob/de7a14d/src/service/viewport-impl.js#L754)中找到了一个原始的解决方法。简而言之，我们滚动文档中实际的`<body>`元素。因此，即使 iframe 本身不滚动，iframe 的内容也会滚动。

生成的 AMP 文档如下所示:

```
<html AMP
    **style="overflow-y: auto; -webkit-overflow-scrolling: touch;"**>
  <head></head>
  <body
 **style="
        overflow-y: auto;
        -webkit-overflow-scrolling: touch;
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
      "**>
    <!-- document content -->
  </body>
</html>
```

iframe 现在滚动了！这是我们在 AMP 中使用了一年的解决方案。然而，随着时间的推移，问题越来越多。在[原帖](/@dvoytenko/amp-ios-scrolling-and-position-fixed-b854a5a0d451)中有详细描述，简而言之:

*   在`<body>`上应用`position:absolute`是意料之外的，会干扰作者风格。例如，一个副作用是我们不允许在`<body>`元素上定制边距。
*   由于[bugs.webkit.org/106133](https://bugs.webkit.org/show_bug.cgi?id=106133)，车身的`scrollTop`、`scrollLeft`、`scrollHeight`和`scrollWidth`不工作。如[原始帖子](/@dvoytenko/amp-ios-scrolling-and-position-fixed-b854a5a0d451#af53)中所述，这可以通过注入带有“假”测量元素的 DOM 来解决。
*   `position:fixed`在`-webkit-overflow-scrolling:touch`集装箱里。参见[bugs.webkit.org/154399](https://bugs.webkit.org/show_bug.cgi?id=154399)。
*   偏移页眉和页脚需要在`<body>`上设置边框，这很昂贵，会减少可滚动区域，有时会破坏现有布局。隐藏标题会导致用户界面的明显跳转和滚动中断。

我们能对此进行改进吗？输入新的解决方案…

# 新的解决方案——包装方法

新的解决方案已编入[ViewportBindingIosEmbedWrapper _](https://github.com/ampproject/amphtml/blob/2d73ac0d9c451dee4c89ac1fa73329b69edca5a4/src/service/viewport-impl.js#L1404)中。

## DOM 结构

在其核心，包装方法类似于可滚动的`<body>`。iframe 在 iOS Safari 中仍然不能滚动，因此我们需要滚动 iframe 的内容。由于滚动`<body>`有很多问题，我们可以创建一个可滚动的包装器，并把它放在`<html>`和`<body>`元素之间。换句话说，我们将在可滚动容器中包装`<body>`元素。

因此，新的 DOM 结构将如下所示:

```
<html AMP
    **style="overflow-y: auto; -webkit-overflow-scrolling: touch;"**>
  <head></head>
 **<i-amp-html-wrapper
      style="
        display: block;
        overflow-y: auto;
        -webkit-overflow-scrolling: touch;
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
      "**>
    **<body style="position: relative;">**
      <!-- document content -->
    </body>
  </i-amp-html-wrapper>
</html>
```

虽然这毫无疑问看起来非常“奇怪”，但它确实解决了最初的问题——它使 iframes 在 iOS Safari 上可滚动。此外，这也解决了上述许多问题:

*   对`<body>`元素没有强制的特殊要求:它通常被定位并具有默认的`overflow:visible`样式。AMP 允许 DOM 中任何地方的大多数 CSS 样式，因此这减少了我们对作者样式的干扰。
*   可滚动的包装元素可用于读取`scrollTop`、`scrollLeft`、`scrollHeight`和`scrollWidth`，因此不再需要[原始帖子](/@dvoytenko/amp-ios-scrolling-and-position-fixed-b854a5a0d451#af53)中描述的“假”测量元素。
*   偏移页眉和页脚不再需要在`<body>`上设置边框——在包装元素上简单填充就足够了。

然而，`position:fixed`问题仍然存在于该解决方案中。稍后将详细介绍。

## 两个元素

我们实现了包装器解决方案，但很快遇到了一个小问题。很多人喜欢使用我们通过在`<html>`和`<body>`之间注入`i-amp-html-wrapper`而打破的`html > body`选择器。作为修正，我们将`i-amp-html-wrapper`重命名为`<html>`。

最终的 DOM 结构如下所示:

```
<html AMP
    style="overflow-y: auto; -webkit-overflow-scrolling: touch;">
  <head></head>
 **<html id="i-amp-html-wrapper"**      style="
        display: block;
        overflow-y: auto;
        -webkit-overflow-scrolling: touch;
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
      ">
    <body style="position: relative;">
      <!-- document content -->
    </body>
  </html>
</html>
```

双倍的“奇怪”，双倍的乐趣。但是 CSS 选择器现在可以正常工作了。

## 履行

AMP 运行时在启动时尽早创建包装器。现有的`<body>`元素简单地在包装器中重新处理:

```
// Create wrapper.
const wrapper = document.createElement(**'html'**);
wrapper.id = **'i-amp-html-wrapper'**;// Setup classes and styles.
wrapper.className = document.documentElement.className;
document.documentElement.className = '';
document.documentElement.style = '...';
wrapper.style = '...';// Attach wrapper straight inside the document root.
**document.documentElement.appendChild(wrapper);**// Reparent the body.
const body = document.body;
**wrapper.appendChild(body);
Object.defineProperty(document, 'body', {
  get: () => body,
});**
```

这段代码非常简单，有一点细微差别——重定义主体会将`document.body`重置为`null`，因此我们需要将`document.body`属性重写回原始的`<body>`元素，这是使用`Object.defineProperty`完成的。

## 位置:已修复问题

虽然包装器方法解决了许多问题，但是`position:fixed`问题仍然存在。

这个问题在[原帖](/@dvoytenko/amp-ios-scrolling-and-position-fixed-b854a5a0d451#4349)中有详细描述。相关的 iOS Safari bug 是[bugs.webkit.org/154399](https://bugs.webkit.org/show_bug.cgi?id=154399)。

简而言之，`-webkit-overflow-scrolling:touch`容器内的一个`position:fixed`元素在滚动时会跳跃和闪烁。看起来像是`position:fixed`元素被轻微滚动了一下，然后很快又跳回了正确的位置。参见此[视频进行演示](https://drive.google.com/file/d/0B_v8thsbiGyDMXZMZkRFZGFRbjA/view?usp=sharing)。

在我们最初的解决方案中，我们将合格的`position:fixed`元素从最初的`<body>`中移出，放入位于`-webkit-overflow-scrolling:touch`容器外的 DOM 中的合成“固定层”元素中。

最终的 DOM 结构如下所示:

```
<html AMP
    style="overflow-y: auto; -webkit-overflow-scrolling: touch;">
  <head></head>
  <html id="i-amp-html-wrapper"
      style="
        display: block;
        overflow-y: auto;
        -webkit-overflow-scrolling: touch;
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
      ">
    <body style="position: relative;">
      <!-- document content -->
    </body>
  </html> **<body id="i-amp-fixed-layer"
      style="
        position: absolute;
        top: 0;
        left: 0;
        right: 0;
        bottom: 0;
        pointer-events: none;
      "**>
    <!-- fixed elements reparented here -->
  **</body>**
</html>
```

因此，我们最终得到两个`<html>`元素和两个`<body>`元素。这看起来完全疯狂，但它确实解决了两个原始问题:iframes 现在滚动和`position:fixed`元素不闪烁。

显然，如果 iOS Safari 的潜在问题得到解决，我们会好得多([bugs.webkit.org/149264](https://bugs.webkit.org/show_bug.cgi?id=149264))。

## 参考

*   原始解决方案:[放大器、iOS、滚动和位置固定](/@dvoytenko/amp-ios-scrolling-and-position-fixed-b854a5a0d451)
*   [Webkit bug:IFrame scrolling = yes 在 iOS Safari 中被忽略](https://bugs.webkit.org/show_bug.cgi?id=149264)
*   [Webkit bug:document . body . scroll top&document . document element . scroll top 跨浏览器不同](https://bugs.webkit.org/show_bug.cgi?id=106133)
*   [Webkit bug:位置固定有溢出漏洞:自动滚动](https://bugs.webkit.org/show_bug.cgi?id=154399)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！