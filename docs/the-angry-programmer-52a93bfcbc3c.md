# 愤怒的程序员🔊

> 原文：<https://medium.com/hackernoon/the-angry-programmer-52a93bfcbc3c>

## 一个工程师如何能同时胜任和不胜任

![](img/9169abfa3f0c628b1ec682ae80f5dc82.png)

The Grumpy Cat, a famous internet meme. Not angry, but almost there.

Listen to the audio version!

你可能认识他们。

他们不喜欢争论。他们的时间只值得花在那些已经证明自己有价值的人身上。然而，当他们争论时，他们用个人经历而不是理由。唯一理解的人，是那些在他们的核心圈子里分享相似经历的人。

他们责怪公司的招聘策略导致雇佣了不称职的工程师，也责怪公司里那些不称职的工程师。他们认为，如果人们有能力，或者如果公司在早期投资使用他们建议的新框架或库来解决所有问题，遗留代码就不会存在。

他们不喜欢教书。他们已经厌倦了。他们厌倦了解释事物，也厌倦了不把它们应用到他们想要的标准中。

他们厌倦了[配对](/@fagnerbrack/pair-programming-8cfbf2dc4d00)。他们认为[编程](https://hackernoon.com/tagged/programming)很容易(因为他们的[知识诅咒](https://en.wikipedia.org/wiki/Curse_of_knowledge))，因此每个人都有义务独自了解每个最佳实践、设计模式和测试策略。每个人都应该学会以正确的方式编码。那些做不到这一点的人是愚蠢的，不值得关注或尊重。

他们对不理解的人感到愤怒。他们首先相信自己是对的，但只有当有人提供了与他们的主张相反的客观实证论据时，他们才会承认自己是错的，即使他们最初的主张不是由客观实证论据组成的。如果没有强有力的论据来反驳他们的主张，他们的主张总是对的。

他们对那些认为自己无所不知的初学者感到愤怒。他们大喊大叫，诅咒，而不是客观地指出没有人知道所有的事情，并解释为什么初学者可能是错误的，以及他们可以做些什么来帮助获得正确的答案。

> 愤怒的程序员是这样一种人，他们把时间花在对错误的事情感到愤怒上，而不是高效地工作去做正确的事情。

有一个有趣的测试叫做[Myers-Briggs Type Indicator](https://en.wikipedia.org/wiki/Myers%E2%80%93Briggs_Type_Indicator)(MBTI)。测试结果由 [16 种性格](https://www.16personalities.com/personality-types)组成，试图表明人们如何感知世界和做决定的心理偏好。

[测试很弱](/@fagnerbrack/i-am-not-psychologist-so-i-cant-argue-against-that-claim-46b1a60cbdf2)被认为是伪科学。试图在一小群人格中概括人类行为的复杂性并不是一件容易的事情，而且在被认为是真理之前，肯定需要更仔细的审查。然而，就本文的目的而言，我不打算将 MBTI 视为真理，而只是作为一组假想的被描述的人格的心理模型，以及它们如何与愤怒的程序员的特征相关联。

西安大略大学在 2002 年进行了一项研究。该研究旨在以 MBTI 为基线，确定哪些性格在软件工程行业更为普遍。ISTJ 和 ESTJ 的性格似乎在这个行业中占了最大的比例，而内向的 INTJ 和 INTP 似乎在软件工程中比一般人更有代表性(分别为 7%和 8%)。

我已经从几个独立的个人那里看到了很多轶事证据，证明了软件开发行业中最好的工程师的某种性格模式。如果 MBTI 是对的，他们将被归入[“分析师”](https://www.16personalities.com/personality-types)范围(*nt*)，并将由以下人员组成:

*   [建筑师](https://www.16personalities.com/intj-personality) ( [INTJ](https://en.wikipedia.org/wiki/INTJ) )

> 规则、限制和传统是 INTJ 人格类型的诅咒——一切都应该接受质疑和重新评估，如果他们找到了方法，intj 通常会单方面采取行动，制定他们在技术上优越的，有时不敏感的，几乎总是非正统的方法和想法。
> 
> ——[16personalities.com](https://www.16personalities.com/intj-personality)上[INTJ 个性](https://www.16personalities.com)

*   [逻辑学家](https://www.16personalities.com/intp-personality) ( [INTP](https://en.wikipedia.org/wiki/INTP) )

> INTPs 以其卓越的理论和不懈的逻辑而闻名——事实上，他们被认为是所有人格类型中逻辑最精确的。
> 
> ——[16personalities.com](https://www.16personalities.com/intp-personality)[上的 INTP 个性](https://www.16personalities.com)

*   [指挥官](https://www.16personalities.com/entj-personality) ( [ENTJ](https://en.wikipedia.org/wiki/ENTJ) )

> 贯穿 ENTJ 头脑的潜在想法可能是这样的:“我不介意你说我是一个麻木不仁的 b*****d，只要我仍然是一个高效的 b*****d”。
> 
> ——[16personalities.com](https://www.16personalities.com/entj-personality)上的 ENTJ 个性

*   [辩手](https://www.16personalities.com/entp-personality)

> 一个奇怪的并列出现与 ENTPs，因为他们毫不妥协的诚实，但会不知疲倦地争论一些他们实际上不相信的东西，站在另一个人的立场上从另一个角度争论一个真理。
> 
> — [ENTP 个性](https://www.16personalities.com/entp-personality)上[16personalities.com](https://www.16personalities.com)

我的理论是，愤怒的程序员更有可能坐在最佳软件工程师的评判人格中(也许是 INTJ 和 ENTJ)，但理论上，没有什么能阻止他们从任何其他评判人格中脱颖而出。愤怒的程序员基本上是一组带有强烈**判断**特征的行为属性。

> 愤怒的程序员代表了形成独特个性的一组特定属性。它可以改变。

任何人都可以有意识或无意识地改变自己的个性，包括愤怒的程序员，这就是为什么能够改变很重要的原因。当程序员生气的时候，他们没有机会退后一步，做出清晰的判断，找到更好的答案。这意味着愤怒的程序员可能会对某个问题提出糟糕的建议，即使答案是正确的！如果答案不正确，他们有可能会认为自己是对的，即使他们是错的。

Julia Galef explains why some people prefer defending their own beliefs instead of seeing the world as it really is.

如果专业人员不能有效地解决问题或传达解决方案，即使存在外部障碍，他们也要承担创建旨在消除这些障碍的策略的负担。

> 如果愤怒的程序员因为别人的无能而无法解决问题，那么他们也是因为无法提供解决方案而无能。

另一个有趣的影响是，如果愤怒的程序员总是同意自己圈子里的人，拒绝圈子外的人(除非他们已经证明了自己)，那么他们永远不会真正遇到另一个在他们看来是“愤怒的程序员”的人。像他们这样的人将能够清晰地相互交流，同时排除那些持怀疑态度但仍有兴趣站在他们肩膀上的人。

愤怒的程序员不太可能给他们参与的项目或团队带来长期价值，但承认他们的存在是找到他们并提出帮助策略的第一步，这样他们就可以不那么愤怒，变得更有用。

> 承认愤怒的程序员的存在是帮助他们少生气、更有用的第一步。

愤怒的程序员可以存在于任何地方(甚至编程之外)，但他们之间有一些共同点，这可能与个人性格有关。

如果你不能承认愤怒的程序员的存在，或者从未遇到过，那么也许(仅仅是也许)你一直都是一个愤怒的程序员而不知道。

最终，我们所有人内心都有一点愤怒的程序员。我们总是与做错事的人和公司合作，这有时会让我们生气。

然而，真正重要的是，我们要有自知之明，知道如何处理它，并想出聪明的行动来纠正错误，而不是对此感到愤怒。

生气不会改变什么。

感谢阅读。如果您有任何反馈，请通过 [Twitter](https://twitter.com/FagnerBrack) 、[脸书](https://www.facebook.com/fagner.brack)或 [Github](http://github.com/FagnerMartinsBrack) 联系我。

想亲自聊聊吗？你可以在 [**悉尼软件工匠会议**](https://www.meetup.com/Software-Crafters-Sydney/) 上找到我。

如果你的团队会从我关于这篇文章或我正在进行的任何其他项目的谈话和/或问答中受益，请在`contact at fagnermartins.com`给我写信。

**编辑 2019 年 1 月 16 日:** [娜奥米·莫斯特](https://medium.com/u/3d983c2205c3?source=post_page-----52a93bfcbc3c--------------------------------)在一份[颇有见地的回复](/@nthmost/i-respect-your-attempt-to-find-associations-with-personality-preferences-but-as-a-trained-ca24064a8441)中指出，在撰写本文时，我可能对 MBTI 的性格类型有一些误解。这并不是第一次发生，我写过一篇关于[依赖抽象](https://hackernoon.com/the-danger-of-relying-on-abstractions-dfa04a8d553d)会如何导致问题和误解的文章。