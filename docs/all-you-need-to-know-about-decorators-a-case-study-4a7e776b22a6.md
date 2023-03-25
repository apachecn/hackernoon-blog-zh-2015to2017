# 关于@Decorators 你需要知道的一切——一个案例研究

> 原文：<https://medium.com/hackernoon/all-you-need-to-know-about-decorators-a-case-study-4a7e776b22a6>

![](img/7567f8716ee42f049ea227394723b094.png)

***本*条** ***原本出现在***[***dormoshe . io***](https://dormoshe.io/articles/all-you-need-to-know-about-decorators-a-case-study-6)

装饰器是 ECMAScript 中我最喜欢的特性之一。易用性和易于构建使它们如此受欢迎。它们是干净的。它们很简单。他们很酷。他们会留在这里。现在你可以使用它们，你不想忽视它们。大部分开发人员使用装饰者，但他们不知道装饰者是什么，也不知道如何构建装饰者。

我第一次真正见到装修工是在 [Angular 2 落地](https://hackernoon.com/top-8-resources-to-explore-angular-4-ff2c1b42020a)的时候，2016 年 9 月——你还记得吗？😍。他们看着我，我看着他们，这是一见钟情。

在本文中，我们将了解它们是什么，如何构建它们，以及为什么我们不需要害怕它们。我们将从我以前的文章中看到如何构建[rich logger decorator。](https://hackernoon.com/rich-typescript-logger-decorator-for-easy-coding-fc2ff73684c6)

# 等等，什么是室内设计师？

装饰器只是一个函数。

> 装饰器使得在设计时注释和修改类和属性成为可能。

装饰器是一种特殊的声明，可以附加到类声明、方法、属性或参数上。Decorators 使用@expression 形式，其中 expression 必须计算出一个函数，该函数将在运行时使用被修饰声明的信息进行调用。

# 个案研究

在这一节中，我们将看到如何构建 rich-logger-decorator 项目的某些部分。

## 伐木装饰工

@ **记录器**装饰器是一个方法装饰器。在功能开始之前和功能结束之后，将打印日志消息。这个装饰器可以获得定义流行为的选项。

Full logger decorator code

由于 options 参数，这个装饰器需要返回一个函数(所以这是一个装饰器工厂)。一个**方法**装饰器有一个类似于`function(target, methodName, descriptor)`的签名。

它需要三个参数:

*   **目标** —被修饰的原方法。
*   **方法名** —被修饰的方法的名称。
*   **描述符** —给定属性的属性描述符，如果它存在于对象上，否则未定义。属性描述符通过调用*object . getownpropertydescriptor()*函数获得。

函数的第一部分试图获取描述符，对于一些边缘情况(对于**类记录器**装饰器):

Logger decorator code — part I

第二部分是通过访问描述符的值来获取原始方法:

Logger decorator code — part II

第三部分是猴子补丁的原始方法:

Logger decorator code — part III

如您所见，该机制在原始方法调用之前记录了一条消息，在调用之后记录了一条消息。用源参数调用的函数和新方法返回的结果。

在最后一部分，我们将该方法标记为 monkey patched method(稍后将在 **ClassLogger** decorator 中使用),并返回描述符以支持为此方法使用更多的 decorator。

Logger decorator code — part IV

# @ClassLogger

**@ClassLogger** 装饰器是一个方法装饰器。当您将 decorator 放在类定义的顶部时，类中的所有方法都会被自动记录(由 **@DisableLogger** 修饰的方法除外)。

Full class logger decorator code

作为 Logger decorator，这也是一个 decorator 工厂。一个**类**装饰器有一个类似于`function(target)`的签名。目标是对类构造函数的引用。

装饰器需要装饰所请求的方法——所有被装饰的类方法，除非将设置数组选项*logged method names*。设置后，只有数组中的方法会被修饰。

Full class logger decorator code — part I

这段代码获取类的属性(包括类方法)。然后使用*logged method names*数组选项对它们进行过滤。

Full class logger decorator code — part II

现在，循环过滤的项目。如果该项不是一个函数或者它已经被日志记录机制修饰过(根据*_ _ loggermonkey patch completed*)，则该项被省略。

Full class logger decorator code — part III

现在我们像在方法记录器装饰器中一样“猴子修补”这些方法。就是这样。

# @DisableLogger

**@DisableLogger** 装饰器也是一个方法装饰器。 **ClassLogger** decorator 将跳过这个方法不被记录。

Disable logger decorator code

为了这个目的，我们只需要改变布尔值就可以了。这是一个不改变原始方法的简单装饰器的例子。

# 参考

*   [丰富的打字记录器装饰器，方便编码](https://hackernoon.com/rich-typescript-logger-decorator-for-easy-coding-fc2ff73684c6)文章
*   rich-logger-decoratorGitHub 存储库
*   国家预防机制中的富人伐木工装饰工

# 结论

装修工很厉害。它们提高了可读性和可重用性。你可以用 Typescript，Traceur 或者 Bable 把它们编译成 ES5。现在你知道如何构建它们了，所以去编写你的自定义装饰器吧，别忘了分享…

![](img/522b2e4ace3cfcecd43bba30fcf0a317.png)

***您可以关注我的***[***dormo she . io***](https://www.dormoshe.io)***或***[***Twitter***](https://twitter.com/DorMoshe)***了解更多关于 Angular、JavaScript 和 web 开发的内容。***