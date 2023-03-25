# [ec6]高阶函数备忘单

> 原文：<https://medium.com/hackernoon/higher-order-function-cheatsheet-es6-javascript-nodejs-react-tutorial-example-d4f3776f4bcd>

![](img/0617d8bcb2132891c4b1886bdfa8bfd8.png)

# 步骤 0

## 理解经典

为了说明用箭头编写高阶函数，我们来看一个经典的例子:add。在 ES5 中，应该是这样的:

```
function add(x){
  return function(y){
    return y + x;
  };
}

var addTwo = add(2);
addTwo(3);          // => 5
add(10)(11);        // => 21
```

# 用例 1 — C ***复合函数***

## 转型(来自 [IBM](https://developer.ibm.com/node/2016/01/11/higher-order-functions-in-es6easy-as-a-b-c/)

这是将经典的 ***复合*** 函数改写成 HOF 的一个简单例子

```
const add = x => y => y + x;

var addTwo = add(2);
addTwo(3);          // => 5
add(10)(11);        // => 21
```

# 用例 2 —迭代函数

短路 ***迭代函数，*** 如*排序*， *forEach* ，*过滤*，*点出*

```
**const isNumber = n => x => x === n** const arr = ['1','2','3']arr.filter( isNumber("3") )
```

# 这为什么有用？

这很有用，原因如下:

*   它减少了重复的代码
*   它允许更容易地重用代码
*   它增加了代码含义的清晰度

## 你可能也喜欢

*—【ES6】函数式编程:cheat sheet:* [https://medium . com/@ Peter Chang _ 82818/ES6-函数-编程-cheat sheet-更新-传播-注释-示例-教程-26f265b0ddf1](/@peterchang_82818/es6-function-programming-cheatsheet-update-spread-note-example-tutorial-26f265b0ddf1)

# 信用:

[—https://developer . IBM . com/node/2016/01/11/higher-order-functions-in-es6 easy-as-a-b-c/](https://developer.ibm.com/node/2016/01/11/higher-order-functions-in-es6easy-as-a-b-c/)