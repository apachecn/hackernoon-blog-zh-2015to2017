# 带有 Vue.js 的原生应用:Weex 还是 NativeScript？第一章

> 原文：<https://medium.com/hackernoon/native-apps-with-vue-js-weex-or-nativescript-8d8f0bac041d>

![](img/ce23ea62b3669fdfe84e82df547915de.png)

> **更新 07-03-2018** :现在有了这篇文章的[第二章](/@tiagoreisalves/native-apps-with-vue-js-weex-or-nativescript-chapter-ii-6d1776da090d)！一定要在读完这本书后检查一下。

[Vue.js](https://hackernoon.com/tagged/vue-js) 是一个很棒的框架！它有一个友好的学习曲线，结合了 React 的组件方法和 Angular 的模板的优点。尽管如此，Vue.js 仍然有一个可能成为交易破坏者的限制:与 React 的 [React Native](https://hackernoon.com/tagged/react-native) 不同，Vue.js 目前没有稳定、广泛采用的方法来开发原生应用。

不过，这种情况一定会改变！目前有两个正在开发的框架可以打开用 Vue.js 创建原生应用的大门，我们都喜欢和珍惜这个框架: **Weex** 和 **NativeScript** 。在本文中，我们将比较这两种框架，让您至少尝试其中一种！

# Weex

Weex 是中国电子商务巨头阿里巴巴创建的一个项目。它的口号是“一次编写，随处运行”，这意味着你可以从完全相同的代码库为 web (html5)、Android 和 iOS 制作版本。目前有几个 Weex 项目正在生产中，掌握在中国数百万用户手中。Weex 的核心正在积极开发中，每周都会有 PRs 发布。Weex 有几个组件、与原生平台交互的插件，以及一组仍然有些粗糙的工具。

不幸的是，Weex 开发者并没有把为开源社区提供这个平台看作是一个优先考虑的事情。确定你有[文档](https://weex.incubator.apache.org/references/)，github 回购等。，现在 Weex 在 Apache 孵化器(ASF)下，一些事情正在变得更好。但是……如果不对本地代码进行大量的修改，仍然没有明确的方法从头开始构建 Weex 项目。还有，忘了在官方 gitter 频道用英语交谈吧

# 原生脚本

四月，[伊戈尔·兰杰洛维奇](https://twitter.com/igor_randj) [开启了](https://twitter.com/igor_randj/status/854501034697383936?ref_src=twsrc%5Etfw&ref_url=https%3A%2F%2Fwww.nativescript.org%2Fblog%2Fa-new-vue-for-nativescript)使用 Vue 和 NativeScript 的可能性。`[nativescript-vue](https://github.com/rigor789/nativescript-vue)`是一个 NativeScript 插件，它弥合了 Vue.js 虚拟 DOM 和 NativeScript 组件之间的差距，允许您使用 Vue.js 构建跨平台的应用程序。虽然该项目仍处于早期阶段，不适合生产应用程序，但它有很大的潜力，因为它利用了 NativeScript 框架及其所有工具和大量组件和[插件](http://plugins.nativescript.org)。有了 webpack，你甚至可以使用`.vue`单个文件组件(参见[这个模板](https://github.com/tralves/nativescript-vue-webpack-template))。

这个社区很棒，非常好！如果你加入官方的 slack，你会发现很多友好的人愿意帮忙，包括 NativeScript 核心团队开发者、`nativescript-vue`创建者和其他参与项目的人。有很多人想要`nativescript-vue`工作！

# 利弊总结

总结每个框架的优点和缺点，我们有:

## Weex:

👍生产中(虽然，只在中国)；
👍适用于 Web、Android 和 iOS 的版本；
👎不良社区；
👎粗糙的工具；
👎没有启动项目的清晰路径；

## nativescript-vue:

👍超赞的社区！
👍利用所有 NativeScript 平台；
👎没有准备好生产；
👎仅适用于 Android 和 iOS 的构建(尽管有人使用 web 构建进行代码共享)；

# 定论

在开源项目中，社区胜过技术。Weex 领先了几个月，并得到了一家大公司的支持，但我们还没有看到社区方面的进展。另一方面， **NativeScript + Vue** 得到了越来越多的关注。当然，有一些粗糙的边缘，但一旦它们被清除，我们将有一个坚实的框架来开发 Vue.js 的原生应用程序。

我赌 **NativeScript** ！

> **接下来:**
> > > > >转到[第二章](/@tiagoreisalves/native-apps-with-vue-js-weex-or-nativescript-chapter-ii-6d1776da090d)。

# 链接

## **Weex:**

*   官方网站:[http://weex.incubator.apache.org/](http://weex.incubator.apache.org/)
*   weex How-to:[https://hacker noon . com/How-to-create-a-weex-vue 2-project-6b 94981 bee4e](https://hackernoon.com/how-to-create-a-weex-vue2-project-6b94981bee4e)
*   用 Weex 制作的演示待办应用:[https://github.com/tralves/weex-todo-list](https://github.com/tralves/weex-todo-list)

## NativeScript + Vue.js:

*   `nativescript-vue`:[https://github.com/rigor789/nativescript-vue](https://github.com/rigor789/nativescript-vue)
*   关于`nativescript-vue`的文章:[https://www . native script . org/blog/a-new-vue-for-native script](https://www.nativescript.org/blog/a-new-vue-for-nativescript)
*   原生脚本松弛通道:[http://developer.telerik.com/wp-login.php?动作=松弛-邀请](http://developer.telerik.com/wp-login.php?action=slack-invitation)
*   NS + Vue 模板:[https://github.com/tralves/nativescript-vue-template](https://github.com/tralves/nativescript-vue-template)
*   带 webpack 的 NS + Vue 模板(准备好用于`.vue`单个文件组件):[https://github . com/tralves/native script-Vue-web pack-template](https://github.com/tralves/nativescript-vue-webpack-template)

## 嗯…你怎么知道这些东西的？

我玩 Weex 已经有一段时间了。我[做了](https://twitter.com/tiagoreisalves/status/816466567496089601) [测试](https://twitter.com/tiagoreisalves/status/841504371015860224)，建了一个[试玩 app](https://github.com/tralves/weex-todo-list) ，一个[穿越视频](https://www.youtube.com/watch?v=ZM3tJMSNC1E&feature=youtu.be)，[写了](/@tiagoreisalves/the-state-of-weex-an-inaccurate-and-outdated-report-fae2c6e5fc2e) [文章](https://hackernoon.com/how-to-create-a-weex-vue2-project-6b94981bee4e)，在[本地事件](https://twitter.com/tiagoreisalves/status/854840414611931136)中呈现了关于 Weex 的[。然后，我登上了`nativescript-vue`列车！我写了一个](https://docs.google.com/presentation/d/18NKqd3_AHmAXQCx_37rVf_v16-HHSkCxJ85qYu6SF7Y/edit?usp=sharing)[傻 app](https://github.com/tralves/not-hotdog-ns-vue) ，[赢了一个比赛](https://www.nativescript.org/blog/announcing-the-winners-of-the-vue.js-nativescript-contest)还建了一个[模板](https://github.com/tralves/nativescript-vue-webpack-template)的[情侣](https://github.com/tralves/nativescript-vue-template)来平滑入门过程。