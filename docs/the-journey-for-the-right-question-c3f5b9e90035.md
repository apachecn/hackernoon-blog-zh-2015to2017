# 寻找正确问题的旅程🔊

> 原文：<https://medium.com/hackernoon/the-journey-for-the-right-question-c3f5b9e90035>

## 一步一个脚印的重要性(在正确的方向上)

![](img/d41aacd565b38795dcbcbfd6b87f1377.png)

The picture of "Deep Thought", a huge supercomputer from “The Hitchhiker’s Guide to the Galaxy” fiction series, on an altar. The computer looks like a huge rock in a humanoid format, built from the belly up, using the hands to hold its big square head.

Listen to the audio version!

不同领域中诞生的技术和想法最终都可以进入编程领域。

例如，[精益软件开发](https://en.wikipedia.org/wiki/Lean_software_development)来源于[精益制造](https://en.wikipedia.org/wiki/Lean_manufacturing)，据说是来源于[丰田生产系统](https://en.wikipedia.org/wiki/Toyota_Production_System)。这是整个运动的基础，即在构建软件时只创建最少量的功能，以避免浪费和不必要的复杂性。

编程让你思考。让你对如何创建一个工作产品进行逻辑推理，让你建立同理心，以便编写一个其他程序员可以理解的代码。做小东西很容易，但做大东西很难。

向自己和他人提出正确的问题，这是一项基本技能，有了它，你才能打造出大而无缝的东西。这些问题可以以“为什么”的形式出现，以便阐明需求并防止[过度设计](/@fagnerbrack/how-to-accept-over-engineering-for-what-it-really-is-6fca9a919263)，或者它们可以帮助调查问题的[根本原因](https://hackernoon.com/how-to-find-the-best-solution-for-a-bug-33818838502)。最后，拥有正确的问题可能比知道正确的答案更有价值。

> 拥有正确的问题比知道正确的答案更有价值，因为正确的答案可能是错误问题的答案。那是没用的。

没有通用的方法来提出正确的问题，因为每个环境都是独特的。这是一种[隐性知识](https://en.wikipedia.org/wiki/Tacit_knowledge)，这意味着它不容易被教授(就像在书中一样)。

可以说“正确的问题”[可能是主观的](/@fagnerbrack/programming-subjective-discussion-9c177c31807f)，两个人可以对什么是正确的问题给出不同的定义[。甚至有可能两个技术高超的人会提出两个不同的、值得探讨的正确问题。](/@fagnerbrack/wittgenstein-s-beetle-in-software-engineering-dcea89a5db92)

我相信下面的例子可以说明这个原则的基本原理。这是一个假设的(但很常见的)场景，团队想要找到使项目更好的方法:

*   **问题:**我们能做些什么让这个 web 应用更快？
*   **回答:**如果我们从用户在其浏览器中下载的 gzipped 代码中删除几个字节，这将减少**所有用户请求的网络流量**，这将减少我们服务器的带宽成本。
*   **问题:**删除这几个字节会在一年内为公司节省多少钱？
*   **回答:**我们用户所有的请求**从 gzipped 代码中去掉 1 个字节的成本**，按照我们月均流量的数据，一年下来大概是 10 块钱。去掉几个字节，我们一年就能节省几十美元。
*   **问题:**减少这几个千兆字节所需的努力成本值得一年节省几十美元吗？

最后一个问题引起了一些有趣的事情。如果公司的目标是赚钱，而工程师为减少 gzipped 字节数所付出的代价大于为所有用户请求提供几十个字节所付出的代价，那么优化也是一件浪费的事情。

当然，可能会出现其他正确的问题，这些问题可能会改变决定，或者至少会引发一些有趣的讨论:

*   有没有数据显示，一些用户离开主页是因为这些字节给网络连接不良的设备造成了网络延迟？
*   我们有多少用户使用这些类型的设备？
*   还值得投资吗？

这项投资可能是值得的。但还是那句话，只要成本大于收益。

有时候，就像在优化示例中一样，提出正确的问题并不需要太多的努力。它需要努力寻找能够回答这些正确问题的数据，并允许进入新的问题。不管这一步的速度或规模如何，拥有正确的问题至少可以为发展指明正确的方向。

> 我们应该始终向前迈进，一步一个脚印。重要的不是速度，而是方向。

在“银河系漫游指南”系列小说中，有一台名为“深度思考”的巨型计算机。建造它的目的是提供“生命、宇宙和一切事物的终极问题的答案”。

算出来的答案是数字“42”。

然而，没有人明白“42”是什么意思。答案之所以毫无意义，是因为指导深度思考的存有们一开始就不知道什么是正确的问题。42 这个数字滑稽地表明，有答案还不足以获得有用的东西。

也许为正确的问题在**的旅程中投入额外的努力是值得的。**

这意味着:

*   在开始做之前，做一个旨在更好地理解需要做的事情的复杂性的尖峰
*   澄清需求，以避免做一些与应该做的不同的事情
*   进行一次调查，以了解业务中哪些最重要的部分可以交付最大的价值(技术上的和非技术上的)

即使结果证明是浪费时间，至少你在开始构建答案之前，已经在问题上浪费了那段时间。

没有正确的问题并直接跳到答案，你还不如浪费**更多的**时间。

我们不要那样做。

感谢阅读。如果你有一些反馈，请在[推特](https://twitter.com/FagnerBrack)、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 上联系我。