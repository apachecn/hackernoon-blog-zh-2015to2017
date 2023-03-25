# 在 Twitter 上的一条推文中托管一个 web 应用程序

> 原文：<https://medium.com/hackernoon/host-a-web-app-on-twitter-in-a-single-tweet-9aed28bdb350>

我喜欢 Twitter，也喜欢编程的约束。

我理解 Twitter 坚持 140 个字符限制的所有原因，但我不得不离开 Twitter 去参与任何我可能感兴趣的内容，这真的让我很恼火。在大多数情况下，外部链接要么充斥着广告，要么网站的表现糟糕透顶。对于我自己的内容，我真的不想拥有自己的网站或使用独立于我在 Twitter 上的连接的外部服务。

所以在假期里，我尝试了一种技术，可以将我的网络应用嵌入 Twitter。看看我的视频，看看它是如何工作的:

基本上，这是通过将应用程序的 HTML/CSS/JAVASCRIPT 嵌入到一个 PNG 图像中来实现的，然后这个 PNG 图像被托管在 Twitter 上。就 Twitter 而言，它只是一个普通的旧图像，并以此对待它。有关在图像中嵌入文本的更多信息，请查看以下内容:

[](https://en.wikipedia.org/wiki/Steganography) [## 隐写术-维基百科

### 这个术语的第一次使用记录是在 1499 年由约翰尼斯·特里特米乌斯在他的《隐写术》中，一篇关于…

en.wikipedia.org](https://en.wikipedia.org/wiki/Steganography) 

图像的编码和解码由两个简单的浏览器书签完成。书签小程序以及如何创建书签小程序的描述如下:

 [## 书签-维基百科

### bookmarklet 是存储在 web 浏览器中的一个书签，它包含 JavaScript 命令，这些命令将新功能添加到…

en.wikipedia.org](https://en.wikipedia.org/wiki/Bookmarklet) 

下面的 bookmarklet 将 [HTML](https://hackernoon.com/tagged/html) 编码成一个 PNG 图像:

```
javascript: (function() {
    function encode(a) {
        if (a.length) {
            var c = a.length,
                e = Math.ceil(Math.sqrt(c / 3)),
                f = e,
                g = document.createElement("canvas"),
                h = g.getContext("2d");
            g.width = e, g.height = f;
            var j = h.getImageData(0, 0, e, f),
                k = j.data,
                l = 0;
            for (var m = 0; m < f; m++)
                for (var n = 0; n < e; n++) {
                    var o = 4 * (m * e) + 4 * n,
                        p = a[l++],
                        q = a[l++],
                        r = a[l++];
                    (p || q || r) && (p && (k[o] = ord(p)), q && (k[o + 1] = ord(q)), r && (k[o + 2] = ord(r)), k[o + 3] = 255)
                }
            return h.putImageData(j, 0, 0), h.canvas.toDataURL()
        }
    }
    var ord = function ord(a) {
            var c = a + "",
                e = c.charCodeAt(0);
            if (55296 <= e && 56319 >= e) {
                if (1 === c.length) return e;
                var f = c.charCodeAt(1);
                return 1024 * (e - 55296) + (f - 56320) + 65536
            }
            return 56320 <= e && 57343 >= e ? e : e
        },
        d = document,
        b = d.body,
        img = new Image;
    img.src = encode(d.querySelector("table").innerText), b.innerHTML = "", b.appendChild(img)
})();
```

这将图像解码回 HTML 并插入到现有的网页中:

```
javascript: (function() {
 d = document;
 t = d.querySelector("img");
 var s = String.fromCharCode,
  c = d.createElement("canvas");
 var cs = c.style,
  cx = c.getContext("2d"),
  w = t.offsetWidth,
  h = t.offsetHeight;
 c.width = w;
 c.height = h;
 cs.width = w + "px";
 cs.height = h + "px";
 cx.drawImage(t, 0, 0);
 var x = cx.getImageData(0, 0, w, h).data;
 var a = "",
  l = x.length,
  p = -1;
 for (var i = 0; i < l; i += 4) {
  if (x[i + 0]) a += s(x[i + 0]);
  if (x[i + 1]) a += s(x[i + 1]);
  if (x[i + 2]) a += s(x[i + 2])
 }
 var dd=d.documentElement;
 while(dd.firstChild){dd.removeChild(dd.firstChild)};
 var doc = (new DOMParser()).parseFromString(a, "text/html");
 var head=(new Range()).createContextualFragment(doc.head.outerHTML);
 var body=(new Range()).createContextualFragment(doc.body.outerHTML);
 dd.appendChild(head);
 dd.appendChild(body);
})();
```

当然，这种技术并不局限于 Twitter，图片可以放在你选择的任何地方。

简单而实用。

我很想听听你的反馈！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)