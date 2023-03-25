# 让我们建立一种编程语言

> 原文：<https://medium.com/hackernoon/lets-build-a-programming-language-2612349105c6>

![](img/55c4d4e927933a3558de297880557aaf.png)

[The Great Wave off Kanagawa](https://en.wikipedia.org/wiki/The_Great_Wave_off_Kanagawa), [Katsushika Hokusai](https://en.wikipedia.org/wiki/Katsushika_Hokusai)

作为一个编写代码的人，你无疑会使用一种或多种编程语言。你可能喜欢用一些编程语言编写代码，因为它们的优雅、表达能力或任何其他原因，你也可能与其他编程语言保持距离，可能是因为它们的一些特性你认为实现得不好。

然而，你想过那些我们又爱又恨的编程语言是怎么来的吗？我们喜欢(或不喜欢)的特定功能是如何设计和实现的，为什么？那些作为编译器和解释器的神奇黑盒是如何工作的？用 JavaScript、Ruby、Python 等语言编写的代码如何变成可执行程序？或者有没有想过构建自己的编程语言？

如果你对以上任何一个问题的回答是肯定的，欢迎阅读关于**构建编程语言**的系列文章。

本系列将带您从 0 到 1 构建一种编程语言的函数解释器。在本系列的最后，您将拥有一个解释器，它是您从头构建的，运行用我们一起设计的编程语言编写的程序。

# 为什么？

为什么要学习如何实现一门编程语言？

*   **构建编译器或解释器会让你成为更好的程序员。**编译器和解释器包含有趣的数据结构和算法，其知识对其他领域是适用和有用的。您可以将这些知识应用于编写不同文件格式的解析器，构建特定领域的语言，例如数据库查询语言等等。您还将更好地理解如何以及何时优化代码，更好地为特定任务选择最佳编程语言，并且您将最终理解您的编译器/解释器有时会抛出的一些奇怪的错误消息:)。
*   **为开发你最喜欢的编程语言**做出贡献。许多有趣的编程语言都是开源的，并欢迎新的贡献者，但通常，对于大多数从未上过 CS 编译器课程的人来说，贡献所需的知识是一个入门障碍。如果你关心你喜欢的编程语言和/或曾经想过为它的发展作出贡献，在你已经建立了一个玩具编译器或解释器之后，你将会更好地做好准备。
*   很有意思。:)

# 怎么会？

本文是一个系列的介绍，其中的每一篇文章都将介绍在实现编程语言中完成一个特定步骤所必需的概念和知识。每篇文章都以挑战结尾(没有挑战就不好玩了吧？).这个挑战将提示你实现一个定义良好的解释器组件。在每篇文章的结尾都有一些测试文件可以下载，你可以通过编写代码来完成所有的测试。当你完成所有的挑战时，你将拥有一个完整的工作解释器，可以运行用我们的编程语言编写的代码。

尽管任何编程语言都可以用来完成这些挑战，但是对于本系列，我们的实现语言将主要是 [JavaScript](https://en.wikipedia.org/wiki/JavaScript) ， [ES6](https://en.wikipedia.org/wiki/ECMAScript#Harmony.2C_6th_Edition) 更准确地说，以便本系列可以被更广泛的受众访问。

# 什么？

如果以上听起来都不错，那么让我们从描述我们将要实现的编程语言开始。我们专门为这个系列设计的编程语言叫做 **Blink** 。这是一种受 Swift、Kotlin 和 Scala 启发的解释型、面向对象和静态类型的编程语言。

# 眨眼之旅

## 你好，眨眼！

典型的*《你好，世界》*是这样写的。

```
Console.println("Hello, Blink!")
```

## 数据类型

Blink 支持常见类型的值，如`Int`、`Double`、`String`和`Bool`。此外，还有一个特殊的类型`Unit`来表示值的缺失(类似于 Java 中的`void`)，并且 Blink 中的所有类型都继承自一个超类型`Object`。

## 评论

Blink 中的一条评论以`//`开头。从`//`符号到行尾的任何内容都将被 Blink 解释器忽略。

```
// This is a comment.
```

## 声明变量

使用`let` *表达式*声明变量。

```
**let** message: **String** = "Hello, Blink!" **in** {
    Console.println(message)
}
```

一个`let`表达式由两部分组成:

*   **声明**(在`in`关键字之前)，在这里变量被声明并可能被初始化。变量声明由变量的名称、冒号`:`和变量的类型`message: String`组成。要在声明时初始化变量，需要添加 equal `=`和变量的值`message: String = "Hello, Blink!"`。
*   可以访问变量的**主体**(在`in`关键字之后)。用`let`声明的变量只能在关联体中访问。

**省略变量的类型。** Blink 支持[类型推断](https://en.wikipedia.org/wiki/Type_inference)，这意味着如果变量在声明时被初始化，那么它的类型可以被省略，Blink 将根据它的值推断出变量的正确类型。前面的例子可以写成这样:

```
**let** message = "Hello, World!" **in** {
    Console.println(message)
}
```

**一次声明多个变量。**声明多个变量，在`let` *表达式*的声明部分用逗号`,`隔开。

```
**let** a = 42**,** b = 12**,** c = 24 **in** {
    Console.println(a + b + c)
}
```

每个变量都可以访问在其初始化表达式中在它之前声明的所有变量。所以像下面这样的事情是完全可能的。

```
**let** a = 42, b = **a * 2**, c = **b * 3** **in** {
    Console.println(a)
    Console.println(b)
    Console.println(c)
}
```

上面的内容会打印出来

```
42
84
252
```

**省略花括号。**关于`let` *表达式*的最后一个注意事项是，如果主体仅由一个*表达式*组成，则可以省略花括号。第一个示例可以更简洁地编写如下。

```
**let** message = "Hello, Blink!" **in** Console.println(message)
```

## 情况

像在大多数编程语言中一样，条件用一个`if-else` *表达式*来表达。

```
**if** (<condition>) {
    <do something>
} **else** {
    <do something>
}
```

条件必须评估为类型`Bool`的值。如果需要，可以省略`else`块，如果块仅由一个*表达式*组成，则`if`或`else`块可以省略花括号。

```
**if** (answer == 42) {
    Console.println("That's the answer of life!")
} **else** {
    Console.println("That's not the answer of life.")
}
```

## 环

一个`while` *表达式*用于在条件成立时执行一个或多个*表达式*。

```
**while** (<condition>) {
    <do something>
}
```

同样，如果`while`的主体仅由一个*表达式组成，可以省略花括号。*

## 定义函数

使用`func`关键字定义函数。

```
**func** sum(a: **Int**, b: **Int**)**: Int** **=** {
    a + b
}
```

**Blink**中的功能参数**用逗号`,`隔开，并用括号`(a: Int, b: Int)`括起来。每个参数都是由其名称后跟冒号`:`和类型`a: Int`定义的。**

****返回类型。**返回类型是通过在参数列表后添加一个冒号`:`，后跟函数返回值的类型来定义的。如果函数不返回值，返回类型必须是`Unit`。**

```
**func** greet()**: Unit** = {
    Console.println("Hello, Blink!")
}
```

**但是作为一个语法糖，如果一个函数没有声明任何返回类型，Blink 会自动假设返回类型是`Unit`。所以前面的例子可以写成如下形式。**

```
**func** greet() = {
    Console.println("Hello, Blink!")
}
```

****功能体。在返回类型之后，是等号运算符，然后是用花括号括起来的函数体。Blink 中没有**无返回关键字**，函数**体中最后一个*表达式*的值就是函数返回的**值。****

**如果主体仅由一个*表达式*组成，则可以省略花括号。我们的第一个例子可以改写成这样:**

```
**func** sum(a: Int, b: Int) **=** a + b
```

## **定义类别**

**使用`class`关键字定义类。**

```
**class** Person {
}
```

**这定义了一个简单(尽管没用)的类`Person`。现在可以使用关键字`new`创建类`Person`的对象。**

```
**let** p = *new* **Person()** **in** { }
```

****属性。**一个类可以有一个或多个用`var`关键字声明的属性。**

```
**class** Person {
    **var** firstname: **String** = "Klaus"
    **var** lastname: **String**
    **var** age: **Int**
}
```

**属性可以在声明时进行初始化。如果一个属性被初始化，初始化*表达式*将在创建对象时被评估。**

****Blink 中的属性是私有的**。他们不能被公开。可以创建 Getters 和 setters(只是函数)来访问类外部的属性。**

****功能。**一个类可以有函数(或者`methods`，如果你喜欢的话)。**

```
**class** Person {
    **var** firstname: **String** = "Klaus"
    **var** lastname: **String**
    **var** age: **Int** **func** firstname(): **String** = {
        firstname
    } **func** setFirstname(name: **String**) = {
        firstname = name
    }
}
```

**在上面的例子中，属性`firstname`有一个 getter 和一个 setter。编写这些函数的更惯用的方法是省略花括号，因为在每个函数的主体中只有一个*表达式*。**

```
**class** Person {
    **var** firstname: **String** = "Klaus"
    **var** lastname: **String**
    **var** age: **Int** **func** firstname(): **String** = firstname
    **func** setFirstname(name: **String**) = firstname = name
}
```

****默认情况下，Blink** 中的功能是公共的。然而，可以通过在`func`关键字前添加`private`修饰符来使函数私有。**

```
**private func** age(): Int = // ...
```

****建造师。Blink 中的一个类有且只有一个构造函数。默认情况下，Blink 中的类有一个不带参数的默认构造函数。显式自定义构造函数可以通过在类名中添加一组括在括号中的参数来定义。****

```
**class** Person**(**firstname**: String,** lastname**: String)** {
    **func** firstname(): **String** = firstname
    **func** setFirstname(name: **String**) = firstname = name **func** lastname(): **String** = lastname
    **func** setLastname(name: **String**) = lastname = name
}
```

**构造函数参数自动充当类的属性，可以从类中所有函数的主体或任何附加属性的初始化表达式*中访问。***

```
**let** person = *new* **Person("**Klaus"**,** "Baudelaire"**)** **in** {
    Console.println(person**.firstname()**)
    Console.println(person**.lastname()**)
}
```

**上面的内容会打印出来**

```
Klaus
Baudelaire
```

****传承。**Blink 中的继承用`extends`关键字表示。**

```
**class** Employee **extends** Person {
}
```

**如果被继承的类(超类)有一个显式的构造函数，继承类必须通过传递创建超类的对象所必需的参数来遵循超类的构造函数。**

```
**class** Employee**(**firstname**: String,** lastname**: String,** company**: String)** **extends** Person**(firstname, lastname)** {
}
```

****超驰功能。**`override`修饰符用于覆盖超类中的一个函数。**

```
**class** Person(firstname: **String**, lastname: **String**) {
    **override** **func** toString(): **String** = firstname + " " + lastname
}
```

**在上面的例子中，我们覆盖了`Object`类中可用的`toString`。Blink 中的所有类都继承自`Object`。**

## **定义运算符**

**就像可以使用算术运算符`+`、`-`、`*`或`/`来执行两个或多个`Int`之间的运算一样，Blink 允许我们为自己的类定义二元运算符`+`、`-`、`*`、`/`、`%`和一元运算符`—`和`!`的行为。**

****二元运算符**通过添加一个函数来定义，该函数的名称是要定义的运算符的符号。例如，让我们为类`Rational`定义一个`+`操作符(代表像`3/4`这样的有理数)。**

```
**class** Rational(num: **Int**, den: **Int**) {
   **func** +(other: **Rational**): **Rational** = // ...
}
```

**给定类型为`Rational`的两个变量`a`和`b`，我们现在可以写`let c: Rational = a + b`。Blink 自动将表达式`a + b`转换为函数调用`a.+(b)`。**

****一元运算符**是通过添加一个函数来定义的，该函数的名称以`unary_`开头，后面跟有被定义运算符的符号。**

```
**class** Rational(num: **Int**, den: **Int**) {
    **func** unary_-(): **Rational** = *new* **Rational**(-num, -den)
}
```

**在上面的例子中，我们在类`Rational`上定义了一元`—`操作符的行为。给定一个`Rational`类型的变量`a`，我们可以写`let b: Rational = -a`。Blink 将*表达式* `-a`转换为函数调用`a.unary_-()`。**

****Blink 中也支持运算符重载**，这意味着可以定义多个`+`运算符，例如，只要每个运算符函数的参数表不同。**

**例如，对于我们的`Rational`类，我们可能想要将 2 个`Rational`加在一起，但是也想要添加一个`Rational`和一个`Int`。运算符重载允许我们这样做。**

```
**class** Rational(num: **Int**, den: **Int**) {
   **func** +(other: **Rational**): **Rational** = // ...
   **func** +(integer: **Int**): **Rational** = // ...
   **func** +(double: **Double**): **Rational** = // ...
}
```

**有了上面的例子，给定两个`Rational`类型的变量`a`和`b`，我们可以写出*表达式*，像`a + b`、`a + 42`或`b + 3.14`。**

## **一切都是物体**

**Blink 里的一切都是对象。甚至像`Int` s 或`Double` s 这样的原语实际上也是对象。而像`32 + 21`这样的算术表达式，其实就是函数调用`32.+(21)`。这是一个很好的特性，它使我们能够在 Blink 中为自己的类型定义操作符。**

## **Blink 中没有语句，只有表达式(和定义)**

**总结我们的 Blink 之旅，值得注意的是，与 Java 或 JavaScript 等其他编程语言不同，Blink 中没有语句，只有*表达式*(以及函数或属性定义等定义)*。*一个*表达式*和一个语句的区别在于，一个*表达式*总是计算出一个*值*，而一个语句只是执行一些动作。**

**例如，在 JavaScript 中，如果条件为`true`或`false`，则`if`语句会执行特定的代码块。在 Blink 中，`if`是一个表达式，其计算结果为一个值。**

**这意味着，在眨眼之间，它是完全有效的写作**

```
**let** isLegallyAdult = **if** (age > 18) { **true** } else { **false** }
```

**如果是`age > 18`，则`isLegallyAdult`将等于`true`，否则将等于`false`。**

**我们对 Blink 的快速浏览到此结束。正如你所看到的，Blink 拥有表达几乎任何程序的必要概念，足够简单，可以在相对较短的时间内为它编写解释器，并且有一些有趣的特性，比如它对对象和表达式的使用。**

**如果你已经准备好了，请阅读第二篇文章，它将给出编译器和解释器的高级概述。**

**为了更深入地了解 Blink 构造如何协同工作，下面是一些完整的 Blink 程序。**

**A simple Guess The Number game in Blink**

**A Fraction class in Blink**

**A simple linked list in Blink.**

**你已经到达终点了。🎉**

> **黑客中午是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**