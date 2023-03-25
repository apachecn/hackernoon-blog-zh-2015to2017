# [Javascript] ES7 异步等待圣经

> 原文：<https://medium.com/hackernoon/javascript-es7-async-await-bible-tutorial-example-32294f6133ab>

![](img/0888f18994134699ca8d258dfd69cb00.png)

## 8 个必须知道的例子:

1.  异步函数对象
2.  承诺函数
3.  异步功能
4.  依次等待
5.  并行等待
6.  等待嵌套
7.  动态等待
8.  错误句柄

## 注意

*—要在 Node.js 上运行 ES7，请阅读:* [BBB，Babel Burger Boilerplat](https://hackernoon.com/nodejs-bbb-babel-burger-boilerplate-b74107eff049#.5x9fvu8ji) e

> 在 ES5 时代之前，JavaScripters 们生活在回调地狱中。调试是一项疯狂的工作，被成千上万的回调函数窒息。在后来的时间里，承诺来拯救我们，它的魔力把地狱变成了一个扁平的结构，隧道尽头的一盏灯。今天，Async Await 是这一承诺的化身。ES7。

![](img/0a7feb941fb5687e033aef9e8203e04b.png)

Chronological Picture

# 1-异步的构造函数: [asyncFunction](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction)

AsyncFunction 构造函数创建一个新的异步函数对象。在 JavaScript 中，每个异步函数实际上都是一个 Async function 对象。来自 Mozilla :

```
//Node.js
console.log(async function () {});// Chrome or Firefox
console.log(async function () {}.constructor);
console.log(async function () {}.__proto__);
```

(感谢[阿尔宾·拉尔森](https://medium.com/u/cad910df79a0?source=post_page-----32294f6133ab--------------------------------)的更正)

谁知道非功能性和承诺的兄弟姐妹？如果不是，则不允许跳过，因为这是 ES6 的承诺函数支持 await 的原因，也是 Async await 如何应用承诺特性的原因，例如*解析并行*和*错误处理*:

```
function(){
  ...
  return new ***Promise***(function(resolve, reject) {
    ...
  })
  ...
}
```

# 2-构造承诺函数

为什么我们在这里讨论承诺函数？因为人们可能忽略了 Async await 的基础是承诺，所以我们创建的每一个 Async 函数和我们等待的每一件事都会自然而然地成为承诺。

***举例(感谢*** [***乔特曼演唱***](https://medium.com/u/423e5feec011?source=post_page-----32294f6133ab--------------------------------) ***的建议):***

```
(async function() {var sleep = function(para) {
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            resolve(para * para)
        }, 1000)
    })
  }var result = await sleep(2)
  // result is 4})();
```

# 3-构造异步函数

`**async function**`声明定义了一个*异步函数*，它返回一个`[AsyncFunction](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction)`对象(也是一个承诺)。当异步函数返回值时，将使用返回值解析承诺。当 async 函数抛出异常或某个值时，promise 将被抛出的值拒绝( [Mozilla](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/async_function) )。

***举例:***

```
async function asyncSleep (para){
 return await sleep(para)
}var result = await asyncSleep(2)
//result is 4asyncSleep(3).then(function(result2){
//result2 is 9
})
```

# 4-顺序等待

***例如:***

```
var result1 = await sleep(2);
var result2 = await sleep(result1);
var result3 = await sleep(result2);//result1 is 4
//result2 is 16
//result3 is 256
```

# 5-并行等待

***举例:***

```
var results = await Promise.all([sleep(1), sleep(2)]);
//results is [1,4]
```

# 六窝

***例如:***

```
// iterator of objects
// for (let thing of things) {for(var i =0 ; i<3; i++){
    var result = await sleep(i);for(var j =0 ; j<result; j++){
        console.log('   i:'+i+', j:'+j+': ', await sleep(j));
    }
}// i:1, j:0:  0
// i:2, j:0:  0
// i:2, j:1:  1
// i:2, j:2:  4
// i:2, j:3:  9
```

# 7-动态异步函数

> JavaScripters 爱好者们，是时候承认了:我们在承诺方面有问题。

我很赞同 [*诺兰·劳森*](https://twitter.com/nolanlawson) *的*引用*的话。*动态承诺数组是在处理未知进程、动态问题时需要用到的。对于这个问题，我的直觉是将所有的 Promise 函数放入一个数组中，然后依次等待它们。但是当我发现 Promise 函数在被声明的时候就执行了，这被证明是不正确的。我的结论是，用承诺解决问题，不要相信人的本能。

这种方法是将参数和承诺分开存储(这里使用的是 map 对象)

***举例:***

```
var sleep = function(para1,para2) {
    var _para1 = para1, _para2 = para2 || para1 ;
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            resolve(_para1 * _para2)
        }, 1000)
    })
}var proMap = new Map();
proMap.set([1], sleep);
proMap.set([2, 3], sleep);
proMap.set([3], sleep);for (var [para, fun] of proMap.entries()) {
    var result = await fun.apply(this, para);
    console.log(para, result)
}//[ 1 ] 1
//[ 2, 3 ] 6
//[ 3 ] 9
```

# 8-错误句柄

> 在一个`async`函数中，错误被“悄悄地”吞下——就像在正常的承诺中一样。

**异步**和**承诺**中的错误处理来自同一个母体，它们都需要 try/catch，以便在异步函数中捕获并处理等待的承诺中的错误。

***例如:***

```
var errorSleep = function(para) {
    return new Promise(function(resolve, reject) {
        setTimeout(function() {
            reject(' ErrorSleep')
        }, 1000)
    })
}***try {* **    var result1 = await sleep(1);
    var result2 = await errorSleep(4);
    var result3 = await sleep(1);console.log('result1: ', result1)
    console.log('result2: ', result2)
    console.log('result3: ', result3)
***} catch (err) {* **    console.log('err: ', err)
    console.log('result1: ', result1)
    console.log('result2: ', result2)
    console.log('result3: ', result3)
***}***//err:   ErrorSleep
//result1:  1
//result2:  undefined
//result3:  undefined
```

# 总结:

对于 nodejs 和浏览器程序员来说，ood，async/await 都是一个很好的语法改进。与承诺相比，这是到达同一个目的地的捷径。它帮助开发人员用 JavaScript 实现函数式编程，增加代码可读性，使 JavaScript 更有趣。

B ad，正如在第 7 和第 8 部分中提到的，Promise 在动态生成实例和解析实例方面有问题，并且当发现意外异常时，await 应该给 Javascripter 程序一个更大的声音。

喜欢这个故事？对别人有帮助吗？它帮助我知道你是否想看到更多关于他的话题，并帮助人们看到这个故事， ***当点击下面的心*** 。

# 参考:

Mozilla:
[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Global _ Objects/async function](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/AsyncFunction)

尼古拉斯·贝瓦夸
[https://pony foo . com/articles/understanding-JavaScript-async-await](https://ponyfoo.com/articles/understanding-javascript-async-await)

[诺兰·劳森](https://twitter.com/nolanlawson)
[https://pouchdb . com/2015/05/18/we-have-a-problem-with-promises . html](https://pouchdb.com/2015/05/18/we-have-a-problem-with-promises.html)