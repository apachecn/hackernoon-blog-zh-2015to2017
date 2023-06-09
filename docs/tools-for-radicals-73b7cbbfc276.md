# 激进分子的工具

> 原文：<https://medium.com/hackernoon/tools-for-radicals-73b7cbbfc276>

![](img/70f57abc494d564a2da03d0c9b8fc6e0.png)

一个幽灵正在困扰着 web 开发——JavaScript 疲劳的幽灵。旧互联网的所有力量都加入了一个神圣的联盟来驱逐这个幽灵:程序员和设计师，浏览器供应商和框架作者，思想领袖和狗屎海报。

但是，尽管自 2015 年末创造出 [JavaScript 疲劳](/@ericclemmons/javascript-fatigue-48d4011b6fc4#.6ibxgcrnr)以来，情况已经逐渐改善，但[创建-反应-应用](https://github.com/facebookincubator/create-react-app)使得开始使用[反应沙拉](/@modernserf/node-js-is-a-salad-bar-74ec01bd4390#.2n44h3wmx)变得容易得多——这只是治标不治本。

像 Peter-Paul Koch 这样的守旧派 web 开发人员对我们为自己建造的摇摇欲坠的复杂之塔不屑一顾。他们渴望更简单的时代，那时 web 开发人员直接在 html、css 和 javascript 的原始材料中工作。他们将现代对工具的依赖视为对用户有害的“盲目崇拜”；如果你离不开工具，你就不是一个网站开发者。

## 你永远不想听到程序员说的九个字

请允许我暂时唱反调。

忽略 PPK 煽动性的评论，承认潜在的事实:我们的工具真的*复杂，我们的网络应用真的*庞大，我们真的*脱离了我们媒体的原材料。Web 开发比以前更难，可能比它需要的更难。我们被复杂性包围，但我们无法区分本质的和偶然的 T21。***

典型的(现代的/过于复杂的) [JavaScript](https://hackernoon.com/tagged/javascript) 应用是一个“单页应用”,构建有一个框架(例如 Angular/Ember/React ),在运行时呈现整个应用。将我们从服务器呈现的静态页面带到今天的每一个选择都是有意义的:

*   脚本允许网页不需要刷新就可以交互。
*   AJAX 同样让页面更具交互性——页面上的数据现在无需完全刷新就可以改变。
*   当页面上的大量数据发生变化时，拥有客户端模板系统会很有帮助，而不是手动添加 DOM 节点。
*   如果我们在客户端构建页面，那么在服务器端做同样的事情是多余的。
*   如果服务器向客户端发送的原始数据与向移动应用程序发送的相同，那么客户端应该像在浏览器中运行的应用程序一样构建？
*   我们比以前写了更多的 javascript 我们需要工具来帮助我们将所有这些文件捆绑在一起并缩小它们。
*   由于我们已经不得不使用构建工具，我们应该扩展它们来解决 JS 应用程序开发中的一些其他问题。

不可否认，现代 web 应用程序比呈现静态 html 的服务器要复杂得多——只不过有更多的移动部件——但它能做的远不止静态 html。每一个单独的步骤都增加了配置的复杂性，但也使新的工作成为可能。每一步都是*投资*——随着时间的推移，前期配置成本会得到回报。

每一步在上下文中都有意义。如果一个人一直跟随技术的发展，并且随着新工具的出现而升级他们的工具，那么个人的改变是可以管理的。但最终结果是一个外人无法理解的系统:一个对初学者和跟不上变化的人来说完全不可理解的技术官僚机构。

我们经常发现自己在解决自己创造的问题，这使情况变得更加复杂；我们使用工具[来减轻由之前的工具集](https://www.youtube.com/watch?v=P9yruQM1ggc)引起的问题。这是我们在许多 webpack 的横幅功能中看到的:

*   热加载解决了应用程序丢失状态的问题&重新加载需要很长时间。
*   增量构建解决了从头构建需要整整一分钟的问题。
*   加载器解决了框架模块化 HTML 和 JavaScript 的问题，但没有解决 CSS 或资产的问题。
*   代码分割解决了将单页应用程序编译成一个大文件的问题。

反过来，这些功能也增加了自身的问题:

*   热加载和增量构建要求您为开发和生产设置单独的构建和服务器。
*   加载器和代码分割与常规节点模块解析不兼容，并破坏了与标准工具的兼容性，需要变通方法或 webpack 感知工具。

# 极端简单

我们不能通过增加内容来降低这个系统的复杂性。我们不能修补我们的出路；我们需要突破性的改变。但是我们的工具不仅仅因为增量设计和意想不到的后果而变得复杂。我们正在解决的问题仍然存在固有的复杂性。即使我们从零开始，知道我们现在所知道的，我们仍然有相同的平台多样性来支持，相同的令人沮丧的边缘情况，相同的对 web 应用的期望。如果[从头重写](https://www.joelonsoftware.com/2000/04/06/things-you-should-never-do-part-i/)对单个软件项目不起作用，我们为什么期望它对整个生态系统起作用？

我们需要的比“重来”更激进——我们需要**解决不同的问题。**

> 一个优秀的 Forth 程序员会比我做得更好吗？是的，但不仅仅是在以不同方式编写代码的层面上。更确切地说，是在以不同的方式做每件事的层面上。还记得自由的名言吗？第四是改变语言、编译器、操作系统甚至硬件设计的自由。
> 
> *…以及改变*问题*的自由。*
> 
> 我正在做的那些计算？在第四版中，他们不会只是写得不同。他们根本不会实现它们。事实上，我们终究也没有实现它们。进入生产代码的算法是非常不同的——在我们的例子中，更加复杂。在第四种情况下，它们会变得不那么复杂。少多了。

[*约西·克雷宁，《我的历史与第四部&栈机》*](http://yosefk.com/blog/my-history-with-forth-stack-machines.html)

现在，在我们的行业中，我们对平台或硬件没有多少控制权。但有时变化会改变我们的期望。大约十年前，第一代 iPhone 问世，它给了软件设计师一个新的工作范式，不受期望的阻碍。移动网站需要比桌面版本更简单，但在此之前，移动网络甚至不值得考虑。小屏幕的限制，在弱计算机上，通过缓慢的蜂窝网络获取数据，迫使我们开发关于如何为网络设计的新想法。

这些新想法中有许多是对旧方法的改进。响应式设计是一种更好的思考网页布局的方式。为触摸而设计迫使我们放弃[神秘肉导航](https://en.wikipedia.org/wiki/Mystery_meat_navigation)。使用更少的数据也有助于在未计量的有线连接上获得更好的体验。

## 激进派和反动派

尽管 JavaScript 社区中不乏抱怨工具复杂性的人，但很少有人意识到它们是如何变得复杂的，以及如何使它们变得更简单。PPK 认为现有的系统复杂性是不可持续的，这是正确的，但他将相关性误认为因果关系:工具是复杂问题的症状，而不是原因。他建议 web 开发人员需要能够“不使用工具”,这忽略了最初创建工具的理由。

相比之下，有人喜欢[子栈](https://substack.neocities.org/)。他对现代 JS 开发的复杂性提出了严厉的批评——经常到我认为不合理的程度。但是无论怎么想象，他都不是反工具的。如果有什么不同的话，他可能比任何其他人都要对[更多的 JS 工装](https://www.npmjs.com/~substack)负责。但是他的工具充满了“做好一件事”的 unix 哲学——它们并不总是容易使用，但是它们始终是简单和可组合的。

这在他的[构建 webapps 的起点](https://www.reddit.com/r/node/comments/5t2hc8/stepbystep_tutorial_to_build_a_modern_javascript/ddkk1v7/)中有很好的说明。它以现代 JS 开发的*基本*组件——节点模块、客户端渲染——为特色，同时移除了几乎所有其他组件。它比 create-react-app 做得少，但它处理最重要的部分，并解释如何在程序需要时逐渐增加复杂性。

当我们把 Substack 和 PPK 归为“网络极简主义者”时，我们就把激进的*和反动的*混为一谈了。反动的方法，尽管表面上与激进的方法相似，却有着相反的目标:反动者不是创造一个新系统，而是回到旧系统；它并没有让新一代的开发人员可以使用工具，而仅仅是恢复了旧的看门人的身份。这就是 PPK 的问题所在——他的愿景从根本上来说是怀旧的，而不是进步的。他不想把 web 开发做得更好；他想让 Web 开发再次变得伟大。**

## 提高激进的声音

我既不激进也不反动。如果我们要延伸政治隐喻，我会是那种人们在推特上抱怨的自由派。我正在努力改进这个系统，但只是循序渐进；我太安于现状，不能推翻它。

虽然我缺乏改变世界的远见和意愿，但我知道风向。*某种东西*即将到来，颠覆整个系统，就像十年前的移动网络一样。我们现有的工具将会太复杂、太保守而无法适应，重新开始不仅是可能的，而且是必要的。

与此同时，我正努力消除我所从事项目的复杂性。我认识到这对于导致 JavaScript 疲劳的基本问题毫无帮助，但是我至少可以让和我一起工作的人觉得事情更简单。(素食主义者明白在资本主义制度下没有道德消费这回事，但她不会一边吃鹅肝一边等待全自动奢侈共产主义的到来。)

例如:我在我的大多数项目中使用 React，无论是个人还是工作，我通常是团队中第一个尝试新 webpack 配置的人。但是我在 create-react-app 的参数范围内找到了越来越多的工作方式，特别关注避免我之前提到的一些自我造成的问题:

*   我在 URL 查询和 [localStorage](https://developer.mozilla.org/en-US/docs/Web/API/Window/localStorage) 中保持非瞬态状态，并且我将每个应用保持得足够小，使其在一秒钟之内加载完毕。这缓解了 create-react-app 中热加载的不足。
*   我对 webpack 的 css 加载器使用了几种替代方法，包括[原子 CSS 框架](http://solid.buzzfeed.com/)和 [CSS-in-JS](https://github.com/styled-components/styled-components) 。
*   每个页面都是它自己的应用程序，而不是构建一个大的应用程序，并使用代码分割来分割它；路由是在服务器端完成的。因为它们在同一个域中，所以它们可以访问相同的持久存储和 cookies 这足以使他们团结一致地一起工作。

在为神秘但确定的未来做准备时，我试图倾听更激进的声音，探索受约束但却非常简单的工具:

*   [next.js](https://zeit.co/blog/next) 旨在让现代 js 应用开发像 PHP 模板一样简单。
*   Elm 是一种人性化的函数式语言，拥有友好的文档、良好的开发工具和零配置编译器。
*   Gomix 在 Google Docs 风格的界面中协调了 web 开发的视图源时代和现代应用程序。

# 结论

事情是这样的:我知道大多数——甚至所有这些——都是死胡同。[激进](https://en.wikipedia.org/wiki/Oneida_Community) [乌托邦](https://archive.org/details/gardencitiestom00howagoog) [愿景](http://www.newyorker.com/magazine/2008/06/09/dymaxion-man)几乎都是错的！但是，即使是一个不成熟的想法也会扩展我们对可能性的认识；发表激进的观点[扩大了讨论的范围](https://en.wikipedia.org/wiki/Overton_window)。我们可能无法在生产中使用概念验证，但我们可以用它们进行试验，看看在哪里可以进一步开发。但是，在我们探索这些选项的同时，我们还可以做一些事情:

首先，我们需要问:*我们应该编写软件来解决这个问题吗？*这个应用程序可以被电子表格取代吗？

接下来，我们需要降低现有项目的复杂性。渐进主义有其局限性，但我们至少可以减轻痛苦。对于过去四年没有关注博客帖子的人来说，我们如何才能让我们的系统更容易接近？

最后，我们需要提升激进的声音。反动派比激进分子有一个主要优势——他们只有一个想法，所以他们听起来都很一致，而每个激进分子都有自己独特的、不相容的愿景。进一步放大反动声音对我们没有任何好处，放大激进声音也不会有任何损失。我们不能全部跟上，但我们至少应该倾听。

彻底的简单需要牺牲。为了摆脱工具的重压，我们需要接受更严格的约束、更宽松的规范和更窄的范围。我们需要对我们的软件应该是什么样子，以及我们的工具是为谁设计的，设定新的期望。但是最难做出的牺牲是在我们自己身上——我们需要让我们的知识过时。这就是反动派和激进分子的最终区别——我们是否准备放弃我们的专业知识和地位，盲目地摸索未来？

*感谢* [*萨拉格罗夫-帕勒莫*](https://medium.com/u/15d15304c9b1?source=post_page-----73b7cbbfc276--------------------------------)*[*马修麦克维卡尔*](https://medium.com/u/cb10681bf0f7?source=post_page-----73b7cbbfc276--------------------------------) *，以及罗宾布兰科进行点评。**

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)的机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*