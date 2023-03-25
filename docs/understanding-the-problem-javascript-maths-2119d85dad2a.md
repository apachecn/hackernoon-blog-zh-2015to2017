# 理解问题:JavaScript 数学

> 原文：<https://medium.com/hackernoon/understanding-the-problem-javascript-maths-2119d85dad2a>

![](img/30ae397c7947396359387d9ea1ec91cb.png)

让我们面对它: [JavaScript](https://hackernoon.com/tagged/javascript) 数学有一些严重的问题。

假设你的应用程序负责跟踪用户和供应商之间的交易。其中一个供应商是一家花卉分销公司，他们向美国各地的商店发送大量包含数千朵鲜花的产品。让我们计算一下买方应该付多少钱。将以下代码复制并粘贴到您的浏览器控制台中，或者在这里运行:

```
const flower = { price: 0.94 };const calculateFlowersTotal = n => n * flower.price;let bundles = [ calculateFlowersTotal(5), calculateFlowersTotal(20), calculateFlowersTotal(100), calculateFlowersTotal(500)];let order = [7, 3, 0, 5];let orderTotal = order.reduce( 
  (acc, quantity, i) => 
    acc += quantity * bundles[i], 0);console.log(‘Order subtotal: ‘, orderTotal + ‘\n’);let orderTotalWithTax = orderTotal * 1.0725; //should return 2616.14925console.log(‘Should return true: ‘, orderTotalWithTax === 2616.14925);console.log(‘\nWhat gives? Let's have a look..\n’);console.log(‘calculated total with tax: ‘, orderTotalWithTax);
```

如你所见，总数不是我们所期望的。小计看起来已经算准了，但是加了税出现了意想不到的结果。你能发现上述操作中允许安全计算的趋势吗，以及这种趋势是如何被打破的？尽管整数和小数都被归类为数字，并且从技术上来说都是浮点数，但它们的行为稍有不同，这可能会导致不准确的计算。特别是当涉及不准确表示的十进制值[时，当用任何乘法、除法或指数运算评估两个浮点数时。](https://hackernoon.com/tagged/decimal)

> “在 JavaScript 中，所有数字都是 IEEE 754 浮点数。由于其编码的二进制性质，一些十进制数无法精确表示。”乔希·克兰顿

位值 0.1 就是这种情况之一，但是解释器安全地假设用户不想通过删除尾部来看到不准确的地方。让我们来看一些例子:

```
0.1*0.1; //evaluates to 0.010000000000000002(0.1*0.1)/0.1 //evaluates to 0.10000000000000002
```

似乎在乘法和除法中，我们消除了以前认为尾部值不应显示的删除假设。在计算中使用全值，包括表示它们所必需的额外值，导致解释器认为全值更可能是真实值，并且移除尾随值将降低计算能力，从而无法以任何准确度进行计算。

[这里](http://adripofjavascript.com/blog/drips/avoiding-problems-with-decimal-math-in-javascript.html)是一些关于如何准确计算交易的建议。它们包括将小数转换成整数，执行计算，然后再转换回来。然而，当引入一个意外的浮动时，这些方法仍然会失效，正如在花卉销售税的例子中所看到的。

目前，解决这些不准确性的唯一万无一失的方法是使用提供精度函数的库。三个主要实现的讨论，big.js，bignumber.js 和 decimal.js，可以在这里找到。

我们只能希望有一天，ECMA 团队将采用这些作者提供的解决方案，并将其实现到该语言的本地操作符中。

雅各