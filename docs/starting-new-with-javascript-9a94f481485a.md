# 从 Javascript 开始

> 原文：<https://medium.com/hackernoon/starting-new-with-javascript-9a94f481485a>

## 我的“引领”年轻开发者之旅

> *首先给学生们巨大的道具，这个故事讲的是。他们设法从零网络开发经验到一个实时反应的应用程序。如果这个故事中有任何冒犯的地方，我很抱歉，因为事实上我很自豪你们能和我一起熬过这个地狱。从零开始，同时暴露在一个年轻的创业公司的不确定性中，绝对是一个糟糕的起点。*

## 背景

达姆施塔特技术大学强迫计算机科学的学生在被允许写他们的学士论文之前为一家真实的公司做一个*无偿的*实践项目。由于我目前在一家需要开发人员的小型初创公司工作，两年前我不得不自己做这个实习，我认为带领一个小型开发团队(第五学期的 4 名学生)为我们做一个小项目实际上会很有趣——所以我们申请并被接受。

自从我在大学的第一个学期开始，我基本上一直在从事 web 开发工作(学生工作来为我的生活筹集资金)，甚至在我在学校有一些课程(php，html/css 的基础)之前，所以我想在第五个学期，人们应该至少知道一点关于 web 开发(html/css)的知识。

[http://giphy.com/gifs/foxtv-new-girl-wrong-ylyUQngtu3CjajjGy4](http://giphy.com/gifs/foxtv-new-girl-wrong-ylyUQngtu3CjajjGy4)

第一次和小组见面后，我意识到我错了……*从过去的角度来看，我应该知道——因为*我在同一所大学:)。TU-Darmstadt 以非常理论化而自豪，所以你基本上不用编写一个“ *hello world* ”就可以获得理学学士学位(有几个例外，你必须在小组工作中进行编程——有点麻烦，java 和 bluespec/verilog<20/180 CP——但阅读一本好书会让你通过考试中的纸上练习)。

![](img/77ab67f440c307ae8a753609f8c37cd0.png)

[https://twitter.com/iamdevloper/status/748201714440900610](https://twitter.com/iamdevloper/status/748201714440900610)

## 入门指南

所以现在我们有了一个超级好的、有动力的团队，每个人必须工作 250 小时。**1000h 做什么？**我们的计划是用一个公共和私有的 rest api 获得一个经过验证的实时传感器图表应用程序的运行原型。

我们事先选择 node.js + react 作为堆栈，并将所有其他决定交给团队，因为我认为如果他们可以选择他们感兴趣的东西，而不需要像全局状态管理这样的"**复杂的**"东西，那就太好了。他们决定采用 create-react-app+firebase——我过去认为，现在仍然认为这是一个不错的选择。基本上，它提供了我们需要的任何东西(通过订阅/托管/节点管理库的 auth/soft-realtime 来构建定制的 rest api)。

## 令人悲伤的事实

> 所有让我喜欢上 Javascript 的事情，当你刚刚开始的时候，实际上是相当令人困惑的。

**选择**:多种多样的选择让人应接不暇，如果你以前从未使用过它，最终会得到一个笨拙的弗兰肯斯坦代码，其中包含你认为在 github 上有足够多*明星的库。*

***Javascript 进化** : Javascript/node.js 正在以令人难以置信的速度进化。这绝对是一件好事，但也有不好的一面:您可以找到一年前的代码示例，其中使用了过时的内容。在反应和混合等方面尤其如此。*

***浏览器疯狂**:想象一下，你是网络开发新手，突然你不得不学习 DOM/vdom html CSS js……然后当你终于有足够的知识来构建对你有用的东西时——它肯定对你这个 mac❤-safari 潮人编码伙伴没用，你不得不开始学习浏览器支持和 polyfills。*

## *我学到的教训*

*领导一个初级开发团队比我最初设想的要复杂得多。我喜欢尝试新东西和阅读文档，所以我害怕强迫他们使用特定的库会降低他们的乐趣。我想我可以用票和需求轰炸他们，审查代码，并在事情可以做得更好时给他们建议…这基本上是对的，但我们面临许多问题，如:*

*   *从一个答案跳到另一个答案，混淆了过去几年所有可能的 Javascript 语法(例如 var/let/const 或 react-classes)…后来通过更严格的 eslint 规则解决了这个问题*
*   *像适当的 git 流这样非常普通的事情似乎是不可能的。多人在做同一件事。在每次提交时合并冲突，但还没有 git 或 svn 的经验。*
*   *在最终切换到 react-router… v1 之前使用不同的笨拙路由器，然后切换到 v4(这很好，但或多或少所有 SO 结果都是 2/3，这导致了他们的笨拙错误)*
*   *他们不知道像 moment.js 这样的流行软件包，所以他们试图为所有东西推出自己的解决方案*
*   *缺乏 html/css 方面的知识浪费了很多学习基础知识的编程时间——后来转到 material-ui，这实际上帮助很大*

## *下一个项目*

*通常我都同意[https://medium . com/JavaScript-scene/why-im-thanking-for-js-fatigue-I-know-you-re-sick-of-that-words-but-this-different-296 FAE 0 c 888 f # . 5l 945 ktfd](https://medium.com/u/c359511de780#.5l945ktfd)—***这是一篇比我的好得多的文章，一定要看！****

> *我的建议:一切都很棒——只要确定你的棒的子集并坚持下去。*

*对于未来的项目，我可能会强迫团队使用 ***我的子集***，让决策变得更容易，而不是被各种各样的选择淹没。*

*如果我创建了一个基础项目，包括 moment.js，react-router@v3，mobx，eslint@airbnb，firebase-nest，mobx-firebase，jest…很多问题可能永远不会发生。*

*此外，我可能会强制一个特定的 git 流，如果团队没有坚持下去，我会向他们唠叨。*

*我注意到的另一件有点烦人的事情是，人们倾向于谷歌搜索结果，即使有很棒的文档/API，最终还是从 SO 或私人博客/论坛得到笨拙/过时的实现。下一次，我向自己承诺，每当注意到这样的事情时，就要问“为什么”,这样学生们也能学会正确地阅读 API 文档，最终成为更好的开发人员。*

*感谢阅读，我希望你喜欢阅读我的经历，并自己做得更好:)我会随时更新你的信息，最终甚至会公布最终结果——如果学生团体允许我这样做的话。*

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*