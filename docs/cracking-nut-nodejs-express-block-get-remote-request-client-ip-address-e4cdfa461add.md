# [ Expressjs ]破解坚果，把 IP 地址列入黑名单

> 原文：<https://medium.com/hackernoon/cracking-nut-nodejs-express-block-get-remote-request-client-ip-address-e4cdfa461add>

![](img/c07a9086612ab58aa74ea2f349d876f7.png)

> 对付不受欢迎的人的最好办法是把他踢出你的派对。对付不受欢迎的 IP 的最好方法是列入黑名单。

今天的背景是一个运行在 Node.js 服务器上的应用服务，包括一个静态的 HTML 网站和 API 服务。我们手头有一些不受欢迎的 IP 名单，我的老板告诉我“彼得，把他们列入黑名单。”。

我喜欢 Node.js 和 Express，中间件加速的设计我完成了工作，然后早早离开办公室去过周五晚上。

## 这个问题被分解为三个部分:

1.  定义黑名单 IP
2.  获取客户端 IP
3.  阻止客户端 IP，如果它在黑名单中

在 Express.js 中提取客户端 ID 超级简单， ***路由器*** 和 ***req*** 都是加速提取所有客户端信息的工具，包括头、Cookies、帖子正文、获取 URL 参数等。

# 定义黑名单 IP

*将不受欢迎的 IP 放在一个数组中，该数组将在中间件中用于检查路由器将指向哪里。*

```
**var BLACKLIST =['192.0.0.1'];
//better to store as an String in process.env.BLACKLIST**
```

# 获取客户端 IP

*Express 提供了一个超级友好的****req****在几行代码内获取客户端 IP。*

> ***req . connection . remote address***返回的 IP，可能是 ipv4 或 ipv6。

```
var getClientIp = function(req) {
 ***var ipAddress = req.connection.remoteAddress;***if (!ipAddress) {
    return '';
  }// convert from "::ffff:192.0.0.1"  to "192.0.0.1"
 ***if (ipAddress.substr(0, 7) == "::ffff:") {
    ipAddress = ipAddress.substr(7)
  }***return ipAddress;
};
```

## 阻止客户端 IP，如果它在黑名单中

*在 app.use()内增加一个用户阻塞中间件函数(req，res，next)，这个中间件函数会在每次请求 app 时执行。就像一个过滤器，让正确的 IP 通过。*

```
app.use(function(req, res, next) {
  var ipAddress = getClientIp(req);if(**BLACKLIST**.indexOf(ipAddress) !== -1){
    next();
  } else {
    res.send(ipAddress + ' IP is not in whiteList')
  }
});
```

# 把所有的放在一起

这是一个在 Express.js 框架中把以上三部分放在一起的例子。要修改一个适合你的黑名单 IP，去推你的 IP 在黑名单阵列，他们永远不会访问该服务。

```
var express = require(‘express’) 
var app = express()

// Part1, defining blacklist
**var BLACKLIST =['192.0.0.1'];**// Part2, Geting client IP
**var getClientIp = function(req) {
  var ipAddress = req.connection.remoteAddress;****if (!ipAddress) {
    return '';
  }****// convert from "::ffff:192.0.0.1"  to "192.0.0.1"
  if (ipAddress.substr(0, 7) == "::ffff:") {
    ipAddress = ipAddress.substr(7)
  }****return ipAddress;
};**//Part3, Blocking Client IP, if it is in the blacklist
**app.use(function(req, res, next) {
  var ipAddress = getClientIp(req);** **if(BLACKLIST.indexOf(ipAddress) === -1){
    next();
  } else {
    res.send(ipAddress + ' IP is not in whiteList')
  }
});**app.get(‘/’, function (req, res) {
   res.send(‘Hello World!’)
})
```

#感谢[【Javascript】override object . constructor()](https://medium.com/u/be20057073a0#.22qqwdp14)
[【Expressjs】override RES . send](https://hackernoon.com/nodejs-express-js-manipulating-response-before-going-back-to-user-5e96ad8d84ca#.zf7rx3el8)

喜欢这个故事？对别人有帮助吗？这有助于我知道你是否想看到更多关于他的话题，并有助于人们在点击下面的 时看到这个故事。

## 参考:

git: [https://github . com/wahengcang/JavaScript-must-know/tree/master/middleware _ block IP](https://github.com/wahengchang/javascript-must-know/tree/master/middleware_blockIp)

express-IP filter
[https://www.npmjs.com/package/express-ipfilter](https://www.npmjs.com/package/express-ipfilter)

expressjs.com
[http://expressjs.com/en/api.html](http://expressjs.com/en/api.html)

stack overflow
[http://stack overflow . com/questions/10849687/express-js-how-to-get-remote-client-address](http://stackoverflow.com/questions/10849687/express-js-how-to-get-remote-client-address)
[http://stack overflow . com/questions/24896386/request-connection-remote address-now-prefixed-in-ffff-in-node-js](http://stackoverflow.com/questions/24896386/request-connection-remoteaddress-now-prefixed-in-ffff-in-node-js)
[http://stack overflow . com/questions/5999379/](http://stackoverflow.com/questions/5999379/how-to-find-out-the-remote-address-in-node-js-if-it-is-https-request)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)