# 看在老天的份上，不要在软件中使用正则表达式了

> 原文：<https://medium.com/hackernoon/for-the-love-of-jeez-stop-using-regex-in-software-3a916e2c5987>

## 如果用 regex 做不到，我们也做不到！

你知道什么是代码混淆器吗？这是一个小程序或编译器工具链的一部分，它可以将你的程序代码打乱成一堆看似随机的字母和符号，这样其他人就无法阅读它(如果不使用相应的工具来帮助解读它)。)代码仍然以同样的方式工作，但它只是不可读。

想象一下，如果你和我一起编写代码。有一天，我签入一些随机的代码，并把它交给你进行代码审查。只是有些不同。现在，我已经通过代码混淆器发送了它，甚至在它被编译之前。混乱的代码，看起来随机的符号，就在拉请求中。在源代码中。用吉特。你有一个简单的 crud 应用程序，现在里面有实际的 CRUD 了。

你会怎么做？

这很难说，因为这种情况没有什么意义，很难把它看作是一个恶作剧。但是，当某人面无表情地将 regex 提交到软件库中时，就会发生这种情况。

Regex 是一个方便的 sysadmin 工具，用于执行日志解析之类的特定任务。没多也没少。在 Bash/Linux 中将字符串连接在一起时，它为您提供了一种简单的查找方法。

Regex 不是魔法。Regex 本质上“编译”成一个复杂的状态机，该状态机用大量 if 语句跟踪条件。它没有一些神奇的 SIMD 性能提升。事实上，相反——regex 通常比编写自己的 if 语句组要慢。

正则表达式不属于软件，因为它是不可读的。如果你在团队中工作，你可能会拒绝某人的代码审查，因为某些部分太难理解了。正则表达式总是很难理解，因为它甚至不是代码。很难确认 regex 的准确性，因为你需要一个显微镜来检查和反复检查所有转义字符和其他神奇符号的准确性。你必须全部手动翻译。没有人期望能够编辑别人的正则表达式。还不如删了重新来过。但是你实际上不知道它是做什么的。它不是自我记录的。这是自我混淆。阅读 regex 提醒你在学习如何编程之前是什么感觉。

正则表达式不属于软件，因为它是一个安全缺陷。某些 Regex 字符串可能包含性能很差的段，以至于一个精心制作的字符串(通常只是一个相同字符的长字符串)将导致代码几乎永远运行，攻击者可以简单地将该字符串作为输入发送，以创建针对服务的自包含 DOS 攻击。您的员工中可能没有人能一眼就告诉您给定的正则表达式字符串是否是安全缺陷。(因为看不懂 regex。)

Regex 对于 XML 或 JSON 的解析、序列化或反序列化没有用。Regex 只能解析常规语法，但是前面提到的语言是上下文无关的语法。在 web 服务框架中，您不希望正则表达式将请求动态映射到路由。您需要一个定制的树状数据结构。

你不需要正则表达式来验证任何东西。你需要一系列的 if 语句。If 语句可以被人类(和机器)验证。)If 语句可以很容易地随着业务需求和约束的变化而修改。您的正则表达式只能精确地验证符合 RFC 的电子邮件地址，必须完全抛弃它，因为，哎呀，您的客户有一个不符合 RFC 的电子邮件地址，因为 RFC 是编造的。

Regex 不是某种神奇的、神秘的、高级的、计算机科学的最终前沿。实际上它很烂，不要在软件中使用它。