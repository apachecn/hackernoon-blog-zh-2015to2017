# JavaScript——对本质的一次窥探(第 3 部分)

> 原文：<https://medium.com/hackernoon/javascript-a-sneak-peak-into-the-essentials-part-3-c97a9c2e933f>

> 这篇博客文章是为那些已经知道如何编写软件并想学习 Javascript 的开发人员写的。我不是在教你如何写软件，因为我假设你可能用不同的语言编码。我还假设，即使是 Javascript——忍者也喜欢在他们可能知道或可能不知道的话题的对话中有所下降。

1.  [句法糖](/@michel.herszak/javascript-a-sneak-peak-into-the-essentials-part-1-5a2ef10e63d#.oebnf7aqu)
2.  [Javascript 模式/Javascript 中的对象](/@michel.herszak/javascript-a-sneak-peak-into-the-essentials-part-2-9a4aa7dbd06#.avycxr84z)
3.  **JavaScript 中的函数**
4.  [“这个”指的是这个](/@michel.herszak/javascript-a-sneak-peak-into-the-essentials-part-4-7abef2e984ae#.mo5i8c2nw)
5.  [OOP(面向对象编程)](/@michel.herszak/javascript-oop-essentials-88745eedb477#.38ktw1lsn)

# Javascript 中的函数

函数对于 Javascript 的工作方式至关重要。这篇博客文章不可能涉及 Javascript 函数的每一个方面，但是我会提到使 Javascript 更有用的要素。你会在这篇文章的末尾找到一个链接，它会把你重定向到一个非常全面的函数资源。

它们提供了一组可重用的代码，可以在程序中的任何地方调用。函数可以适用于各种上下文，范围可以被封装和保留。它们的行为不同于你可能知道的其他语言，它们的命名有特定的顺序。Javascript 函数既可以用作函数，也可以用作对象。Javascript 函数可以是:

*   正常函数构造
*   匿名函数
*   或带有指定变量的普通函数

```
// Normal function construct function
**function** myFunction(arguments){
    // some code
};// An anonymous function
myFunction = **function** (arguments) {
    // some code
};// A normal function construct with an assigned variable
myFunction = **function** myFunction(arguments) {
    // some code
};
```

我很确定你至少已经看过其中一部了？如果是这样，你应该了解他们的背景和行为。

# Javascript 上下文

有些应用程序希望您在语句中声明函数。这不是很常见，但当你这样做时，使用下面的例子作为样板。目前所有使用指定匿名函数的浏览器都支持这一点，这是达到预期效果的正确方法:

```
**var** functionName; 

**if**( someCondition ) { 
    functionName = **function** () { ... }; 
}
```

这些函数可以在需要时被调用，其中包含的代码将会运行。调用函数通常有以下方法:

*   myFunction(变量列表)；
*   window.myFunction(变量列表)；
*   object.onEventName = myFunction

我已经数不清我面试过的 JavaScript 开发人员中有多少人不能告诉我函数声明和函数表达式之间的区别，有多少人不知道什么是直接调用的函数表达式。我不是在咆哮，但我确实是:D

# 自调用 Javascript 函数

Javascript 中使用的一种模式是所谓的自调用函数。自调用匿名函数在运行时创建时会立即运行。为了让您知道这是什么样子，下面是包装一些代码的自执行匿名函数的格式:

```
(**function**(){
    // some code…
})();
```

有趣的是，如果你查看 jQuery 的[源代码，你会发现所有的东西都被包裹在中间，以便建立一个新的上下文:](http://code.jquery.com/jquery-1.4.4.js)

```
(**function**( window, undefined ) {
    // jQuery code
})(window);
```

如果您想要重复运行一段代码，或者想要建立一个新的作用域。如果您使用的其他插件和 Javascript 供应商文件也可能使用您的变量，并干扰您的全局名称空间，那么新的作用域就很重要。假设我们在全局范围内有一个变量，只需调用它就可以访问它:

```
// app.js
**var** myVariable = ['this','is','my','message'];

// another.js
**if**(myVariable){ // true
    console.log('your var is global');
}
```

这实际上不被认为是最佳实践。myVariable 将被添加到窗口范围，并在应用程序的所有其他文件中可见。你想为每个文件做的是，你想通过使用自 envoking 函数为你的 var 建立一个作用域。让我们尝试一下:

```
(**function**(){ 
    **var** myVariable = ['this','is','my','message']; 
})(); 

// another.js 
**if**(myVariable){ // false because it it undefined 
    console.log('your var is global'); 
}
```

要解决这个问题，您可以使用一个自执行匿名函数，并为此变量创建一个封装的上下文。所以让我们稍微重构一下这个函数，给大家展示一些有趣的东西。如果你知道 Javascript 在这种情况下做了什么，那才是有趣的。

```
(**function**(){ 
    **var** myVariable = ['this','is','my','message'];
    **for**(**var** i **in** myVariable) { 
        setTimeout(**function** () { 
            // will be exectured delayed by 10 milliseconds 
            console.log(myVariable[i]);
            // output: message message message message 
        },10); 
    } 
})();
```

那么这里发生了什么？它实际上打印消息四次。所以人们可能会想，让我们把 setTimeout 时间降低到 1，每次你打印它，它都会从数组中返回正确的句子，而不是。setTimeout 延迟了执行，因此，当它被调用时，我已经是“message”了，或者是 3，因为我们正在处理一个数组。

为了向你展示如何解决这个问题，我们必须以一种不太直观的方式重构这个函数。

```
(**function**(){
    **var** myVariable = ['this','is','my','message'];

    **var** say = **function** (i) { // the variable i will
        setTimeout(**function** () {
            console.log(myVariable[i]); 
            // output: message message message message
        },10);
    };

    // So by the time i is executed it is no longer referring 
    // to this i in the for loop it is referring to the 
    // scoped i inside the say function
    **for**(**var** i **in** myVariable)
    {
        say(i); // output: this is my message
    }
})();
```

此时，您实际上拥有的是一个作用域中的封装作用域。记住，当你调用这个函数的时候，我会把它交给这个函数，并创建一个新的函数作用域。所以当函数被调用时，I 不再引用 for 循环中的 I。它指的是索引。这在 Javascript 中是一个非常强大的概念，可以创建一个独立的上下文。如果你过去遇到过这个问题，请在评论区告诉我。

# 闭合模式

我们有一个引用了函数外部变量的函数。让我们重构最后一个例子，使它成为一个闭包，并使它在这个上下文中工作。

```
(**function**(){
    **var** myVariable = ['this','is','my','message']; **var** say = **function** (i) { // the variable i will scoped
        **return function** () {
            console.log(myVariable[i]); 
            // Will be called later.
        };
    }; // So by the time i is executed it is no longer referring 
    // to this i in the for loop it is referring to the 
    // scoped i inside the say function
    **for**(**var** i **in** myVariable)
    {
        setTimeout(say(i),1000); // output: this is my message
    }
})();
```

这个概念是闭包函数的常见模式。闭包是一个包装函数，它捕获一个参数，而参数捕获函数内部的上下文。这个函数然后返回另一个函数，这个函数的唯一目的是在以后被调用。它利用在其作用域中初始化的参数。这是强大的，但你要学习它，直到你得到它的权利。这让我们想到了另一种常见的模式，称为模块模式。

# 模块模式

当您想要封装私有函数和变量来访问时，这种模式非常有用。此外，模块模式公开了一个公共 api，您可以使用它来工作和构建您的应用程序。让我们看一个模块模式的好例子:

```
**var** MODULE = (**function** () {
    **var** privateVariable = 1;

    **function** privateMethod() {
        // do something in here
    }

    **return** {
        moduleProperty:1,
        moduleMethod:**function** () {
            */**
             * A function that can utilizes a private method
             * or a private variable.
             */* }
    };
}());
```

注意，我们已经声明了一个名为 module 的全局模块，它有两个公共属性:一个名为 MODULE.moduleMethod 的方法和一个名为 MODULE.moduleProperty 的变量。此外，我们可以使用上面学到的模式轻松导入所需的全局变量。这种模式在构建插件时被大量使用，并且很容易转换成另一种叫做 CommonJS 的模式。

# CommonJS

当我们说一个应用程序是**模块化**时，我们通常指的是它由一组存储在模块中的高度解耦的不同功能组成。你可能知道，[松耦合](http://arguments.callee.info/2009/05/18/javascript-design-patterns--mediator/)通过尽可能移除*依赖*来简化应用程序的可维护性。

CommonJS 模块提案指定了一个简单的 API，用于在服务器端声明模块，与 AMD 不同，它试图覆盖更广泛的关注点，如 io、文件系统、承诺等等。

CommonJS Javascript 在 NodeJS 中被大量使用。因此，如果您正在进行大量的 NodeJS 开发，您会经常看到这种情况。本质上，你所做的是将类接口分配给一个 module.export。为了完成这项工作，你不需要一个自动执行的匿名函数。让我们去掉多余的圆括号:

```
*/**
 * Define more behaviour we would like to expose.
 * And expose foobar to other modules as well.
 */* exports.myFunction = **function** myFunction(){
    **this**.one = **function**(){
        console.log('Hello one');
    }

    **this**.two = **function**(){
        console.log('Hello two');
    }
}

*/**
 * Access the module relative to the path where both usage
 * and module files exist in the same directory.
 ** ***@type*** *{exports.*myFunction*|*}
 */* **var** myFunction = require('./myFunction').myFunction,
    test   = **new** myFunction();*/**
 * Make use of exported variables
 */* test.two(); // output 'Hello two'
```

作为一个**很好的练习建议**，当你在处理函数的时候。不要使用匿名函数。原因是，假设你必须调试精简的代码，并且要处理大量的匿名函数。你很可能会认输，从头开始。太痛苦了！

我相信关于函数这就足够了，因为在其他地方已经讨论了很多。所以看看下面的链接，我将继续第四部分，包括“这个”关键字和范围。

电子邮件—michel.herszak@gmail.com

推特—@ MHerszak(twitter.com/MHerszak)

想知道我的工作方式吗？很好，你可以从我如何着手一个项目的概述开始[这里](http://base2industries.com/)。

关于 Javascript 函数各个方面的综合博文将在[这里](http://ryanmorr.com/understanding-scope-and-context-in-javascript/)找到。确实非常好！

CommonJS 模式的服务器端用法:

*   http://nodejs.org[节点](http://nodejs.org/)
*   独角鲸[https://github.com/tlrobinson/narwhal](https://github.com/tlrobinson/narwhal)

*原载于 2015 年 12 月 21 日*[*www.browserstudios.com*](http://www.browserstudios.com/2015/12/21/javascript%E2%80%8A-%E2%80%8Aa-sneak-peak-into-the-essentials-part-3/)*。*