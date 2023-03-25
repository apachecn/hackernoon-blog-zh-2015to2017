# [ Expressjs ]开裂螺母，覆盖 res.send

> 原文：<https://medium.com/hackernoon/nodejs-express-js-manipulating-response-before-going-back-to-user-5e96ad8d84ca>

![](img/3174e7c17306a459e87457801f8f2b7a.png)

> Express.js 是坚果的外壳，它具有良好的架构和基本的受保护功能，其中的逻辑是坚果的新鲜，它赋予了坚果味道，使坚果与众不同。

我是 Express.js 的忠实粉丝，已经在许多项目中使用它，我最喜欢的部分是中间件模式和路由器设计，它们减轻了构建 HTTP 服务器的痛苦。

我的老板昨天给了我一个特写:

“彼得，我们需要一个东西来监控应用程序中的用户行为。”
“我觉得 GA 已经加在 APP 里了？”
“我们需要来自用户的更多细节，但它们没有存储在 GA 中(像资金流)，GA 有一个缺点，不容易做搜索。”
“我明白了，需要一个模块来存储每个请求的每一个细节(GET、POST、PUT)**，一个日志模块来记录和监控，当错误或异常发生时，至少我们可以从记录中找到*原因。”*

*最好的解决方案是操作 **res.send( )** 函数，只需几行程序:*

```
*var express = require(‘express’) 
var app = express()

// Overwrite res.send
app.use(function(req, res, next) {
 ***var temp = res.send
    res.send = function() {
        console.log(‘do something..’);
        temp.apply(this,***arguments***);
    }
    next();***
})app.get(‘/’, function (req, res) {
res.send(‘Hello World!’)
})*
```

**#拜* [【Javascript】override object . constructor()](https://medium.com/u/b662fb63374b#.22qqwdp14)
[【Expressjs】将 IP 放入黑名单](/@peterchang_82818/expressjs-cracking-nuts-black-list-ip-4787a0850e49#.bxj93og4k)*

*喜欢这个故事？对别人有帮助吗？这有助于我知道你是否想看到更多关于他的话题，并有助于人们看到这个故事， ***当点击下面的心*** 。*

## *参考:*

*[http://stack overflow . com/questions/27885425/express-middleware-before-response-is-to-client](http://stackoverflow.com/questions/27885425/express-middleware-before-response-is-carried-out-to-client)*

*[https://github . com/wahengchang/JavaScript-must-know/blob/master/middleware _ overwrite _ RES . send/app . js](https://github.com/wahengchang/javascript-must-know/blob/master/middleware_overwrite_res.send/app.js)*

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*