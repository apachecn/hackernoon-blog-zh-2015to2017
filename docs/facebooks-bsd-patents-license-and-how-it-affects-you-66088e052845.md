# 脸书的 BSD+专利许可证以及它如何影响你的公司或副业

> 原文：<https://medium.com/hackernoon/facebooks-bsd-patents-license-and-how-it-affects-you-66088e052845>

TL；博士，首先让我说我不是律师，只有律师才能了解你的情况。

![](img/69ef22d42b8b91607a93b4aa3f2028f8.png)

Image found via Google Images at [https://img.rt.com/files/2017.01/original/588cfd87c4618899638b45e4.jpg](https://img.rt.com/files/2017.01/original/588cfd87c4618899638b45e4.jpg)

# **更新:** [脸书将 React、Jest、Flow 和 Immutable.js 重新授权给麻省理工学院](https://code.facebook.com/posts/300798627056246/relicensing-react-jest-flow-and-immutable-js/)

这里有很多关于脸书 BSD+专利许可证的不满，这主要是因为语言不太清晰。此外，请记住，网飞已经让他们的律师看过了，他们认为对他们来说没有问题。由于 Apache Software Foundation 法律事务委员会宣布，许可证问题已被重新提起

[https://issues.apache.org/jira/browse/LEGAL-303?focusedCommentId = 16088663&page = com . atlassian . jira . plugin . system . issuetabpanels:comment-tab panel # comment-16088663](https://issues.apache.org/jira/browse/LEGAL-303?focusedCommentId=16088663&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-16088663)

脸书的 BSD+专利许可证不再被允许作为 Apache 项目中的直接依赖项。这个评论最好地描述了其中的原因。

> 我将以 WordPress 为例。如果 WordPress 采用 React，他们目前正在使用 React 来构建 Gutenberg 项目，该项目计划取代 WordPress 中的主编辑器，这可能会对商业 WordPress 产品的整个 WordPress 生态系统产生影响。是的，其中许多是小企业。但是，其中许多也被更大的企业收购了。如果 WordPress 采用 React，而创业公司的产品建立在 WordPress 之上……那些更大的企业可能会退出收购，因为他们的法律团队在对 React 条款进行尽职调查时发出了危险信号。

[评论-317143085317143085](https://github.com/facebook/react/issues/10191#issuecomment-)

如果脸书提供在线流媒体服务，那么网飞可能会改变他们对许可证的立场，但现在他们不认为这是一个问题。如果你打算用脸书的技术开始副业/公司/创业:

*   [graph QL](http://graphql.org/)
*   [**反应过来**](https://github.com/facebook/react)
*   [**接力**](https://github.com/facebook/relay)
*   [**反应原生**](https://github.com/facebook/react-native)
*   [](https://github.com/facebook/flow)
*   **[**不可变. js**](https://github.com/facebook/immutable-js)**
*   **[**笑话**](https://github.com/facebook/Jest)**
*   **[**预包装**](https://github.com/facebook/prepack)**
*   **[**创建-反应-应用**](https://github.com/facebookincubator/create-react-app)**

**从我的发现来看，只要你理解许可，并有一个迁移计划，如果它成为一个问题，这是好的。人们说“关掉反应”的唯一原因是你只是避免这种情况，因为这是不必要的风险。这里有一个过于简化的分类可能会有所帮助。**

*   **如果你不打算拥有任何专利，这不是一个问题。**
*   **如果你计划被一家公司收购，确保他们也没有问题，或者在做尽职调查之前就迁移。**
*   **如果你不在美国，专利法甚至不会影响你，除非你在美国做生意。**
*   **如果你真的打算起诉脸书侵犯专利，在你这么做之前，确保你把脸书的技术转移出去，否则他们会反诉你。**

# **放弃**

**再说一次，我不是律师，这只是我在 twitter/articles/etc 上找到的，要确定这是否会影响到你的公司，唯一的方法就是问你的律师。如果有更多的问题或者任何我应该写在这篇文章里的东西，请随时发微博给我，地址是 [@gdi2290](https://twitter.com/gdi2290) 。**

# **没有专利许可证的开源框架**

**[超过 35 家公司的 75 个以上受欢迎的开源项目(6000+star 及以上)，并得出结论，脸书是开源领域中的一个*例外*。另一家使用相同许可模式的公司是 Palantir。大多数框架都是 MIT 的，它允许你做任何你想做的事情。](/@raulk/list-of-companies-and-popular-projects-by-the-open-source-licenses-they-use-35a53eaf1c80)**

> **特此免费授予获得本软件和相关文档文件(“软件”)副本的任何人不受限制地经营本软件的权利，包括但不限于使用、复制、修改、合并、发布、分发、再许可和/或出售本软件副本的权利，并允许获得本软件的人这样做**

**[https://opensource.org/licenses/MIT](https://opensource.org/licenses/MIT)**

**[角度](https://github.com/angular/angular)通过 MIT(和[角度](https://github.com/angular/angular.js)通过 MIT)**

**[通过麻省理工学院预测](https://github.com/developit/preact)**

**通过麻省理工学院**

**通过麻省理工学院**

# **资源**

# **[Apache 软件基金会法律事务委员会已经宣布“脸书 BSD+专利许可证”不能成为 Apache 项目的直接依赖项。](https://issues.apache.org/jira/browse/LEGAL-303?focusedCommentId=16088663&page=com.atlassian.jira.plugin.system.issuetabpanels:comment-tabpanel#comment-16088663)**

# **[创建 Github 问题，要求 React 更改他们的许可证](https://github.com/facebook/react/issues/10191)**

# **[脸书讲解 React 的执照](https://code.facebook.com/posts/112130496157735/explaining-react-s-license/)**

# **[黑客新评论关于脸书解释许可证](https://news.ycombinator.com/item?id=15050841)**

# **[**黑客新闻评论**](https://news.ycombinator.com/item?id=15053082)**

# **法律注释:ReactJS 的许可计划是怎么回事？**

# **[React 许可-专利的非主张义务过于宽泛—](http://qiita.com/Cat_sushi/items/d52332bb10073e5fadfb)**

**[](/@dwalsh.sdlr/using-graphql-why-facebook-now-owns-you-3182751028c9) [## 使用 GraphQL？为什么脸书现在拥有你🐲

### 使用 graph QL——为什么脸书现在拥有你

medium.com](/@dwalsh.sdlr/using-graphql-why-facebook-now-owns-you-3182751028c9) [](/@raulk/if-youre-a-startup-you-should-not-use-react-reflecting-on-the-bsd-patents-license-b049d4a67dd2) [## 如果你是一家初创公司，你不应该使用 React(反映在 BSD +专利许可证上)

### 也就是说，如果你希望被更大的公司收购

medium.com](/@raulk/if-youre-a-startup-you-should-not-use-react-reflecting-on-the-bsd-patents-license-b049d4a67dd2) [](https://meshedinsights.com/2017/07/27/5-reasons-facebooks-react-license-was-a-mistake/amp/) [## 脸书的 React 执照是个错误的 5 个理由

### 脸书的 BSD+专利许可组合失败不是因为许可本身，而是因为它忽略了更深层的本质…

meshedinsights.com](https://meshedinsights.com/2017/07/27/5-reasons-facebooks-react-license-was-a-mistake/amp/) [](/@dwalsh.sdlr/react-facebook-and-the-revokable-patent-license-why-its-a-paper-25c40c50b562) [## 反应，脸书，和可撤销的专利许可证。为什么是一篇论文🐯。

### 正如格特鲁德·斯坦因可能会告诫的，“那里没有那里。”

medium.com](/@dwalsh.sdlr/react-facebook-and-the-revokable-patent-license-why-its-a-paper-25c40c50b562) [](/@raulk/list-of-companies-and-popular-projects-by-the-open-source-licenses-they-use-35a53eaf1c80) [## 对 35 家公司的 75 多个开源项目所使用的许可证的分析

### 脸书几乎是唯一一个使用 BSD-3 +强力专利报复的国家

medium.com](/@raulk/list-of-companies-and-popular-projects-by-the-open-source-licenses-they-use-35a53eaf1c80)**