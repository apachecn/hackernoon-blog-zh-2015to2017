# JavaScript 现象是一种集体精神病

> 原文：<https://medium.com/hackernoon/the-javascript-phenomenon-is-a-mass-psychosis-57adebb09359>

![](img/20c6938add27b040f2881da57f762e2d.png)

from Ken Russell’s “The Devils”

我最近在 LinkedIn 上收到了一条来自加拿大网络犯罪技术公司总裁的友好信息:

> 我读过你的一些 javascript 文章。这就像在一部僵尸电影中，最终找到了一个没有被感染的人。
> 
> 我经营了几年软件生意。它被收购了，现在我正在开发我曾经销售的产品。我以前的员工把 AngularJs 和 Node.js 放在里面。我记得 3 年前我和我最好的工程师的对话:他说 javascript 正在接管一切。我想“哇。他们设法修正了那种可怕的语言”
> 
> 嗯，不。这是更糟糕的，因为至少在此之前，我们用 JS 搞砸了一些小事，它是一个玩具。问题是，有一种关于 JS 的集体精神病，好像每个人都假装它并不可怕。然后，好像这还不够糟糕，有人想出了一个绝妙的主意，把这个东西放在后台。采用 Nodejs 的幼稚公司每年要花费数百万美元。你想知道他们是谁:他们是初创公司和小公司。
> 
> 请注意，这些工程师很聪明，但他们不善于从众思维。
> 
> 所以这让我对 JS 的情况想了很多，唯一合理的解释是:前端一直被工程师鄙视，因为它不太科学，更直观，也因为工具多年来让我们失望。因此，设计师们捡起了球，现在他们想编程，结果是 NodeJS，JS 和对他们的洞的视而不见(坑洞)。设计师不是工程师，反之亦然，我们应该坚持各自的优势。
> 
> 在我的新公司，每个人都假装 JS 很好。我累了，大声说出来。原来，在内心深处他们都讨厌 JS，这只是大众思维。现在都讨厌 JS 了。我们迫不及待地等待网络组装。
> 
> 只是需要有人站出来说话，就像你一样。所以继续做下去。在某个孩子受伤和我们发生悲剧之前。

在这些年来我收到的所有支持信息中，这是我最喜欢的一条。这证实了我一直以来都知道的事情:JavaScript 程序员已经被误导，认为 JavaScript 是一种好的编程语言。总统谈到了“群体精神病”和“群体思维”，但我使用了斯德哥尔摩综合症和邪教心理学的类比。想想帕蒂·赫斯特和山达基。

众所周知，所有的编程语言都有缺点。有些人比其他人拥有更多。但是，JavaScript 尤其糟糕。这就是为什么你能在网上找到这么多关于 JavaScript 的抱怨。关于 JavaScript 最令人惊讶和苦恼的事情之一是，由于语法错误，它实际上可以在运行时无声地失败！另一件事是“回调地狱”*承诺*可以减轻，但不是一个完美的解决方案。JavaScript 最臭名昭著的错误可能是它的弱类型化(不要与动态类型化混淆),这表现在大量的 wat 和 wtf 中，这使得 JavaScript 成为许多行业笑话的笑柄。下面是最有趣的一个(来自一个 JavaScript 支持者！):

我不会仅仅为了说一个简单的谷歌搜索就会发现 JavaScript 的许多内部不一致和陷阱，这实际上使 JavaScript 成为了一个“[数字雷区](http://walkercoderanger.com/blog/2014/02/javascript-minefield)”而重复网页

这种语言非常糟糕，以至于几乎所有 JavaScript 程序员都必须使用 linter(比如 JSLint 或 ESLint)。尽管 ECMAScript 在最近几年经历了很多很多的改进，在 ES6 中达到顶峰。显然，ECMA TC39 委员会无法完全消除 JavaScript 所有最严重的错误。所以问问你自己这个问题:*还有什么现代编程语言如此糟糕，以至于为了安全起见最推荐使用 linter？*

我们不要忽视这样一个事实，没有完美的棉绒。它不能捕获*所有的*，甚至可能产生*误报*。是的，这正是你想从静态代码分析器中得到的东西。

说到 web 开发，JavaScript 是不可或缺的。这是网络浏览器的唯一母语。实际上，它把你当成了人质(毫不奇怪，由于斯德哥尔摩综合症，许多 JavaScript 程序员已经爱上了这种语言)。

但是你有选择的余地！你可以使用将*转换成*JavaScript 的语言。[这里](/javascript-non-grata/the-super-surrogates-of-javascript-862460199915)是一些比较好的，但是[有很多很多语言可以选择](https://github.com/jashkenas/coffeescript/wiki/list-of-languages-that-compile-to-js)。对于前端开发，除非是羊，否则不必选择 JavaScript。

在后端，你不必选择 Node (JavaScript ),因为后端已经有丰富的 Java、Python、C#、Ruby、Erlang、Go 等多种高级语言。[去，特别是](/p/the-little-language-that-could-61eaa62b5e0a):见[府的陷落节点](/javascript-non-grata/the-fall-of-the-house-of-node-43697fd56a6)。

我已经写了十多年的 web 应用程序，我对 JavaScript 的了解如此之少，真是令人震惊！除了与 jQuery 之类的接口之外，您根本不需要使用太多的 JavaScript。我用 Java、Python ( [web2py](http://web2py.com/) )、C#、PHP (Drupal)、Smalltalk ( [Seaside](http://seaside.st/) )和 Go ( [Beego](https://beego.me/) )完成了我所有的 web 开发工作。特别是对于前端，[我用了 Amber Smalltalk](/smalltalk-talk/a-gentle-introduction-to-amber-8c532631e9ab) 。

所以真的是你的选择。你是否想深入 JavaScript 的混乱世界取决于你自己。就前端工作而言，这就是钱的来源。但这也是 JS web 框架混乱的地方，这导致了“框架疲劳”角 1，角 2，反应，余烬，流星，脊梁，击倒，水银，聚合体，奥瑞利亚，秘银，Vue 等。(React 是当前的“热点”,但 Vue 很可能会推翻它。)这些框架有果蝇的寿命！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)