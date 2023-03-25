# 使用 Kotlin 实现 Android 编码的虫洞路线

> 原文：<https://medium.com/hackernoon/the-wormhole-route-to-android-coding-using-kotlin-33eea6626ea>

## 将您的 Android 开发置于 Nitro 模式

![](img/080b7349aa95294c0ad2e810496e56c4.png)

当 [**Kotlin**](http://kotlinlang.org/) 在[今年的 Google I/O](https://www.youtube.com/watch?v=X1RVYt2QKQE) 上被宣布为 Android 的一级语言时，我把深入研究这种语言作为我的首要任务。虽然我很久以前就从我的一个朋友那里听说过 Kotlin，他也是一个 Android 爱好者，但直到现在，我才真正为我的项目学习和使用这种语言。

科特林语作为一种语言是美丽的。它简洁、强大，学习曲线非常平坦。一旦你开始习惯这种语言，就没有回头路了。没有不尊重 Java 的意思，但是我想我不会再为 Android 使用 Java 了。

然而，一个工具的强大取决于你如何使用它。如果你不打算利用语言中隐藏的宝石，那么你最好不要使用它。由于我一直在探索这种语言，Kotlin 如何加快 Android 应用程序开发的一些方面引起了我的注意，我将与你分享它们，以便你可以在 6 之前从办公室乘坐虫洞*回家。*

# 1.应用 Android 扩展插件

你能做的第一件事是去掉所有那些难看的 **findViewById()** 调用，或者任何你用来把你的视图注入到你的活动或者片段或者其他视图的框架。

JetBrains 在[中捆绑了一个 Android 扩展插件](https://kotlinlang.org/docs/tutorials/android-plugin.html)和它的 Kotlin 插件，可以让你访问你的**活动**、**片段**或**视图**中的视图，就好像它只是一个预定义的属性一样。迷茫？等着看例子吧。

首先，您需要将这一行添加到应用程序的 build.gradle 中。

```
apply plugin: 'kotlin-android-extensions'
```

现在你的项目应该可以利用 Kotlin 的 Android 扩展插件了。

## 怎么用？

非常简单。没有什么需要设置的。不需要初始化。只需使用在 XML 布局中声明的 id 来引用活动或片段或视图中的视图。

所以，假设你已经用 XML 声明了你的视图，

```
<android.support.v7.widget.RecyclerView
    android:**id**="@+id/recyclerView"
    android:layout_width="match_parent"
    android:layout_height="wrap_content"/>
```

然后，您可以在您的活动或片段或视图中访问您的 RecyclerView，以这种方式，

```
recyclerView.adapter = adapter
```

就是这样。您不需要从 XML 中显式解析视图，也不需要做任何其他的事情。没有 findViewById()。没有黄油刀。您的视图已经可以在您的 Kotlin 代码中引用和使用了。

但是，如果您的 Kotlin 类不是活动、片段或视图，这将无法工作，因为该类没有任何附加的布局来遍历和定位您的视图。但这并不妨碍它施展魅力。如果您有一个展开的视图，那么您可以使用这种方法访问它的子视图，

```
view.textView.text = "Hello"
view.buttonSubmit.setOnClickListener {
    println("Button Clicked!")
}
```

这种方法在像访问 RecyclerView 的视图容器中的视图这样的情况下很有用，在这种情况下，您需要像这样访问您的视图，

```
**class** **ViewHolder**(itemView: View) : RecyclerView.ViewHolder(itemView) {
    **fun** bindData(newsModel: **NewsModel**) {
        with(newsModel) {
            itemView.heading_news.text = heading
        }
    }
}
```

注意，在上面的代码片段中，我们通过使用父元素 itemView 的引用来引用每个 row 元素中的视图。有了它，您可以绑定项目中的几乎所有视图，甚至不需要编写额外的代码来完成所有的仪式。findViewById()去年就是如此。

## 你需要知道的是

Android Studio 无缝地完成了导入类的所有繁重工作，因此您永远不用担心自己编写导入语句。然而，**如果 IDE 不能**做到这一点，您需要在任何使用扩展直接访问视图的地方添加这个 import 语句，

```
**import** **kotlinx**.android.synthetic.main.your-layout.*
```

对于后一种情况，即访问既不是活动也不是片段或视图的类中的视图，您需要添加一点点更多的信息，

```
**import** **kotlinx**.android.synthetic.main.your-layout.view.*
```

这应该足够完成这项工作了。

# 2.从安科那里获得帮助

JetBrains，Kotlin 背后的家伙发布了[一个名为**Anko**的库，这是另一个获得开发完成捷径的工具。它包括许多功能和方法，通过为我们在 Android 应用程序中做的一些非常常见的事情编写紧凑而美丽的代码，帮助您减少开发时间，同时获得快乐。](https://github.com/Kotlin/anko)

首先，您需要向 build.gradle 中添加一个依赖项，

```
dependencies {
    compile "org.jetbrains.anko:anko:0.10.1"
}
```

在您执行了 Gradle Sync 之后，您应该准备好在您的项目中利用 Anko 的强大功能。

Anko 由几个部分组成，涵盖了项目中的各种用例，

*   **Anko Commons** —用于启动*意图*，显示*对话框*等
*   **Anko 布局** ( [wiki](https://github.com/Kotlin/anko/wiki/Anko-Layouts) ) —编写没有任何 XML 的动态布局
*   **Anko SQLite**([wiki](https://github.com/Kotlin/anko/wiki/Anko-SQLite))—*Android 的 SQLite* 的简化用法
*   **Anko 协同程序** ( [wiki](https://github.com/Kotlin/anko/wiki/Anko-Coroutines) ) —科特林*协同程序*的助手

为了简洁起见，在本文中我将只讨论 Anko Commons 的用法。让我们深入研究一下。

Anko Commons 提供了更简单紧凑的代码来执行任何 Android 应用程序中最常见的功能。一些使用案例是，

## 发起一个意图

启动一个意图可能是 Android 应用程序中从一个屏幕导航到另一个屏幕最常见的方面。然而，这需要执行许多仪式，尤其是在将数据从一个活动传递到另一个活动时。

例如，如果我们想从 **MainActivity** 中启动一个 **UserDetailsActivity** ，并传递一些用户数据，使该活动成为一个[**singleTop**activity](https://inthecheesefactory.com/blog/understand-android-activity-launchmode/en)，那么就有很多东西要写，

```
**val** userActivity = Intent(**this**, UserDetailsActivity::**class**.**java**)
userActivity.putExtra("id", 1)
userActivity.putExtra("name", "Rahul")
userActivity.flags = Intent.FLAG_ACTIVITY_SINGLE_TOP
startActivity(userActivity)
```

这是一个简单任务的 5 行代码。有了 Anko，您可以将其归结为一行代码，就像这样，

```
startActivity(intentFor<UserDetailsActivity>("id" to 1, "name" to "Rahul").singleTop())
```

很简单。有意义。不会再伤害你的手指了。

## 显示信息

向应用程序的用户显示消息的两种最常见的方式是显示一个祝酒词或一个对话框。安科给他们俩注射了一些有益血清。

展示一个**祝酒词**就像，

```
*//Normal toast using a hardcoded string*
toast("Hi there!")
*//Normal toast using a string resource*
toast(R.string.welcome)
*//Long toast*
longToast("Wow, such a duration")
```

写完祝酒辞后，不要忘记调用 show()函数。

对话框也很简单，

```
alert(Appcompat, "Hi, I'm Rahul", "Do we know each other?") {
    yesButton { toast("Oh…") }
    noButton {}
}.show()
```

嗯，这不难写。注意，这里指定 Appcompat 是为了告诉 Anko 呈现对话框的 AppCompat 版本。如果您不希望这样，可以跳过该参数。

你可以在这里获得对 Anko Commons wiki 的全面概述。

## 需要知道的事情

由于 Anko 是由 Commons、Layouts、SQLite 和 Coroutines 等几个部分组成的总包，所以我们添加的普通包会显著增加应用程序的大小和功能数量。这是一个很好的做法，只包括必要的模块，省略其余的，以保持你的 APK 苗条。

您可以用这种方式添加独立的依赖项，无论您的项目需要哪个模块，

```
dependencies {
    *// Anko Commons*
    compile "org.jetbrains.anko:anko-commons:0.10.1" *// Anko Layouts*
    compile "org.jetbrains.anko:anko-sdk25:0.10.1"
    compile "org.jetbrains.anko:anko-appcompat-v7:0.10.1" *// Coroutine listeners for Anko Layouts*
    compile "org.jetbrains.anko:anko-sdk25-coroutines:0.10.1"
    compile "org.jetbrains.anko:anko-appcompat-v7-couroutines:0.10.1" *// Anko SQLite*
    compile "org.jetbrains.anko:anko-sqlite:0.10.1"
}
```

# 3.写扩展函数

Kotlin 最大的优点之一是能够编写扩展函数和属性。考虑到一些 Android API 是多么糟糕，这对所有 Android 应用程序开发者来说都是一个福音。如果我的话对您没有任何意义，请不要惊慌，您马上就会明白，但是在深入研究如何用 Kotlin 编写扩展函数之前，让我们先简单介绍一下它们是什么。

## 什么是扩展函数？

Kotlin 为开发人员提供了向项目或平台(*即 SDK* )类添加功能的能力，而无需以任何常规方式实际扩展或修改该类。

让我们用一个例子来说明，假设我们有一个这样的 Kotlin 类，

```
**class** **StandardClass** {
    **fun** doSomething() {
        *//Do something here*
        println("Maybe print something")
    }
}
```

我们现在可以给这个类注入一个函数，它的行为就像类中的一个常规函数，并且可以访问类中所有的**属性**和**函数**，甚至不用扩展它。怎么会？这是怎么回事。

```
**fun** StandardClass.doSomethingMore() {
    *//Do something more here*
    println("Maybe print something new and extra")
}
```

仅此而已，请注意，这个函数可以在任何地方定义，甚至可以作为**正则函数中的顶级函数。kt 文件**。定义这种扩展函数的语法或结构如下，

```
**fun** receiver-**class**-name.function-name() {
    *//Body of the function goes here*
}
```

## 这有什么帮助？

嗯，所有这些看起来都很花哨，但是当涉及到 Android 应用程序开发时，这些时髦的功能实际上在哪里派上用场呢？答案很简单。它是一个标准的实用程序类，提供了一个全新的维度，能够为平台 API 添加缺失的实用程序。

以这种情况为例。膨胀一个视图并将其添加到一个**视图组**是一项繁琐的任务。首先，你需要一个像这样的**充气机**，

```
val inflater = LayoutInflater.from(context)
```

然后，您必须展开布局并将其添加到父视图组，如下所示，

```
**inflater**.inflate(**R**.layout.view_layout, **parent**, **true**)
```

每次你需要展开一个布局时，你都必须这样做，这在 Android 项目中经常发生。那么我们如何让它变得更好呢？我们为它创建了一个扩展函数。

```
**fun** ViewGroup.inflate(layoutId: **Int**, attachToRoot: **Boolean** = false): View {
    **return** LayoutInflater.from(context).inflate(layoutId, **this**, attachToRoot)
}
```

现在，您可以通过对整个项目中的任何 ViewGroup 对象调用 inflate()函数来扩展任何布局，如下所示:

```
**view**.inflate(**R**.layout.view_layout)
```

在上面的函数定义中需要注意的一点是，我们没有将上下文作为参数，但是仍然能够解析和使用它。这是因为我们向类 ViewGroup 添加了一个扩展函数，现在我们可以访问函数块中的所有属性和函数。Context 是类中定义的属性之一，我们可以使用它，而不需要任何额外的努力来检索它。

还不信服？这是另一个改变你想法的例子，

```
**fun** SharedPreferences.save(function: **SharedPreferences**.**Editor**.() -> Unit) {
    **val** editor = edit()
    editor.function()
    editor.apply()
}
```

这个函数的作用是扩展 Android 中的 **SharedPreferences** 类，以便轻松地将数据保存在首选项中，而无需执行所有的仪式，例如获取 **SharedPreferences。编辑 preferences 对象中的**对象，并记住在输入所有数据后调用 **apply()** 或 **commit()** 。

使用这个函数非常简单，

```
**val** sharedPref = PreferenceManager.getDefaultSharedPreferences(applicationContext)
sharedPref.save {
    putString("name", "Rahul")
    putBoolean("isLoggedIn", true)
}
```

这只是将一些数据保存到应用程序的共享首选项中。

尽管最初可能需要一些时间来设置所有的扩展函数，但是一旦你完成了，你的生产力将会有一个飞跃，你将能够更快地编写代码。最好的事？您可以跨项目共享您的扩展功能，并根据需要重用它们。

有了扩展功能，你可以延长你做生活中其他事情的时间。

# 4.让数据类为您服务

科特林引入了 [**数据类**](https://kotlinlang.org/docs/reference/data-classes.html) 来简化模型类的创建和维护。仅仅为了创建一个模型类就需要编写大量样板代码的日子已经一去不复返了。

谷歌试图用 [**AutoValue**](https://github.com/google/auto/tree/master/value) 来解决这个问题。其他人试图通过为 IDE 创建插件来为您生成所有代码来解决这个问题，但是将这个特性内置在语言中是一种享受。没有要包含的内容。没有要安装的第三方插件。

## 有什么特别的？

Kotlin 数据类自动为您生成以下内容，让您的代码保持整洁，让您的手指放松

*   **equals()** 和 **hashCode()** 函数用于模型比较
*   格式化的 **toString()** 函数，以可读的方式输出内容
*   **componentN()** 用于访问类中属性的函数
*   [**copy()**](https://kotlinlang.org/docs/reference/data-classes.html#copying) 函数可以轻松地将属性从一个对象复制到另一个对象

所有这些都是在隐藏模式下生成的，这意味着在 Kotlin 代码中看不到这些函数，从而保持了数据类的简洁明了。

## 如何将类声明为数据类？

只需在你的类定义前添加关键字**数据**。这里没什么可做的了。例如，您可以像这样定义一个**用户模型**类，

```
**data** **class** **UserModel**(**val** name: String, **val** age: Int, **var** isOnline: Boolean)
```

是的，你可以在一行中定义一个完整的模型类。这再简单不过了。

最好的部分是，如果我们想向用户模型引入一个新的属性，只需更改几个字符。让我们添加一个属性来保存用户的出生日期。

```
**data** **class** **UserModel**(**val** name: String, **val** age: Int, **var** isOnline: Boolean, **val** dateOfBirth: Date)
```

就是这样。我刚刚在主构造函数定义中添加了一个新属性。科特林会处理剩下的。不再添加新的 getter 和 setter 或更新 toString()方法。

## 在常规 Android 应用中的使用

几乎每个 Android 应用程序都在一个模型中保存数据。无论是用户数据还是其他数据。考虑一个应用程序，它使用 [**改进**](http://square.github.io/retrofit/) 连接到后端 API，并将响应存储在这样定义的模型类中，

```
**data** **class** **UserApiResponse**(**@SerializedName("user_name")** **var**   userName: String,
                           **@SerializedName("age")** **var** age: Int)
```

认为模型一旦定义就不会改变是天真的。它可以而且将会改变(这里*不是试图通过应用墨菲定律看起来很酷*)。

无论您的应用程序是本地的还是连接到后端 API 来获取数据，您的本地数据库模式或 API 响应格式都非常容易发生变化。

当它确实改变时会发生什么？您沿着项目结构走一趟，找到模型类并进行必要的更改。

如果这是更新模型类的老派方法，您将不得不花费一两分钟或无聊的任务来添加字段，创建 getters 和 setters，并更新 toString()函数。

谢天谢地，我们不再做那种体力劳动了，记得吗？科特林会帮我们处理好一切。*节省的时间*。

Android 应用程序开发是一项耗时的工作。科特林的使命是让它成为一次愉快的经历，并在前进的道路上加速这一过程。确保你使用尽可能多的技巧来充分利用这种新语言。

下次再见，在另一个教程或常识讲座中。分享这篇文章，如果你喜欢它。

## 鼓掌。鼓掌。鼓掌。帮助我有动力为你写更多的东西。