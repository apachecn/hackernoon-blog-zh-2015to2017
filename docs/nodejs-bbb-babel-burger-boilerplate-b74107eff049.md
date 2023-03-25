# BBB，巴别塔汉堡样板

> 原文：<https://medium.com/hackernoon/nodejs-bbb-babel-burger-boilerplate-b74107eff049>

![](img/12c26fdd9bec6c524b6d19830b22c738.png)

pixabay.com

> 如今编码就像吃快餐:做得快，吃得快，只要工作在截止日期前完成。你会犯很多错误，你会回来犯更多的错误。

在开发周期中，一半的时间花在新框架和分解特性上，另一半时间花在实现上，故事的目标是缩短环境配置的时间，并开始编码。

最近在 Node.js 里面玩 ES7 async/await，需要 ES7 环境。有几个基于伟大的 SDK babeljs.io 的方法来实现这个环境，我挑选了一个作为样板并与大家分享。

**备注:本故事针对节点 v4。并且最新的节点支持原生 ES7，感谢** [**斯蒂芬·博比夫**](https://medium.com/u/5e55b6f40caf?source=post_page-----b74107eff049--------------------------------)

*步骤 1 —* [*从 github*](https://github.com/wahengchang/node-es7-babel-template) *中克隆模板，并安装。*

```
$ git clone git@github.com:wahengchang/node-es7-babel-template.git
$ npm install
```

*步骤 2——基本上环境已经设置好，让我们在 ES7 中尝试一些东西，看看它是否工作良好。去编辑****" main . js "****文件，这是 ES7 编译器的默认入口，有一个 sleep()函数是 Promise 函数，之后会被惊艳的 ES7 调用。*

```
//    ./[.gitignore](https://github.com/wahengchang/node-es7-babel-template/blob/master/.gitignore)
//    ./[README.md](https://github.com/wahengchang/node-es7-babel-template/blob/master/README.md)
//    ./[es7compiler.js](https://github.com/wahengchang/node-es7-babel-template/blob/master/es7compiler.js)
//    ./[main.js](https://github.com/wahengchang/node-es7-babel-template/blob/master/main.js)
//    ./[package.json](https://github.com/wahengchang/node-es7-babel-template/blob/master/package.json)//main.js
module.exports = function() { (async function() {
        var sleep = function(para) {
            return new Promise(function(resolve, reject) {
                setTimeout(function() {
                    console.log('para: ', para)
                    resolve(para * para)
                }, 1000)
            })
        } var result = await sleep(2);
        console.log('result: ', result);
    }());}
```

*步骤 3 —运行在步骤 2 中编辑的脚本，完成。*

```
$ npm start
//para:  2
//result:  4
```

## 参考:

github:
https://github.com/wahengchang/node-es7-babel-template

通天塔必读:
http://babeljs.io/learn-es2015/

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)