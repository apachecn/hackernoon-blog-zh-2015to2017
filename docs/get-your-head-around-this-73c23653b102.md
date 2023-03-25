# 理解 JavaScript 中的关键字“this”

> 原文：<https://medium.com/hackernoon/get-your-head-around-this-73c23653b102>

## JavaScript 中的“`this"`关键字可能会引起混淆。我们来揭秘一下吧！

![](img/d4265af439f787a8280f164c95c6c4c8.png)

在 [JavaScript](https://hackernoon.com/tagged/javascript) 中，`this`关键字的用法与我们在普通句子中使用它的用法相同:它指的是我们之前已经介绍过的内容。

如果我说:*“我昨天在一个网络大会上遇到了加里·韦纳尔查克。这家伙太厉害了。”*，你马上明白“这家伙”指的是加里·韦纳尔楚克。

同样，在 JavaScript 中使用时，`this`指的是一个对象，更确切地说是调用代码的对象。

考虑这个例子:

→ [自己试试](https://jsbin.com/cijojefuja/edit?js,console)

我们创建了一个带有属性的`customer`对象和一个`greetCustomer`方法。该方法使用关键字`this`来引用`customer`对象。

注意`this`能够访问对象的属性(`firstName`和`lastName`)。这是因为**不仅** `**this**` ***引用*到** `**customer**` **对象，而且*包含*对象**的值:因此它可以访问对象的方法和属性。

当`greetCustomer`方法被调用时，方法内的`this`自动引用调用该方法的对象:对象`customer`。但是在方法被调用之前，这一点非常重要，没有给`*this*` *赋值*。

这是什么意思？

以这个函数为例:

```
var sayCatName = function(catName){
  	console.log("My cat's name is " + catName);
};
```

它要打印什么？嗯，目前还不多，我们已经*定义了*函数，但是我们不知道`catName`的值，直到我们*调用*函数并给它传递一个猫的名字:

```
sayCatName('Alphonse'); // Prints "My cat's name is Alphonse"
```

这也是关键字`this`背后完全相同的想法:我们不知道它指的是什么，直到一个对象调用定义了`this`的函数。换句话说，`this`关键字直到定义它的函数被实际调用时才会被赋值。

所以**每当你试图弄清楚** `**this**` **关键字指的是什么时，问问自己这个问题:*“这个函数在哪里被调用？”*** 。不是定义它的地方，而是调用它的地方。

> 每当你试图弄清楚`this`关键字指的是什么时，问问自己这个问题:*“这个函数在哪里被调用？”*。不是定义它的地方，而是调用它的地方。

这是一个基本的例子，在我们的代码中，有时更难确定`this`指的是什么。现在，让我们深入了解允许我们这样做的四条规则:

*   **隐式绑定**(当`this`在声明的对象内部时)
*   **显式绑定**(当我们使用`call`、`apply`或`bind`显式设置`this`的值时)
*   **全局上下文**(当`this`在声明对象之外时)
*   **`**new**`**关键字**(创建新对象时)**

# **1.隐式绑定(当`this`在声明的对象内时)**

**当关键字`this`在声明的对象中被发现时，`this`的值将总是最近的父对象。**

**用新方法考虑我们的客户对象`whatIsMyObject`:**

**[→自己试试](https://jsbin.com/jimiyehaju/edit?js,console)**

**我们首先在函数`greetCustomer`中找到关键字`this`。最接近的父对象是`customer`对象，这意味着`this`的值是`customer`对象。**

**当我们在函数`whatIsMyObject`中单独打印`this`时，其最近的父对象也是`customer`对象，我们得到了控制台中打印的整个对象，所以我们现在确定`this`的值是`customer`对象。**

**这很好，但是当我们有一个嵌套对象时会发生什么呢？**

**A nested object**

**[→自己试试](https://jsbin.com/zecivoneya/edit?js,console)**

**我们给名为`address`的`customer`对象添加了一个键。`address`的值是另一个对象。在这个对象中，我们有一个`askAddress`函数，它使用`this`来称呼客户的名字。**

**但是`this`似乎无法访问`customer`对象的属性`name`并打印*“亲爱的未定义，请输入您的地址”*。你猜到原因了吗？**

**让我们回到我们的规则，即`this`的值总是最近的父对象:这里我们在`customer`对象中，但也在嵌套的`address`对象中，当它在函数`askAdress`中时，它实际上是`this`最近的父对象。**

**因为`address`对象没有属性`firstName`，所以`this.firstName`是未定义的。我们还可以看到，`address`对象内部的`whatIsMyObject`函数不再引用`customer`，而是引用`address`。**

**那么我们该如何解决这个问题呢？如果我们需要我们的`askAddress`方法返回客户的名字，该怎么办？我们需要用`call`、`apply`或`bind`方法显式地改变`this`的值。**

# **2.显式绑定(当我们使用`call`、`apply`或`bind`显式设置`this`的值时)**

**JavaScript 中的函数是对象，作为对象，它们有几个方法。当我们想要完全控制关键字`this`所指的内容时，我们使用函数的`call`、`apply`和`bind`方法。**

**让我们看一下我们在前面的例子中使用的代码:**

**之前，我们调用的是返回*“亲爱的未定义……”*的`customer.address.askAddress`，因为`askAddress`函数中`this`的值引用了`address`对象，而该对象没有`firstName`属性。**

# **`call()`**

**通过使用`call`方法，我们可以改变我们想要的`this`的值:这里我们将把`this`的值改为`customer`对象:**

```
customer.address.askAddress.call(customer); // Prints "Dear John, please enter your address"
```

**[→自己试试](https://jsbin.com/nireruvoca/edit?js,console)**

**成功了！注意，我们不是*调用* `askAddress`，我们只是*将方法`call`附加到*上，所以当我们使用`call`时在`askAddress`方法后面没有括号。**

**`call`方法的参数是我们希望关键字`this`引用的，在本例中是`customer`对象。**

# **`apply()`**

**当我们对想要使用`call`或`apply`的函数有参数时，就可以看出`call`和`apply`的区别。**

**我们创建了一个新方法`calculateAge`，它有两个参数`currentYear`和`birthDate`。**

**→ [自己试试](https://jsbin.com/zakawamada/edit?js,console)**

**两种解决方案都有效，唯一的区别是:**

*   **使用`call`函数，我们传递由逗号分隔的参数，**
*   **使用`apply`函数，我们将参数作为数组中的值传递**

# **`bind()`**

**`bind`的工作方式与`call`类似，但是*并没有立即调用*函数，而是返回一个函数*定义*，将`this`设置为传递给`bind()`的第一个参数。**

**[→自己试试](https://jsbin.com/nuyajizuyu/edit?js,console)**

**`bind`允许我们明确地将`this`设置为我们想要的对象。它生成一个我们存储在变量`ageOfCustomer3`中的`calculateAge`函数的副本，但是它不会立即*调用*函数。**

**我们可以稍后在代码中调用该函数，并传递它需要执行的参数。创建带有预置参数的函数是非常有用的，这些函数将在以后被调用，而`this`已经被设置为正确的对象。**

# **3.全局上下文(当`this`在声明的对象之外时)**

**`this`是在全局上下文中，当你发现它“在野外”，在一个声明的对象之外(意味着没有一个包含关键字`this`的对象被定义)。**

**在这种情况下，它的值引用全局对象，在浏览器中，它对应于`window`对象。**

**事实上，在全局范围内声明的每个变量实际上都附加到了窗口对象上:**

**[→自己试试](https://jsbin.com/qiyizipujo/edit?js,console)**

**如果我们发现`this`在一个常规的函数调用里面(不是一个对象或者一个对象的方法)怎么办？在这种情况下，`this`关键字也指向全局对象。**

**[→自己试试](https://jsbin.com/zubixiluto/edit?js,console)**

**这里当我调用`greetCustomer`方法时，`nestedFunction`将被调用，并打印全局窗口对象，即使我们在`customer`对象内部。这是为什么呢？**

**这里我们有一个方法内部的常规函数调用，而不是对象的方法。虽然它写在方法内部，但它仍然是一个简单的函数。**

# **4.`new`关键字**

**创建对象的一种方法是使用`new`关键字。**

**考虑定义一个对象的函数 Customer:**

```
function Customer(firstName, lastName) {
  this.firstName = firstName;
  this.lastName = lastName;
}
```

**在这个函数中，我们用关键字`this`定义对象的属性，它们将等于传递给函数的值。**

**等等，我们在一个函数里面。正如我们前面看到的，`this`的值不应该是全局对象吗？**

**但是在这种情况下，我们不会进行常规的函数调用来创建一个新对象。相反，我们将使用`new`关键字，规则是当您使用`new`关键字时，`this`的值会改变。**

```
var customer1 = new Customer("John", "Smith");console.log(customer1.firstName + " " + customer1.lastName); // Prints "John Smith"
```

**[→自己试试](https://jsbin.com/menitozemi/edit?js,console)**

**`this`现在指的是使用`new`关键字时创建的对象。我们将该对象存储在一个名为`customer1`的变量中，该变量现在可以访问`firstName`和`lastName`属性。**

# **结论**

**要知道代码中`this`的值是多少，总是要问自己这样一个问题:**

> ****在哪里调用函数？****

**`this`有一些非常棘手的部分，在本文[中有详细介绍](http://javascriptissexy.com/understand-javascripts-this-with-clarity-and-master-it/):**

> **当我们借用一个使用`this`的方法时，当我们将一个使用`this`的方法赋给一个变量时，当一个使用`this`的函数作为回调函数被传递时，当`this`被用在一个闭包——一个内部函数中时，`this`关键字最容易被误解**

**在使用原生 JavaScript 或 jQuery 之类的库时，您肯定会经常遇到一些现实世界中的问题，在这些情况下，理解`this`的基本工作原理会有很大的帮助。**

**想了解更多？查看我关于 JavaScript 基础的其他文章:**

*   **[**JavaScript 中的吊装:快速指南**](https://hackernoon.com/hoisting-in-javascript-a-quick-guide-cc4d9597bbd7)**
*   **[**轻松理解 JavaScript 变量作用域**](https://hackernoon.com/understand-javascript-variable-scope-with-ease-221a6d41dc43)**
*   **[**如何放心使用 JavaScript 闭包**](/@lenafaure/how-to-use-javascript-closures-with-confidence-85cd1f841a6b)**
*   **[**掌握 JavaScript**](https://hackernoon.com/grasp-by-value-and-by-reference-in-javascript-7ed75efa1293) 中的“按值”和“按引用”**
*   **[**JavaScript 中的日期快速手册**](https://hackernoon.com/a-quick-handbook-for-dates-in-javascript-7b71d0ef8e53)**
*   **[**像老板一样使用 JavaScript 数组**](/@lenafaure/work-with-javascript-arrays-like-a-boss-97207a042e42)**

**我希望你喜欢这篇关于 JavaScript 关键字“this”的介绍。**

**请随意评论并喜欢这篇文章，以便其他人可以在 Medium 上轻松找到它！**

**[![](img/3183c9cfe7a8644bc0a59d2e3e57fa27.png)](http://eepurl.com/dvio9L)****[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 T21 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**