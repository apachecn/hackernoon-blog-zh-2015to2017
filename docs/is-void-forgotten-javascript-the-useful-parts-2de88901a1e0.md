# 虚空被遗忘了吗？JavaScript:有用的部分

> 原文：<https://medium.com/hackernoon/is-void-forgotten-javascript-the-useful-parts-2de88901a1e0>

![](img/b29a93a3fd9de9ab197ed43129f1495c.png)

Photo by [Johnson Cameraface](https://www.flickr.com/photos/54459164@N00/) on [Flickr](https://www.flickr.com/)

> 在许多语言中，`void`是一个没有值的类型。在 JavaScript 中，`void`是一个接受操作数并返回`undefined`的运算符。这个没啥用，而且很混乱。避开`void`。~道格拉斯·克洛克福特。 [JavaScript:好的部分](http://shop.oreilly.com/product/9780596517748.do)

如果你同意这种说法，我不想浪费你的时间，从这里跳出来，继续拯救世界。😎如果不是，让我们来看看为什么 void 是有用的，你不应该避免它。

# 为什么虚空会混淆？

`void 0 === undefined //true`😰 😱 😵

为什么？怎么会？什么是虚空？这是 JavaScript 吗？

当我问开发人员关于虚空的问题时得到的答案，这里主要的困惑是开发人员忘记了或者不知道或者觉得奇怪。

`[**void**](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Operators/void)` **运算符**对给定的`*expression*`求值，然后返回`[undefined](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/undefined)`。这就是你需要了解的关于`void`操作员的所有信息。

# 虚空能有多大用处？

## 1.计算表达式并从函数中跳出

有一次我做了一次代码审查，发现了一个这样的方法:

```
function doStuff(a) {
    if (a) {
        doSomething()       
    } else {
        doSomethingElse()
        return
    } doSomethingMore()
}
```

因此，从这个方法中你可以看到，在`doSomethingElse()`的情况下，我们返回`undefined`并从函数中跳出。让我们保存一行，用`void`让它更易读。

```
function doStuff(a) {
    if (a) {
        doSomething()       
    } else {
        return void doSomethingElse()
    } doSomethingMore()
}
```

从 void 描述中你知道它评估`doSomethingElse()`并返回`undefined`。这正是我们需要的👍

## 2.检查未定义的

旧的浏览器允许`undefined`被重新分配`undefined = 'test'`。但是，ECMAScript 5 在 2009 年修复了这个问题。

[](https://es5.github.io/#x15.1.1.3) [## 带注释的 ES5

### 这是 ECMAScript 规范 5.1 版的带注释的超链接 HTML 版本，其源代码是…

es5.github.io](https://es5.github.io/#x15.1.1.3) 

如果你需要支持旧的浏览器，例如 IE8，这里有一个帮助功能😊

```
function isUndefined(value) {
    return value === void 0
}
```

## 3.链接的 preventDefault()行为

我们都知道没有`href`属性的`a`标签是无效的，但是在 HTML5 中`href`属性是不需要的，所以可以省略。

 [## HTML 5.1: 4.8。链接

### 同样，对于具有 href 属性和属性的和元素，必须为的关键字创建链接

www.w3.org](https://www.w3.org/TR/2016/REC-html51-20161101/links.html#element-attrdef-a-href) 

不知何故，如果你需要阻止`href`作品使用`void`。

```
<a href="javascript: void(0)">About</a>
```

## 4.立即调用的函数表达式

当使用[立即调用的函数表达式](https://developer.mozilla.org/en-US/docs/Glossary/IIFE)时，`void`可用于强制将`function`关键字视为表达式而非声明。

```
void function IIFE() {
    // do something ...
}()
```

# 结论

`void`是一个简单的操作符，我觉得它很有用。我估计，更多的开发者不使用是因为学习的时候没有接触过。当然，如果一个团队感到困惑，最好进行讨论。在帮助中，ESLint 有规则( [no-void](https://eslint.org/docs/rules/no-void2) )。编码快乐！😎

👏**感谢您的阅读。欢迎建议、评论、想法:)**

**如果你喜欢这个，鼓掌吧，跟着我上** [**中**](/@shystruk)**[**推特**](https://twitter.com/shystrukk)**[**github**](https://github.com/shystruk)**和你的朋友们分享一下** ✌️****