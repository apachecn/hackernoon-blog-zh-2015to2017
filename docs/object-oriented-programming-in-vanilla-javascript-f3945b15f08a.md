# 普通 JavaScript 中的面向对象编程

> 原文：<https://medium.com/hackernoon/object-oriented-programming-in-vanilla-javascript-f3945b15f08a>

![](img/00c6dadf2a6255b87ac98ebfbbd3a571.png)

[JavaScript](https://hackernoon.com/tagged/javascript) 是一种强大的面向对象[编程](https://hackernoon.com/tagged/programming) (OOP)语言，然而，与许多传统编程语言不同，它使用基于原型的 OOP 模型，这使得它的语法对大多数开发人员来说是陌生的。此外，JavaScript 还将函数视为一级对象，这可能会让不熟悉这些概念的开发人员更加困惑。

可以通过采用另一种编程语言来回避这些概念，例如 TypeScript，它具有熟悉的语法并提供额外的功能，但是，这种语言无论如何都可以编译成普通的 JavaScript，因此用这些知识武装自己不仅可以帮助您理解它们是如何工作的，还可以帮助您理解何时适合使用它们。

以下是我们将要涉及的主题列表:

命名空间
对象
对象文字
构造函数
继承

# 命名空间

随着 web 上第三方库、框架和依赖性的不断增加，命名空间对于 JavaScript 开发来说是必不可少的，因为我们试图避免全局命名空间中的对象和变量之间的冲突。

不幸的是，JavaScript 没有内置的命名空间支持，但是我们可以使用对象来达到同样的效果。在 JavaScript 中实现名称空间有许多不同的模式，但是我们将讨论嵌套的名称空间，这当然是最常见的一种。

嵌套命名空间模式使用一个对象文字将功能捆绑在一个惟一的、特定于应用程序的名称下。我们可以从创建一个全局对象开始，并像这样将它赋给一个变量:

```
var MyApp = MyApp || {};
```

我们也可以使用相同的技术来创建子名称空间:

```
MyApp.users = MyApp.user || {};
```

一旦我们有了一个容器，我们就可以用它来定义方法和属性，并在我们的全局命名空间中使用它们，而不会有与现有定义冲突的风险。

```
MyApp.users = { // properties existingUsers: [...], // methods renderUsersHTML: function() {
      ...
    }};
```

在这里可以找到对 JavaScript 中命名空间模式的深入概述:[基本 JavaScript 命名空间模式](https://addyosmani.com/blog/essential-js-namespacing/#beginners)。

# 目标

如果您曾经用 JavaScript 编写过代码，那么您已经在某种程度上使用了对象。JavaScript 有三种不同类型的对象:

## 本地对象

本机对象是语言规范的一部分。无论我们的 JavaScript 代码在哪个客户端上运行，它们都是可用的。本地对象的例子有:`Array`、`Date`和`Math`。完整列表请参考 [JavaScript 内置对象参考](https://mzl.la/2bTppcQ)

```
var users = Array(); // Array is a native object
```

## 宿主对象

与本地对象不同，主机对象是由运行 JavaScript 代码的客户端提供的。不同的客户端有不同的主机对象，在大多数情况下，这些对象允许我们与它进行交互。例如，如果我们为一个浏览器编写代码，它为我们提供了宿主对象，比如:`window`、`document`、`location`和`history`。

```
document.body.innerHTML = 'Hello'; // document is a host object
```

## 用户对象

用户对象——有时也称为贡献对象——是我们在运行时定义的自定义对象。在 JavaScript 中有两种方法来声明我们自己的对象，接下来我们将介绍它们。

# 对象文字

在讨论命名空间时，我们已经讨论了对象文字，但是现在是时候给出一个清晰的定义了:对象文字是用大括号括起来的逗号分隔的名称-值对列表。它们可以捆绑属性和方法，像 JavaScript 中的任何其他对象一样，它们可以传递给函数，也可以从函数返回。下面是另一个对象文字的例子:

```
var dog = { // properties breed: ‘Bulldog’, // methods bark: function() { console.log(“Woof!”); },};// accessing methods and propertiesdog.bark();
```

对象文字是单值的。它们最常见的用途是封装代码并将其封装在一个整洁的包中，以避免与全局范围(命名空间)内的变量和对象发生冲突，并将配置传递给插件和对象。

对象文字是有用的，但它们不能被实例化或继承。如果我们想利用这些特性，我们需要探索用 JavaScript 创建对象的另一种方法。

# 构造函数

JavaScript 中的函数被视为一等公民，这意味着它们支持其他实体可用的相同操作。用 JavaScript 术语来说，这意味着函数可以在运行时构造，作为参数传递，从其他函数返回，以及赋给变量。此外，它们可以有自己的属性和方法。这允许我们使用函数作为对象，使*可以被*实例化和继承。

下面是一个使用构造函数定义对象的示例:

```
function User( name, email ) { // properties this.name = name;
  this.email = email; // methods this.sayHey = function() { console.log( “Hey, I’m “ + this.name ); };}// instantiating the objectvar steve = new User( “Steve”, “steve@hotmail.com” );// accessing methods and propertiessteve.sayHey();
```

创建构造函数类似于创建常规函数，只有一点不同:我们使用`this`关键字来声明属性和方法。

使用`new`关键字实例化构造函数类似于在传统的基于类的编程语言中实例化对象，然而，有一个问题可能一开始并不明显。

当我们在 JavaScript 中使用`new`关键字创建新对象时，我们一次又一次地运行函数块，这导致我们的脚本每次都为每个方法声明匿名函数。这将导致我们的程序消耗更多的内存，这可能会严重影响性能，这取决于程序的规模。

幸运的是，有一种更好的方法可以将方法附加到构造函数上，而不会污染全局范围。

## 方法和原型

JavaScript 是一种原型编程语言，这意味着我们可以使用原型作为对象的模板。这将帮助我们在扩展应用程序时避免匿名函数陷阱。`prototype`是 JavaScript 中的一个特殊属性，允许我们向对象添加新方法。

下面是我们之前使用原型的例子的改写:

```
function User( name, email ) { // properties this.name = name;
  this.email = email;}// methodsUser.prototype.sayHey = function() { console.log( “Hey, I’m “ + this.name );}// instantiating the objectvar steve = new User( “Steve”, “steve@hotmail.com” );// accessing methods and propertiessteve.sayHey();
```

在这个例子中，`sayHey()`将被`User`对象的所有实例共享。

# 遗产

原型也通过原型链用于继承。在 JavaScript 中，每个对象都有一个原型，因为原型只是另一个对象，所以它也有一个原型，以此类推……直到我们得到一个值为`null`的原型——原型链中的最后一环。

当我们访问一个方法或属性时，JavaScript 检查它是否在对象定义中定义，如果不是，它将检查原型并查看它是否在那里定义。如果它也不能在那里找到它，它将继续沿着原型链向下，直到找到它，或者直到它到达链的末端。

它是这样工作的:

```
// the user objectfunction User( name, email, role ) { this.name = name;
  this.email = email;
  this.role = role;}User.prototype.sayHey = function() { console.log( “Hey, I’m an “ + role);}// the editor object inherits from userfunction Editor( name, email ) { // The Call function is calling the Constructor of User
   // and decorates Editor with the same properties User.call(this, name, email, "admin"); }// To set up the prototype chain, we create a new object using
// the User prototype and assign it to the Editor prototypeEditor.prototype = Object.create( User.prototype );// Now we can access all the properties and methods
// of User from the Editor objectvar david = new Editor( "David", "matthew@medium.com" );david.sayHey();
```

原型继承可能需要一些时间来适应，但是为了掌握普通 JavaScript 中的 OOP，这是一个重要的概念。虽然它经常被认为是 JavaScript 的弱点之一，但原型继承模型实际上比经典模型更强大。例如，在原型模型之上构建一个经典的继承模型是相当简单的。

*ECMAScript 6 引入了一组新的关键字实现* [*类*](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Classes) *。虽然这些构造看起来像基于类的语言，但它们并不相同。JavaScript 仍然是基于原型的。*

JavaScript 已经发展了很长一段时间，在此期间，许多开发人员已经采用了当今标准应该避免的不同实践。随着 ES2015 的推出，这种情况开始慢慢改变，但是，许多开发人员仍然坚持他们的老方法，这损害了他们代码的相关性。理解并应用 JavaScript 中的 OOP 编程对于编写可持续的代码是必不可少的，我希望这篇简短的介绍能帮助你实现这一点。