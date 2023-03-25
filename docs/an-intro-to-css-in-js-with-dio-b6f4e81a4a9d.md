# DIO 的 JS 中的 CSS 介绍

> 原文：<https://medium.com/hackernoon/an-intro-to-css-in-js-with-dio-b6f4e81a4a9d>

```
function Hello () {
    return {
        render () {
           return h('h1', 'Hello World')
        },
        stylesheet () {
           return `& { color:brown; font-size:40px; }`
        }
    }
}dio.render(Hello)// [http://jsbin.com/dolozi/edit?js,output](http://jsbin.com/dolozi/edit?js,output)
```

DIO[1]是一个 10kb 的框架，很像[反应](https://hackernoon.com/tagged/react)，尽管从前它是 6kb，每增加一个[千字节](https://hackernoon.com/tagged/kilobyte)都是一个拖轮，因为一方面我希望框架足够小，以避免页面权重所面临的问题，另一方面我希望允许组件可以与紧密耦合的 css 一起发布。

所以两个星期前，我得出结论，如果这是可行的，我可能需要解析 css，因为认为你可以用正则表达式编写 css 解析器是天真的，对吗？提示:如果你用 javascript 写一个解析器`. charCodeAt `是你的朋友(比较数字很快)。现在，` stylesheet()'背后的思想类似于` render()'方法背后的思想，一个返回虚拟节点，另一个返回字符串。

因此给出如下

```
stylesheet () {
    return `&{ color:brown; font-size:40px; }`
}
```

这与神秘相去甚远，但下面是你期望 DIO 产生的结果。

```
[scope=Hellojfird]{color:brown;font-size:40px;}
```

这个生成的 css 被缓存以供扩展这个组件的所有实例使用，尽管出于性能原因，这个过程与正常的虚拟 DOM diff 协调是分离的。

# 包装

尽管 css 中的一切都是全局的，除非明确声明，否则我们希望编写下面的代码，而不必用 id 和类过滤它，然后我们将不得不手动附加到我们的组件并跟踪命名冲突。

```
stylesheet () {
    return `
        [@keyframes](http://twitter.com/keyframes) slideIn { 
            from { transform: translate(0px); } 
            50% { transform: translate(200px); } 
            100% { transform: translate(0px); } 
        }

        [@media](http://twitter.com/media) (max-width: 600px) { 
            & { color: red ; } 
        }

        & { animation: slideIn 3s ease infinite; }
   '
}// [http://jsbin.com/mozefe/1/edit?js,output](http://jsbin.com/mozefe/1/edit?js,output) (playground)
```

在幕后，DIO 制作了以下内容

```
@keyframes HellojfirdslideIn {...}
@-webkit-keyframes HellojfirdslideIn {...}@media (max-width: 600px) {
    [scope=Hellojfird] { color: red; }
}[scope=Hellojfird] { 
    animation: HellojfirdslideIn 3s ease infinite;
    -webkit-animation: HellojfirdslideIn 3s ease infinite;
}
```

创建一个样式元素，并将有点模糊的属性“[ scope = hello fird]”附加到相应的组件 dom 节点。

[1]https://github.com/thysultan/dio.js

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！