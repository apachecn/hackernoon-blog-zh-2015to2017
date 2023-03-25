# 含 Lodash 的函数组合

> 原文：<https://medium.com/hackernoon/function-composition-with-lodash-d30eb50153d1>

![](img/0ddfa42e98623547b528146159217e0c.png)

你最近在看 JavaScript 帖子吗？也许你已经注意到函数式编程现在非常流行。这是一种非常强大的编程方式，但是一开始就让人不知所措。谢天谢地，最受欢迎的 NPM 软件包(这个月有 4800 万次下载)有一个非常有用的函数式编程软件包来帮助我们开始！

在这篇文章中，我们将看看如何使用 Lodash/fp 来组合我们自己的函数，以及你今天可能已经在使用的 Lodash 函数！

在我们开始编码之前——让我们确保我们知道我们在谈论什么！

## 函数式编程:

> 一种编程方法，侧重于使用函数作为一级变量。它避免突变(改变)数据，并试图将应用程序视为一个线性流程，将功能拼凑成一个整体。

## 功能组成:

> 按顺序调用已经列出的函数，将每个函数的结果传递给列表中的下一个函数，然后将最终的函数结果作为整体的结果返回。

通常当你编写函数时，你可以这样做(不知道你正在编写):

```
const myResult = myFunction(myOtherFunction(myData));
```

在这个例子中，你给`myFunction`的结果是`myOtherFunction`的唯一参数。请注意，这些函数将从右向左调用，或者从内向外调用。我们对函数组合做了类似的事情。

```
const getMyResult = compose(
  myFunction,
  myOtherFunction,
);
const myResult = getMyResult(myData);
```

为了让事情更清楚，我想为我们的复合函数定义几个目标。

*   它们将有单一的输入和输出。
*   它们不会有副作用。
*   当链接在一起时，它们可以作为一组数据上的单个“动作”来使用。

## Lodash/fp

为了实现这些目标，我们将使用 Lodash 库的子集 Lodash/fp。函数式编程的“Fp”。当你安装 Lodash 的时候，这个包已经被安装了！唯一的区别是函数变成了不可变的、自动定制的、迭代优先的和数据最后的。

那是什么意思？

*   **不可变**:函数不会改变它们的任何参数。
*   **自动定制**:传入的参数比函数接受的少，只会返回另一个函数。该函数需要其余的参数。
*   迭代优先(iterate-first):通常情况下，你将对数据做什么作为最后一个参数来传递。想想数组函数。你最后传入回调。在 FP 中，你首先传入它！
*   **Data-last** :函数最不期望的就是数据。因为它是可定制的，这允许你定义函数要做什么，把它赋给一个变量，然后在一个组合函数中(或单独)给它数据。
*   **回调/迭代的参数**被封顶(通常只到第一个参数)。这避免了像`parseInt` 这样有可选额外参数的函数的副作用。请注意，这并不意味着函数本身只能有一个参数。

所有这些现在看起来都很混乱。所以让我们来看一个代码示例！

## 问题:

您正在构建一个向用户显示联系信息的网页。企业规定联系人必须按名字排序，过滤掉没有电话号码的联系人，联系人可能被添加了两次，因此只应显示唯一的联系人号码，号码的格式必须像`(xxx)xxx-xxxx`一样。

联系对象看起来像这样:

```
{
  firstName: 'justin',
  lastName: 'fuller',
  phone: '1234568490'
}
```

## 命令式版本:

```
import _ from 'lodash';
const data = [ /* data in here */ ];const sorted = _.sortBy(data, 'firstName');const filtered = _.filter(sorted, 'phone');const unique = _.uniqBy(filtered, 'phone');const formatPhone = c => ({
  ...c,
  phone: `(${c.phone.slice(0, 2)})${c.phone.slice(3, 5)-${c.phone.slice(6)}}`
});const formatted = _.map(unique, formatPhone);console.log(formatted);
```

## 命令式版本 2:

```
import _ from 'lodash';
const data = [ /* data here */ ];const formatPhone = c => ({
  ...c,
  phone: `(${c.phone.slice(0, 2)})${c.phone.slice(3, 5)-${c.phone.slice(6)}}`
});const formatted = _.map(
  _.uniqBy(
    _.filter(
      _.sortBy(data, 'firstName')
      'phone',    
    ), 
    'phone',  
  ),
  formatPhone,
);console.log(formatted);
```

## 功能版本:

```
import fp from ‘lodash/fp’;
const data = [ /* data here **/ ];const formatPhone = c => ({
  ...c,
  phone: `(${c.phone.slice(0, 2)})${c.phone.slice(3, 5)-${c.phone.slice(6)}}`
});const formatData = fp.compose(
  fp.map(formatPhone),
  fp.uniqBy('phone'),
  fp.filter('phone'),
  fp.sortBy('firstName'),
);console.log(formatData(data));
```

那些喜欢函数式编程的人会告诉你，最后一个版本更具声明性。它不是告诉你*这个函数如何工作，而是告诉你*它做了什么*！*

因此，让我们通过代码的每一步来解开它做什么。

*   fp.compose —一个接受任意数量的函数作为参数的函数。然后它从右到左调用它们，就像当你把它们作为参数传递时调用函数一样。
*   Fp。SortBy、uniqBy、filter 和 map 都最后接受数据。因此，首先我们告诉函数排序、过滤和映射的依据，然后它接受数据并返回结果。
*   当我们用数据调用 formatData 时，它获取每个函数的结果，并将其传递给下一个函数。最后一个函数的结果是整个链的结果。

现在我们已经看到了 Lodash 函数式编程的强大表达能力，我想探索更多的问题。您将开始看到这是多么容易，并且希望您将看到这种编程风格带来的安全性！

## 应用组合

合成不仅限于处理数据。一个完整的应用程序可以通过许多更小的功能组合在一起。

让我们做一个心理练习:思考一个应用程序的步骤。

*   检索任何初始配置(环境变量、命令行参数)，然后打包成一个可重用的对象。
*   导入任何第三方依赖项。
*   使用配置和依赖项来引导应用程序。
*   实例化路线，服务，组件，无论它是什么应用程序。

你能看出这个例子或者其他例子是如何串联起来的吗？你看到消除副作用的好处了吗？

在这一点上，你很有希望开始使用 composition 和 Lodash/fp 来解决问题，所以下面我会包含一个 fp 文档的链接。使用它来了解如何在函数/组合风格中使用您最喜欢的 Lodash 方法！

[](https://github.com/lodash/lodash/wiki/FP-Guide) [## 洛达什/洛达什

### lodash——一个现代的 JavaScript 实用程序库，提供模块化、高性能和额外功能。

github.com](https://github.com/lodash/lodash/wiki/FP-Guide) 

嗨，我是贾斯汀·富勒。很高兴你看了我的帖子！我需要让你知道，我在这里写的一切都是我自己的观点，并不代表我的雇主。所有代码样本都是我自己的，与美国银行的代码完全无关。

我也很乐意收到您的来信，请随时通过 [LinkedIn](https://www.linkedin.com/in/justin-fuller-8726b2b1/) 、 [Github](https://github.com/justindfuller) 或 [Medium](/@justindanielfuller) 与我联系。再次感谢阅读！