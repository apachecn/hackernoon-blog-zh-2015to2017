# Google IO 2017 对开发者来说有什么新内容

> 原文：<https://medium.com/hackernoon/whats-new-for-developers-in-google-io-2017-c108ec93cf33>

来自 [Google](https://hackernoon.com/tagged/google) IO 2017 开发者主题演讲的简短总结，对于那些不想花一个小时观看它的所有 Android 开发者:

所以，他们有以下大话题可以谈:

1.  一种新的语言支持:Kotlin
2.  更新的工具:Android Studio 和库
3.  应用质量及其成功
4.  Android 即时应用
5.  面向开发者的谷歌助手
6.  谷歌与人工智能和机器[学习](https://hackernoon.com/tagged/learning)
7.  谷歌和网络体验
8.  开发应用和发展业务

# kot Lin——一种成熟的生产就绪编程语言

[Stephanie Saad Cuthbertson](https://medium.com/u/8488e6888e1a?source=post_page-----c108ec93cf33--------------------------------) 和 [Tor Norbye](https://medium.com/u/8251a5f98c9d?source=post_page-----c108ec93cf33--------------------------------) 出色地展示了 Kotlin 及其特点。

Kotlin 已经存在 5 年了，它是由构建 [Idea IntelliJ](https://www.jetbrains.com/idea/) 的同一个团队构建的。Android Studio 将为这一语言特性提供出色的支持。像 Pinterest、Expedia 和其他一些著名的应用程序已经在他们的产品中使用了 Kotlin。这种编程语言与 Java 是完全互操作的，这意味着你可以使用一个 Kotlin 类的成员来玩另一个 Java 类，反之亦然。您可能想添加 Kotlin 语言支持，但它完全是可选的。好吧，听起来不错。

让我们从一个简单的 POJO 开始，它有 56 行代码*Customer.java，其中充满了 getter-setter、equals 和 hashCode 以及 toString()方法*:

```
public class Customer {
   private String name;
   private String email;
   private String company;

   public Customer(String name) {
       this(name, "", "");
   }

   public Customer(String name, String email) {
       this(name, email, "");

   }

   public Customer(String name, String email, String company) {
       this.name = name;
       this.email = email;
       this.company = company;
   }

   public String getName() {
       return name;
   }

   public void setName(String name) {
       this.name = name;
   }

   public String getEmail() {
       return email;
   }

   public void setEmail(String email) {
       this.email = email;
   }

   public String getCompany() {
       return company;
   }

   public void setCompany(String company) {
       this.company = company;
   }

   @Override
   public boolean equals(Object o) {
       if (this == o) return true;
       if (o == null || getClass() != o.getClass()) return false;

       Customer customer = (Customer) o;

       if (name != null ? !name.equals(customer.name) : customer.name != null) return false;
       if (email != null ? !email.equals(customer.email) : customer.email != null) return false;
       return company != null ? company.equals(customer.company) : customer.company == null;
   }

   @Override
   public int hashCode() {
       int result = name != null ? name.hashCode() : 0;
       result = 31 * result + (email != null ? email.hashCode() : 0);
       result = 31 * result + (company != null ? company.hashCode() : 0);
       return result;
   }

   @Override
   public String toString() {
       return "Customer{" +
               "name='" + name + '\'' +
               ", email='" + email + '\'' +
               ", company='" + company + '\'' +
               '}';
   }
}
```

但是当转换成 Kotlin *Customer.kt* 时，代码减少到只有一行:

```
data class Customer(var name: String, var email: String = "",
                    var company: String = "")
```

![](img/d351d46def75fb02ff473ed985ad064b.png)

**将代码从 Java 转换成 Kotlin:**

假设你知道如何用 Java 写一段代码，但不知道如何用 Kotlin 写。所以，首先用 Java 编写你的代码，然后简单地复制粘贴到 kotlin 文件中。

![](img/a45812144a7c7f5da246a09e915a3678.png)

Android Studio IDE 会自动将你的 Java 代码转换成 Kotlin 代码，(你又会像…)

![](img/79f74a5563cd0400797429ca49a15741.png)

Super Excited !

# 更新的工具:Android Studio 和库

## 安卓工作室

Android 的工具团队致力于让 IDE 更智能、更快，并加快构建速度。Android Studio 刚刚获得了新的更新[Android Studio 3.0 Canary 1](https://developer.android.com/studio/preview/index.html)。

Android Studio 刚刚获得了 3 款新的**评测**工具，名为“ [Android Profilers](https://developer.android.com/studio/preview/features/android-profiler.html) ”:

1.  [网络分析器](https://developer.android.com/studio/profile/network-profiler.html)
2.  [CPU 剖析器](https://developer.android.com/studio/profile/cpu-profiler.html)
3.  [内存分析器](https://developer.android.com/studio/profile/memory-profiler.html)

Android Studio 还带有预览功能，用于调试任何 APK，并可以使用这些分析器。

**梯度构建和同步速度**通常从 3 分钟减少到 2 秒。

他们在 Android 模拟器中加入了 **PlayStore。**

Android Studio 3.0 包括**平台支持**如即时应用支持、Android 操作系统:系统图像、自适应图标、可下载字体和 XML 字体

我们不再需要依赖 Android SDK 管理器。Android 依赖项被托管在 Maven 仓库中，所以我们可以从那里直接使用。

```
maven { url 'https://maven.google.com' }
```

此外，我们在 Android Studio 3.0 中获得了更好的文件浏览器和改进的布局检查器

## 图书馆:

长期以来，我们一直在要求 Android 应用程序开发的这三个主要方面:

1.  更简单的 Android 框架

2.固执己见的最佳实践指南

3.处理生命周期的更好方式

你会很高兴地欢迎 [**安卓架构**](https://developer.android.com/topic/libraries/architecture/index.html) 。

现在，我们有了图书馆:

*   [视图-模型](https://developer.android.com/topic/libraries/architecture/viewmodel.html)模式
*   [数据存储](https://developer.android.com/topic/libraries/architecture/room.html)
*   [生命周期管理](https://developer.android.com/topic/libraries/architecture/lifecycle.html)

我们将在我的下一篇文章中讨论这个问题；-)

# 应用质量及其成功

[Ellie Powers](https://medium.com/u/740e3437d237?source=post_page-----c108ec93cf33--------------------------------) 非常好地解释了如何改善应用质量体验，创造一个伟大的成功故事。

**设备目录:**浏览设备目录及其详细规格

![](img/82749a263490032bff6bffe1730b4d5d.png)

**设备定位:**

![](img/324daa3fc8b7ba1672eb9b184efa8f4e.png)

我们还可以设置目标规则来解决特定于设备的问题。

**设备安装、评级和收入:**

![](img/f646cbe0975817303305f84c9b9f985a.png)

我们还可以按设备查看安装量、收视率和收入。

# Android 即时应用

![](img/55ac85da41aa3e71c827b3c02cc95e2f.png)

所以安卓即时应用现在对所有人开放。Android Studio 3.0 让你可以创建 [**Android 即时应用**](https://developer.android.com/topic/instant-apps/index.html) 。你不必为创建即时应用程序单独编写任何代码。如果你计划支持即时应用，你只需要模块化你的应用:

![](img/166503f15ef79b3c976f233e1ba3e003.png)

Modularize your app

但是怎么做呢？？？

嗯，你一点也不用担心。Google 帮你搞定了，他们也为此创建了模块化重构操作工具。此工具将帮助您将文件移动到相关模块。

![](img/13736107f846a7408b169166da8be794.png)

Modularize in Android Studio

甚至他们还有缩小你的安卓即时应用的工具:

1.  节省空间的共享库
2.  优化资产交付
3.  在线压缩

Android O 现已为即时应用程序准备好了新的 API:

1.  高效的运行时沙箱
2.  发射器支架
3.  主屏幕显示

# 面向开发者的谷歌助手

谷歌的智能为开发者带来了什么，布拉德·艾布拉姆斯揭示了开发者可以用谷歌助手 SDK 做的事情。

首先，谷歌助手现在也可以在 iOS 上使用。

![](img/a726031c6ea5b0f47fc7d81a3070f89f.png)

Actions on Google

Google Assistant 增加了新的用户界面选项，使用户会话更具互动性:

1.  图像传送带
2.  列表
3.  建议筹码

![](img/4f6eb74661847ea829d771aa2f4e5505.png)

Google Assistant 已准备好支持您的应用:)

![](img/9636dddbb666ad958d959b78e14598ee.png)

Google Assistant

Google Assistant 还为您带来了交易库:

**谷歌促成的支付:**您的一站式支付模块集成

![](img/b9ba2a4a12a1e23ce069ac21fa73a270.png)

**无缝账户链接:**创建或链接现有支付账户

![](img/c8f4b505f2edaede23a2e630229c8d59.png)

Seamless Account Linking

**重新约定:**从订单历史中重新订购

![](img/b13ca70dc99d472cb568b5666493b6d7.png)

Re-engagement

**订单更新:**订单状态即时更新

![](img/76d4ff69c3de4686c0051a1bcd9a5b52.png)

Order Updates

**App 目录**

让你的应用到达人们身边也很重要，这就是谷歌助手将帮助你的地方。应用程序目录不仅向你推荐应用程序，还会给你一个“尝试”的选项。酷…!！

![](img/f17811a09a4c51b0919cbb7fa09e633b.png)

App Directory

**快捷方式:**用户也可以创建你的应用程序的快捷方式，因此它可以从助手本身调用。+1.

![](img/9a5308ac052e6443df88090cdef0e7bd.png)

Shortcuts

**谷歌开发者控制台:**

![](img/8ca6e0309704c29f3e597e40aac717a2.png)

动作很好地集成在 Google 开发者控制台中，使您能够轻松利用 Firebase 和 Google Cloud 的强大功能。

![](img/75d5dbbdb0e3106c419d54a8944013bd.png)

Google Assistant 兼容以下产品:

![](img/5272cf2fe40f50a4e4f551bf9fcdea10.png)

你可以通过谷歌助手开始使用 [api.ai](https://api.ai/) ，让你的对话更加“对话化”:)

![](img/58689d4e4e29b792ceeb5d649bad7931.png)

api.ai

# 谷歌致力于人工智能

谷歌一直在 AI(人工智能)和 ML(机器学习)领域投入巨大资源。费(首席科学家，云人工智能和 ML)展示了谷歌一直在努力的东西。

使 API 民主化:谷歌提出了这些新的 API

![](img/8a24da37bd3c5694761b2dea5b5be4fb.png)

**张量流:**张量流刚刚更新到 1.2 版本，可以开始使用了。

![](img/84e1dce4dece88a7507e26634b39d5ac.png)

Tensor Flow v1.2

**谷歌云机器学习引擎:**

![](img/dab9e14d76ef5780904f73aab2cec499.png)

TPU — Tensor Processing Unit

如果你想在谷歌云引擎上学习和尝试云 TPUs，就在[g.co/tpusignup](http://g.co/tpusignup)上报名吧

# 谷歌网络体验

谷歌如何在 Chrome 上改进网络，**Tal Oppenheimer**(Chrome 产品经理)很少为我们更新，鼓励我们在 PWA ( [渐进式网络应用](https://developers.google.com/web/progressive-web-apps/))和 AMP ( [加速移动页面](https://www.ampproject.org/))上开发应用

**渐进式网络应用:** Twitter 变得渐进式:)在 [Twitter Lite](https://lite.twitter.com) 上看看这个。

![](img/e03fb642c4a60d305b5b8e6cea55e197.png)

Twitter Lite

您是否曾想过在 PWAs 上进行构建有多容易？Wego 的 PWA 仅用了两个月就完成了，仅仅由一名软件工程师完成，他是 Web 开发的新手。哇哦..！！！

![](img/a04e4c3abd63788a7a36a2eec2d0f8af.png)

Wego

许多 IT 巨头开始进步:

![](img/ee147d086da83ad8664aec3ee71bb2fb.png)

**LightHouse:** 直接从 chrome 上检查你的网络应用性能的工具:

![](img/dd81d2297f610350adc61818878d6712.png)

LightHouse

# 开发应用和发展业务

我最喜欢的话题出现了..:)

![](img/0a467a20c907827e363e407a5d8fdea1.png)

Firebase

**Ben Galbraith** (开发者产品组产品管理总监)从 Firebase 控制台更新开始。

Firebase 现在在控制台中提供了他们称之为“流视图”的实时视图。

![](img/7689dacd385049f2fcf81115ba8f2d83.png)

StreamView

**云功能:**去年期待已久的高要求功能。

您可以使用[云功能](https://firebase.google.com/docs/functions/)做的事情:

在节点生态系统上创建后端

![](img/10f9836376c9e6973e16da7cad5b9c66.png)

收听来自[实时数据库](https://firebase.google.com/docs/database/)的更新，并使用[云消息](https://firebase.google.com/docs/cloud-messaging/)发送通知:

![](img/c007d1e12fa864a04eadcd0a2290901f.png)

或者从上传到 [Firebase 存储器](https://firebase.google.com/docs/storage/)的图像创建缩略图:

![](img/60ae1c0c30b18eff391a86e6285a673d.png)

想知道 Firebase 云函数是怎么回事，你可以参考我的文章 [Firebase 云函数:触发你想要的一切](/@chintansoni/firebase-cloud-functions-part-1-introduction-to-triggers-all-you-ever-wanted-8008e50ecb70)

**Firebase Hosting +云功能:** [Firebase Hosting](https://firebase.google.com/docs/hosting/) ，现在，可以与云功能对话，使您能够创建完全动态的网站。

![](img/d8c99c5bf763aeb97e7532df0aea7c18.png)

**Firebase 性能**:我们都想知道我们的应用程序缺少什么，这就是 Firebase 为我们提供的。

![](img/1be707cfef5ede814ddfea99e0b9b148.png)

[Firebase 性能监控](https://firebase.google.com/docs/perf-mon/)显示您的应用从启动到网络延迟的运行情况

![](img/a01af615ae51f66099d2fade13dfb30a.png)

最后， [Jason Titus](https://medium.com/u/9e48b3a86103?source=post_page-----c108ec93cf33--------------------------------) 宣布了开发者挑战赛[行动挑战赛](https://developers.google.com/actions/challenge/)以鼓励开发者更多地使用 Google Assistant 平台，他们也有相当不错的奖品。

感谢所有的演讲者让这次会议变得精彩。我们学到了很多，也享受了很多。

希望你的读者喜欢读这篇文章..！！！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)