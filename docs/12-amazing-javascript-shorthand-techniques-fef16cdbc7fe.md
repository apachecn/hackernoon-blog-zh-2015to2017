# 12 种优秀的 JavaScript 速记技巧

> 原文：<https://medium.com/hackernoon/12-amazing-javascript-shorthand-techniques-fef16cdbc7fe>

![](img/fc4767fa360ba1ada3f7426077c6f3dc.png)

The best image about JavaScript EVER lol

**更新 1:由于有许多两极分化的评论(如喜欢或讨厌这篇文章),我只想明确一点，速记有时有用，有时无用，这取决于具体情况，不要认为速记代码在任何情况下都是最好的！**

我把这篇文章作为我多年来学习的[速记](https://hackernoon.com/tagged/learning)JavaScript[编码技术的重要参考来源。为了帮助你理解它是怎么回事，我已经包括了手写版本，以给出一些关于短裤的编码观点。](https://hackernoon.com/tagged/javascript)

# 1.Null、未定义的空支票速记

当创建新的变量时，有时你想检查你引用的变量的值是否不为空或未定义。我认为这是 JavaScript 编码人员非常常见的检查。

*手写*

`if (variable1 !== null || variable1 !== undefined || variable1 !== '') { let variable2 = variable1; }`

*速记*

```
let variable2 = variable1  || '';
```

不相信我？自己测试(粘贴到 Chrome 开发工具中，点击运行):

```
//null value example
let variable1 = null;
let variable2 = variable1  || '';
console.log(variable2);
//output: '' (an empty string)

//undefined value example
let variable1 = undefined;
let variable2 = variable1  || '';
console.log(variable2);
//output: '' (an empty string)

//normal value example
let variable1 = 'hi there';
let variable2 = variable1  || '';
console.log(variable2);
//output: 'hi there'
```

# 2.对象数组符号速记

*手写*

`let a = new Array(); a[0] = "myString1"; a[1] = "myString2"; a[2] = "myString3";`

*速记*

`let a = ["myString1", "myString2", "myString3"];`

# 3.如果是真的…否则速记

如果测试为真，这是一个很好的代码节省器，否则使用三元运算符做其他事情。

*手写:*

```
let big;
if (x > 10) {
    big = true;
}
else {
    big = false;
}
```

*速记:*

```
let big = x > 10 ? true : false;
```

如果你依靠 JavaScript 的一些弱类型特征，这也可以实现更简洁的代码。例如，您可以将前面的代码片段简化为:

```
let big = (x > 10);//further nested examples
let x = 3,
big = (x > 10) ? "greater 10" : (x < 5) ? "less 5" : "between 5 and 10";
console.log(big); //"less 5"let x = 20,
big = {true: x>10, false : x< =10};
console.log(big); //"Object {true=true, false=false}"
```

# 4.声明变量速记

我认为这是国外使用最多的社区，尽管我们知道 javascript 使用[提升](https://www.w3schools.com/js/js_hoisting.asp)到你的变量声明。这是一个很好的模式，在顶部和内部声明所有的变量。

*手写:*

```
let x;
let y;
let z = 3;
```

*速记:*

```
let x, y, z=3;
```

# 5.赋值运算符速记

赋值操作符用于给 JavaScript 变量赋值，毫无疑问，你每天都在不假思索地使用算术(不管你用什么编程语言，Java，PHP，C++本质上都是一样的原理)。

*手写:*

```
x=x+1;
minusCount = minusCount - 1;
y=y*10;
```

*速记:*

```
x++;
minusCount --;
y*=10;
```

其他简写运算符，假设 x=10，y=5，下表解释了赋值运算符:

```
x += y //result x=15
x -= y //result x=5
x *= y //result x=50
x /= y //result x=2
x %= y //result x=0
```

# 6.RegExp 对象速记

避免使用 RegExp 对象的示例。

*手写:*

```
var re = new RegExp("\d+(.)+\d+","igm"),
result = re.exec("padding 01234 text text 56789 padding");
console.log(result); //"01234 text text 56789"
```

*速记:*

```
var result = /d+(.)+d+/igm.exec("padding 01234 text text 56789 padding");
console.log(result); //"01234 text text 56789"
```

# 8.如果在场速记

这可能是微不足道的，但值得一提。在进行“if 检查”时，有时可以省略赋值操作符。

手写:

```
if (likeJavaScript === true)
```

速记:

```
if (likeJavaScript)
```

这是另一个例子。如果“c”不等于真，那就做点什么。

手写:

```
let c;
if ( c!= true ) {
// do something...
}
```

速记:

```
let c;
if ( !c ) {
// do something...
}
```

# 9.函数变量参数速记

对象文字速记可能需要一点时间来适应，但是经验丰富的开发人员通常更喜欢它，而不是一系列嵌套的函数和变量。你可以争论哪种技术更短，但是我喜欢使用对象文字符号作为构造函数的简单替代。

*手写:*

```
function myFunction( myString, myNumber, myObject, myArray, myBoolean ) {
    // do something...
}
myFunction( "String", 1, [], {}, true );
```

*速记(看起来很长，但只是因为我有 console.log！):*

```
function myFunction() {
    console.log( arguments.length ); // Returns 5
    for ( i = 0; i < arguments.length; i++ ) {
        console.log( typeof arguments[i] ); // Returns string, number, object, object, boolean
    }
}
myFunction( "String", 1, [], {}, true );
```

# 10.charAt()速记

您可以使用 eval()函数来做到这一点，但是这种括号符号速记技术比求值要干净得多，您将赢得曾经嘲笑您业余编码能力的同事的称赞！

*亲笔:*

```
"myString".charAt(0);
```

*速记:*

```
"myString"[0]; // Returns 'm'
```

# 11.短函数调用

就像#1 一样，您可以使用三元运算符来简化基于条件的函数调用。

*手写:*

```
function x() {console.log('x')};function y() {console.log('y')};
let z = 3;
if (z == 3) 
{
    x();
} else 
{
    y();
}
```

*速记:*

```
function x() {console.log('x')};function y() {console.log('y')};let z = 3;
(z==3?x:y)(); // Short version!
```

# 12.十进制基数指数

你可能见过这个，它本质上是一种没有零的奇特的书写方式。1e7 本质上意味着 1 后面跟着 7 个零，它表示十进制基数(JS 解释为浮点类型)等于 10，000，000。

*手写:*

```
for (let i = 0; i < 10000; i++) {
```

*速记:*

```
for (let i = 0; i < 1e4; i++) {
```

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)