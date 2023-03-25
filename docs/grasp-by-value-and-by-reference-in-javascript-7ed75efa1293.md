# 掌握 JavaScript 中的“按值”和“按引用”

> 原文：<https://medium.com/hackernoon/grasp-by-value-and-by-reference-in-javascript-7ed75efa1293>

## 并了解为什么知道其中的区别是至关重要的

![](img/10ff2f130148bd5944194f43d9be5c3b.png)

[https://unsplash.com/](https://unsplash.com/)

JavaScript 是一种面向对象的语言:这意味着 JavaScript 中的大多数东西都是**对象**。例如，函数就是对象。唯一不是对象的元素是**原始数据类型:** *字符串*，*数字*，*布尔*，*空值*和*未定义。*这些原始数据类型也是**不可变的**，这意味着一旦创建就不能修改。

两者的区别之一是原始数据类型通过值传递*，对象通过引用*传递*。*

> 原始数据类型通过值传递*，对象通过引用传递*。**

这是什么意思？你可以这样想:

*   **按值意味着创建原始**的副本。把它想象成双胞胎:他们生来一模一样，但是第一个双胞胎没有失去一条腿，而第二个双胞胎在战争中失去了一条腿。
*   **引用是指为原**创建一个别名。当你妈妈叫你“南瓜派”的时候，虽然你的名字是玛格丽特，但这并不会突然产生一个克隆的你:你仍然是一个，但你可以被这两个截然不同的名字所称呼。

让我们看看原语和对象是如何表现的，首先当我们用[赋值](https://hackernoon.com/tagged/assiognment)操作符(=)给它们赋值时，其次当我们将它们作为参数传递给函数时。

# 1.用=运算符为基本对象赋值

## 对于原始数据类型，=运算符按值工作

考虑以下代码:

```
var name = "Carlos";
var firstName = name;
name = "Carla";console.log(name); // "Carla"
console.log(firstName); // "Carlos"
```

→ [自己试试](https://jsbin.com/naloqarajo/edit?js,console)

结果非常简单:这就是=运算符按值工作。这里实际发生的事情可以简化如下:

*   变量`name`被创建并被赋予值“Carlos”。JavaScript 为它分配一个内存点。
*   变量`firstName`被创建并被赋予`name`值的*副本*。`firstName`有自己的内存点，独立于`name`。这时在代码中，`firstName`也有一个值“卡洛斯”。
*   然后我们将`name`的值改为“Carla”。但是`firstName`仍然保持其原始值，因为它位于不同的内存点。

> 当处理原语时，运算符=创建原始变量的副本。这就是价值的含义。

## 对于对象，=运算符通过引用工作

考虑以下代码:

```
var myName = {
  firstName: "Carlos"
};
var identity = myName;
myName.firstName = "Carla";console.log(myName.firstName); // "Carla"
console.log(identity.firstName); // "Carla"
```

→ [自己试试](https://jsbin.com/fujidanoma/edit?js,console)

这里，包含对象的变量的输出是相同的。发生这种情况是因为，当处理对象时，=操作符通过引用工作。实际发生的情况可以描述如下:

*   变量`myName`被创建，并被赋予一个具有属性`firstName`的对象的值。`firstName`有“卡洛斯”的价值。JavaScript 为`myName`和它包含的对象分配一个内存点。
*   变量`identity`被创建并指向`myName`。*没有专用的内存空间给* `*identity*` *的值*。它只*指向*的`myName`的值。
*   我们将`myName`的`firstName`属性的值改为“Carla”而不是“Carlos”。

当我们记录`myName.firstName`时，它显示新的值，这非常简单。但是当我们记录`identity.firstName`时，它的*也会显示`myName.firstName`的新值“Carla”。这是因为只有`identity.firstName`指向* `myName.firstName`在内存中的位置。

> 处理对象时，运算符=会创建原始对象的别名，而不会创建新对象。这就是“通过引用”的意思。

# 2.将原语和对象传递给函数

## 原始数据类型通过值传递给函数

如果在函数内部更改原始数据类型的值，这种更改不会影响外部作用域中的变量:

```
var myName = "Carlos";
function myNameIs(aName){
  aName = "Carla";
}
myNameIs(myName);console.log(myName); // "Carlos"
```

→ [自己试试](https://jsbin.com/jiciyunaso/edit?js,console)

即使我们在函数`myNameIs`内部改变`myName`变量，当我们在调用函数后打印它时，它仍然有值“Carlos”。这是因为当传递基本类型时，它们是通过值传递的。

我们传递的是`myName`的副本:你在函数体内对`myName`做的任何事情都不会影响全局范围内的`myName`变量，因为你传递的是`myName`的副本，而不是原始的`myName`变量。

## 对象通过引用传递给函数

当你通过引用传递东西时，你传递的是指向其他东西的东西，而不是对象的副本。因此，由于 JavaScript 通过引用传递对象，当您在函数中更改该对象的属性时，更改将反映在外部范围中:

```
var myName = {};
function myNameIs(aName){
  aName.firstName = "Carla";
}
myNameIs(myName);console.log(myName); // Object {firstName: "Carla"}
```

→ [自己试试](https://jsbin.com/weqamuwewu/edit?js,console)

现在，如果我在调用函数`myNameIs`后记录`myName`变量，它会记录一个键为`firstName`的对象，其值等于“Carla”。当我们将对象传递给函数时，它在全局范围内发生了变化。

这是因为当您将一个对象传递给函数时，您传递的不是一个副本。你正在传递一个*将*指向`myName`对象的东西。因此，当您在函数中更改该对象的属性时，您正在更改外部作用域中该对象的属性。

但是有一点你应该注意:

```
var myName = {
  firstName: "Carla"
};function myNameIs(aName){
  aName = {
    nickName: "Carlita"
  };
}myNameIs(myName);
console.log(myName); // Object {firstName: "Carla"}
```

→ [自己试试](https://jsbin.com/zifayokotu/edit?js,console)

这里它打印了外部作用域中变量`myName`的值，这次没有给对象添加一个`nickName`属性。这是为什么呢？如果你仔细观察，我们在函数中所做的是试图给`myName`对象重新赋值。

但是你不能把`myName` *指向*的东西，你只能把`myName`的一个*属性*改成别的，就像这样:

```
var myName = {
  firstName: "Carla"
};function myNameIs(aName){
  aName.nickName = "Carlita";
}myNameIs(myName);
console.log(myName); // Object {firstName: "Carla", nickName: "Carlita"}
```

→ [自己试试](https://jsbin.com/xicuyuneho/edit?js,console)

想了解更多？查看我关于 JavaScript 基础的其他文章:

*   [**JavaScript 中的吊装:快速指南**](https://hackernoon.com/hoisting-in-javascript-a-quick-guide-cc4d9597bbd7)
*   [**轻松理解 JavaScript 变量作用域**](https://hackernoon.com/understand-javascript-variable-scope-with-ease-221a6d41dc43)
*   [](https://hackernoon.com/get-your-head-around-this-73c23653b102)
*   **[**如何自信地使用 JavaScript 闭包**](https://hackernoon.com/how-to-use-javascript-closures-with-confidence-85cd1f841a6b)**
*   **[**JavaScript 中的日期快速手册**](https://hackernoon.com/a-quick-handbook-for-dates-in-javascript-7b71d0ef8e53)**
*   **[**像老板一样使用 JavaScript 数组**](/@lenafaure/work-with-javascript-arrays-like-a-boss-97207a042e42)**

**我希望你喜欢这个“按值”和“按引用”的解释。**

**请随意评论并喜欢这篇文章，以便其他人可以在 Medium 上轻松找到它！**

**[![](img/3183c9cfe7a8644bc0a59d2e3e57fa27.png)](http://eepurl.com/dvio9L)****[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**