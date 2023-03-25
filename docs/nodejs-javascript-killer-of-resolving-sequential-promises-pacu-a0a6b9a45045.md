# [ Javascript ]连续承诺的黑仔

> 原文：<https://medium.com/hackernoon/nodejs-javascript-killer-of-resolving-sequential-promises-pacu-a0a6b9a45045>

![](img/cede73f2eb017dd57638310cc6d93c07.png)

> javascript 的痛苦是与异步函数斗争，特别是大老板“回调地狱”，当你把数以千计的 console.log 代码变脏，以杀死一个回调错误。它持续了很长时间，直到我发现 Promises，它遵循简单的逻辑，有组织的 resolve-reject 结构，它解决了大多数异步问题，所以感谢 Promises。

然而，一个用参数链接 N 个未知数量承诺并顺序解析它们的用例，无论是通过本地 ES6 承诺还是通过第三方库(q、bluebird、deferred.js 等)似乎都不可能完成。

# ***失败例子:***

> 定义本文中会多次用到的 ***genPromist*** 函数:

```
var genPromist = function(para) {
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            console.log('para: ', para)
            resolve(para * para)
        }, 1000)
    })
}
```

> 链 4 承诺并递归地解决它:

```
var promises = [];
promises.push(genPromist(1));
promises.push(genPromist(2));
promises.push(genPromist(3));var recursiveFunction = function(promisesList) {
    return new Promise(function(resolve, reject) {
        if (promisesList.length <= 0) {
            resolve()
        } else {
            return promisesList[0].then(function() {
                promisesList.shift();
                return recursiveFunction(promisesList);
            });
        }
    });
}recursiveFunction(promises)
```

> 可悲的是，程序并没有按预期运行， ***promises*** 数组被并行解析。当 promise 函数没有参数时，大多数库都工作良好，这意味着 javascript 中的一个类。

# 解决方案:

Javascript 处理 ***承诺函数*** 和 ***带参数承诺函数*** 的方式不同(带参数匿名函数问题)。

> 这样处理这个问题就需要将 ***的许诺函数*** 推送到一个数组列表中

> PACU 是一个工具，用来解决将 promises 函数与参数链接起来并连续解析每个函数的问题。

安装:

```
$ npm install --save pacu
```

用法:

```
var pacu **=** require("pacu")pacu.series(promisesList).then(function(result) { console.log("Result series: ", result)},function(err){ console.log("Error series: ", result)});
```

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿美族家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)