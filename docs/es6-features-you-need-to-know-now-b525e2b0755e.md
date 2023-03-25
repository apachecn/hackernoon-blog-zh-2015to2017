# 初学者入门:你需要知道的 ES6 特性

> 原文：<https://medium.com/hackernoon/es6-features-you-need-to-know-now-b525e2b0755e>

您熟悉原生 JavaScript，并使用它创建了一些应用程序，但是当您进入更广阔的生态系统时，会发现 JavaScript 看起来很陌生。尤其是在你经常听说的那些库的文档中，像“React”、“Redux”、“TypeScript”、“Angular”、“Ember”、“T5”在这里插入很酷的框架/库 >。我个人发现学习任何新的库都很困难，因为需要理解未来的 JavaScript 语言 ES6。

在这篇文章中，我希望涵盖使用 JSBin 测试未来的 JavaScript 特性，并提供示例和练习。这篇文章的目标读者是希望接触 ECMAScript6 最重要部分的初学者(以便阅读和理解当今使用的现代库的文档)。

我们将涵盖:

*   箭头功能
*   休息参数
*   解构分配
*   模板文字
*   班级
*   承诺
*   发电机

我推荐使用 [JSBin](https://jsbin.com/) 来测试这些例子！(确保将 JSBin 语言改为 **ES6 / Babel** )

## 箭头功能

箭头函数可以被认为是编写函数的一种简写方式。以下两个函数是等效的:

```
var fn1 = function (a) {
    return 2 * a
}
```

`var fn2 = a => 2 * a;`

[现场玩一下上面的例子](https://jsbin.com/kiduqisagi/2/edit?js,console)。

您会注意到上面的箭头函数有一些问题。

首先，arrow 函数的参数缺少括号。这是因为只有一个参数`a`。如果我们想传入多个参数，我们需要把那些括号放回去。

`var fn3 = (a, b) => a + b;`

其次，您还会注意到箭头右侧的*隐式返回*。箭头的右边是`return a + b;`的简写。如果我们想在 arrow 函数中做更多的事情，而不仅仅是一个返回行呢？我们重新引入`{}`括号。

```
var fn4 = (a, b) => {
    var result = a + b;
    return result
}
```

最后，如果你想*隐式返回*一个对象，你会遇到一个问题。对象文字符号周围的花括号会干扰声明函数体的花括号。在这种情况下，您需要用括号将对象括起来。

```
// Incorrect return of object literal notation.
var fn5 = () => { name: 'Bob' };// Correct return of object literal notation.
var fn6 = () => ({ name: 'Bob' });
```

值得研究的其他几点:

*   [箭头函数从它们被调用的词法范围绑定“this”。](http://www.2ality.com/2016/02/arrow-functions-vs-bind.html)
*   `arguments`没有在箭头函数中定义(解决方法见下面的 [*剩余参数*](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Functions/rest_parameters) )。

## 休息参数

Rest 参数是一种在数组中捕获传递给函数的任意数量的参数的方法。这类似于`arguments`,但是会给你一个实际的数组。

```
var fn7 = (a, b, ...theRest) => {
    console.log(a);
    console.log(b);
    console.log(theRest);
}fn7('A', 'B', 'C', 'D', 'E');
```

[使用休息参数。](https://jsbin.com/nihasopibo/1/edit?js,console)

*剩余参数*必须是参数列表中的最后一个参数。这种`...`语法也可以用另外一种方式， [*析构*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment) 一个数组。

## 解构分配

这允许将数组或对象分散到不同的变量中。在上面的例子中，我们用`’A’, ‘B’, ‘C’, ‘D’, ‘E’`来调用`fn7`。我们可以将这些字母存储在一个数组中。但是如果我们将一个数组传递到函数中，我们最终会将数组分配给`a`，而不是将数组分割到函数参数中。这里是*析构赋值语法的理想用例，*我们在数组前添加`...`来析构它。

```
var someArray = [’A’, ‘B’, ‘C’, ‘D’, ‘E’];// Using fn7 defined above.// Destructuring assignment. This will split 'someArray'.
fn7(...someArray);/** WRONG **/ fn7(someArray);
```

[玩析构直播](https://jsbin.com/sarohizera/2/edit?js,console)。

尝试使用析构语法的其他有趣的事情在 [MDN 文档](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/Destructuring_assignment)中显示。`…`称为[展开语法](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Operators/Spread_operator)。

## 模板文字

这是编写字符串文字的另一种方式，但允许您将表达式嵌入到字符串中。字符串包含在`'`或`"`中。模板文字包含在反勾号```中。嵌入模板字符串的表达式位于`${}`括号内。

```
var name = 'Mia';
console.log(`Hi my name is ${name}.`);
```

模板文字功能:

*   多线
*   有一个 *raw* 属性允许你访问原始字符串
*   使用*标记的*模板文字进行输出修改

[模板文字的实例](https://jsbin.com/gutademeba/2/edit?js,console)。

## 班级

一个简单的类可以这样定义:

```
class Monster {

  constructor(name){
    this.name = name;
  }

  greetings(){
    console.log(`Hi my name is ${this.name}.`);
  }
}var goblin = new Monster('Jerry'); goblin.greetings();
```

一个类有一个构造函数，当你调用`new Monster`时，这个构造函数就会被调用。在这个类中，构造函数需要一个参数`name`。这个`name`被分配给`this.name`，T3 是一个分配给创建的实例的变量。

[一个基础类的实例](https://jsbin.com/mugituquge/1/edit?js,console)。

这是一篇关于类的精彩文章，我建议你深入研究。

## 承诺

在解释什么是承诺之前，我会试着解释承诺能解决什么问题。

您正在编写一个天气应用程序，它允许用户获取给定位置的当前天气。有一个服务器，在你调用它之后，它会用温度来响应，但这可能需要一些时间。如果您的程序等待服务器提供温度，它将冻结，直到提供温度。另一种选择是*承诺*有一天这个价值会存在。然后对*解析*后的值做一些事情。如果出现错误，承诺可以*拒绝*，而您可以*捕捉*错误。

花点时间看看这个例子并尝试这个练习。

承诺有完整的帖子专门讨论它们，所以我会链接一些帮助我理解它们的材料。

*   [JavaScript 给傻瓜的承诺](https://scotch.io/tutorials/javascript-promises-for-dummies)
*   [https://www.promisejs.org/](https://www.promisejs.org/)
*   [MDN 承诺参考](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Global_Objects/Promise)

## 发电机

生成器是一个可以暂停执行，然后从停止的地方继续执行的函数。当你需要分离逻辑和时间时，这是很棒的。

我使用生成器的一个很好的例子是为[交互式迷宫解算器](https://spyr1014.github.io/mazeSolver/)创建动画。这让我可以制作一个简单的递归路径查找算法的动画，然后冻结/解冻它来绘制每个单独的帧。

在函数声明中使用`function*`声明一个生成器函数，如下所示:

`function* generatorDefinition(){ /** stuff here **/ };`

生成器函数使用`yield` *而不是 return* 来暂停执行并产生一个值。为了让*产生*一个来自生成器的值，在从生成器返回的迭代器上调用`.next()`。这将返回一个具有两个属性的对象(“done”和“value”)。当迭代器遇到一个`return`语句时，它结束，返回`done: true`和值。

使用生成器中的`yield*`调用另一个生成器函数。这就是你如何编写一个递归生成器。

总结一下:

*   生成器返回一个迭代器。
*   打电话。迭代器上的 next()返回一个具有两个属性的对象。“完成”和“价值”。
*   使用`yield*`可以递归生成器。

```
function* trivialGenerator() {
    var i = 0;
    while (true) {
        yield i++;
    }
}var myIterator = trivialGenerator();console.log(myIterator.next());
console.log(myIterator.next());
```

[在这里玩一个活生生的例子，尝试练习](https://jsbin.com/wadusudeme/2/edit?js,console)。

如果你走到这一步，恭喜你！你绝对应该拍拍自己的背。你现在已经接触到了人们使用的语言的主要变化。作为奖励，有一个总结 ES6 的[备忘单。](https://gist.github.com/vasco3/22b09ef0ca5e0f8c5996)

黑客快乐！

*Edit 20/12/2016:改写了 intro，纠正了一些被指出的错别字。*

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个大家庭的一员。我们现在[正在接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请阅读我们的“关于”页面、[喜欢/在脸书上给我们发消息](http://bit.ly/HackernoonFB)，或者简单地发送 [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！