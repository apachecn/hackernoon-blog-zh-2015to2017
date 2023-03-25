# 介绍船长日志(☠)️

> 原文：<https://medium.com/hackernoon/introducing-captains-log-%EF%B8%8F-7ab94a9ea1fd>

> 嗨，我是凯尔。我是一个长期的控制台日志调试用户。
> 
> 嗨，凯尔。

你的控制台语句是否缺乏？你是否一直在添加更多的细节或者忘记了它们？你的控制台看起来比堆栈跟踪更糟糕吗？我一直发现自己迷失在数据超载中。

![](img/9fc55b583da596466f38647512c243bb.png)

Too much of a good thing can be bad

> [船长日志](https://github.com/kwelch/babel-plugin-captains-log)是一个 babel 插件，旨在改善黯淡的控制台语句，改善开发者在调试时的体验。

# 灵感

我对控制台语句的使用达到了这样一种程度，即当我创建位置数据和变量名时，我直觉地开始预先考虑它们。不幸的是，名字变了，语句被复制了。我不能依靠自己的能力来准确标记这些语句。

我不记得我第一次听说巴别塔插件的确切场景，但我打算*【指责】* [肯特·c·多兹](https://medium.com/u/db72389e89d8?source=post_page-----7ab94a9ea1fd--------------------------------)。我很快就被它们的力量震惊了，但不知道如何运用它们。我开始摆弄[astexplorer.net](http://astexplorer.net/)，一遍又一遍地阅读[巴别塔手册](https://github.com/thejameskyle/babel-handbook)，越来越得心应手。

在我可怕的通勤途中的一个早晨，我突然想到我可以使用插件来减轻控制台调试的痛苦。

> 瞬间就找到了一个项目和我的动力！

# 我学到了什么？

> 制作一个插件比打破多年的控制台声明习惯要容易得多。

首先，我学会了如何制作一个插件，这太棒了！我现在觉得我在更深的层次上了解了 JavaScript 只是在更深的层次上没有任何改善。

我非常惊讶，这感觉非常像在 DOM 树中导航和修改元素。

这是一次如此有趣和神奇的经历，教会了我很多东西。然而，老实说，我不认为我自己会把另一个巴别塔插件作为一个开源项目。

> 我打算维持这个，别担心。

我看到了我所学的真正力量被更好地用作[代码模块](https://github.com/square/babel-codemod)。它们是单独使用的，但在我看来更适用于团队环境，而且我通常不会有还没有被创造出来的酷想法。

如果你还没有尝试过 babel-plugins，我建议你尝试一些简单的东西。你会惊讶于你学到了多少，以及它是如何改变你看你写代码的方式的。

> 想了解更多，我强烈推荐肯特前端硕士课程:[https://frontendmasters.com/courses/linting-asts/](https://frontendmasters.com/courses/linting-asts/)。挑战 6 是我最喜欢的😎！

# 入门指南

```
npm install --save-dev babel-plugin-captains-log// .babelrc
{
  "plugins": ["captains-log"]
}
```

# 它是做什么的？

目前，[船长日志](https://github.com/kwelch/babel-plugin-captains-log)有两大特色:

> 将有用的数据注入控制台语句。

**例如**

```
function add(a = 1, b = 2) {
  // outputs: 1
  console.log(a); 
  return a + b;
}↓ ↓ ↓ ↓ ↓ ↓function add(a = 1, b = 2) {
  // outputs: "simple.js(2:2)" "a" 1
  console.log("simple.js(2:2)", "a", a); 
  return a + b;
}
```

您的控制台语句不再只有变量值。船长的[日志](https://hackernoon.com/tagged/log)会追加*文件位置数据* 和*变量名标签*，帮助简化调试过程。让我们再多讨论一下这些。

## 文件位置数据

这就像您想象的那样:它根据控制台语句的位置预先考虑文件名、行号和列号。

## 变量名标签

这个更有意思一点。作为一个狂热的控制台用户，我不断遇到这样一个问题:记录变量只记录值。当在循环内部跨多个文件记录多个变量时，这很难跟踪。*变量名标签*会在编译时自动将变量名注入控制台语句。

## **更新:**

这包括对象表达式和方法。这意味着包含表达式的语句将用调用表达式进行标记。

**示例:** *(文件位置数据已被移除，以关注变量标签。*

```
console.log(a);
console.log(obj.prop);
console.log(obj.method());
console.log(obj.nested.prop);
console.log(method());↓ ↓ ↓ ↓ ↓ ↓console.log("a", a);
console.log("obj.prop", obj.prop);
console.log("obj.method()", obj.method());
console.log("obj.nested.prop", obj.nested.prop);
console.log("method()", method());
```

# 享受

虽然这是为我和我的疯狂习惯写的，但我想我不能独自沉迷于游戏机。我希望这能帮助其他人。无论如何，我知道它让我成为了一个更高效的开发者。

✌

> T️hanks 对瑞秋和马特的评论！

Kyle Welch 是一名高级开发人员，在各种项目的构建、领导和咨询方面拥有 10 多年的经验。他的背景包括金融、产品、营销和政府行业的工作。目前，Kyle 在全国独立企业联合会领导前端团队，在那里他用 ReactJS 创建 web 应用程序。工作之余，他帮助组织一次当地的聚会。在 twitter 上找到他， [@kylewelch](https://twitter.com/kylewelch) ，他在那里谈论 JavaScript、ReactJS 和其他编程冒险。