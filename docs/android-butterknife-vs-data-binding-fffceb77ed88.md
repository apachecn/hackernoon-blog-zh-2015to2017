# Android Butterknife vs 数据绑定

> 原文：<https://medium.com/hackernoon/android-butterknife-vs-data-binding-fffceb77ed88>

我们来对比一下 [Butterknife](https://hackernoon.com/tagged/butterknife) 和数据绑定库，2017 年哪个更先进？如果你不熟悉这两个，简单来说:它们让你的布局建设体验更好。在比较它们时，我将从设置开始，涵盖每个基本方面，这样您将学会如何使用它们！

# 设置

## 黄油刀

对于 Butterknife，您需要将这些依赖项添加到 build.gradle

> **编译“com . jakewharton:butter knife:8 . 6 . 0”**
> 
> **annotation processor ' com . jakewharton:butter knife-compiler:8 . 6 . 0 '**

## 数据绑定

在这里，您只需将它们添加到 build.gradle 中的 android 范围内

> **安卓{**
> 
> **…**
> 
> **数据绑定{**
> 
> **启用真**
> 
> **}**
> 
> **}**

然后用 *layout* 标签包装所有的布局，这样它在任何地方都是根标签

> **<布局>**
> 
> **<线性布局>…</线性布局>**
> 
> **</布局>**

# 查找视图

# 老派

在普通 Android 框架中，使用这种方法可以找到布局中的视图

> **findViewById(r . id . textview)；**

然后你把它转换成正确的类型，把它保存为局部变量或者保存到字段中，这对于大的布局来说确实是大量的工作

## 黄油刀

使用 Butterknife，您可以用这个注释来声明字段

> **公共类 MyActivity{**
> 
> ***@ bind view*(r . id . TextView)TextView 文本；**
> 
> **…**
> 
> **}**

并以这种方式将每个视图声明为字段，不需要 *findViewById* 方法。现在，如果您将这种方法与以前的方法进行比较，如果您将视图保存为字段，您可以节省 50%的代码行。少了两行。

但是，如果你有 20 个视图，你会在类的顶部有 20 个难看的字段，这只是视图，变量呢？

## 数据绑定

现在这是下一个层次，这里你需要做一些改变。用此替换默认的 setContentView

> ***@覆盖***
> 
> **受保护的 void onCreate(Bundle saved instancestate){**
> 
> **ActivityMainBinding binding = databindingutil . set content view(this，r . layout . activity _ main)；**
> 
> **…**
> 
> **}**

正如你看到的，有一些 ActivityMainBinding 类——这个类是为你的布局生成的，它基于转换为 camel case 的布局名称，在末尾添加绑定。当你用*布局*标签包装你的布局时，就会发生这种情况。如果您有一个名为 layout.xml 的布局，您将生成 LayoutBinding 类

现在，要访问您刚刚使用的任何视图

> **binding.textView**

如果你已经意识到了数据绑定的优势，那就是你最多只有两行代码。如果需要，您只需将绑定变量保存为字段，就可以访问该类中任何地方的任何字段

## 结论

假设我们的布局中有 20 个视图，我们希望它们可以被一个类中的任何方法访问，所以我们需要将它们作为字段。

使用 findViewById，您将有 40 行代码，Butterknife 有 20 行，DataBinding 有 2 行。两行！数据绑定在这里是明显的赢家

# Butterknife 还能做什么

如果你调查一下[butter knife](https://github.com/JakeWharton/butterknife)doc——基本上没有更多的。OnClick 注释甚至不需要任何库，因为您可以使用 *android:onClick* 属性在 XML 中查看视图

有些人使用的资源绑定，在数据绑定中没有。基本上就是这样

# 数据绑定

对于数据绑定来说，这只是一个开始。您可以将您的逻辑移动到 XML 文件中，这是构建布局时更自然的方法

假设您有一个包含 20 个字段的电影模型，您希望在布局中显示所有字段。通常你会为每个电影字段调用 Java 中的 *setText* 和其他方法。使用数据绑定，您可以做到这一点

> **<布局>**
> 
> **<数据>**
> 
> **变量**
> 
> **name="movie"**
> 
> **type = " com . unified waves . movie "/>**
> 
> **</数据>**
> 
> **<文本视图**
> 
> **…**
> 
> **Android:text = " @ { movie . name } "/>**
> 
> **</拉尤特>**

您可以在布局的顶部添加变量，然后就可以在那里设置视图。Name 是 Movie 类中的变量名。

为了将一个对象传递给你的布局，你在 Java 中调用这个方法，它是为你的每个变量生成的

> **binding.setMovie(新电影(…))；**

这并不是说这种方法节省了一些代码行，而是因为它更自然，在构建布局、编写 Java 时，您不需要一次又一次地从 Java 到 XML 来回切换。你只需要写布局，完成后，进入 Java。简单。

# 绑定适配器

还有更多数据绑定可以提供，这是我想提到的最后一件事——自定义属性。通过使用绑定适配器，您可以为任何视图创建自定义属性，而无需实际扩展这些视图

我最喜欢的一个——*imageUrl*。有多少次你希望 Android 框架有这个属性，你只需把图片 url 传递给 ImageView，它就加载它？嗯，当你刚刚开始的时候，你可能会这样做。

看看我们能做什么

> ***@ binding adapter*(“app:imageUrl”)**
> 
> **公共静态 void imageUrl(ImageView v，String url){**
> 
> **//Glide.with()…**
> 
> **}**
> 
> **<布局>**
> 
> **<数据>**
> 
> **变量**
> 
> **name="movie"**
> 
> **type = " com . myhexaville . movie "/>**
> 
> **</数据>**
> 
> **< ImageView**
> 
> **…**
> 
> **app:imageUrl = " @ { movie . imageUrl } "/>**
> 
> **< /layouyt >**

imageUrl 是电影模型的字符串字段变量。我们刚刚为 ImageView 声明了一个自定义属性。一旦您使用*binding . set movie(movie)*将电影对象传递到您的布局，您的 ImageView 将使用 Glide 从电影对象加载图像 url

至于那个静态方法——你可以把它放在你的项目中任何你想放的地方。几乎在任何地方，它都会工作

# 结论

在 2015 年我开始学习 Android 后不久，数据绑定就出现了，所以我用了几次 Butterknife，当我看到数据绑定时，我意识到——这是下一个级别。看看简单的访问视图。

顺便说一句，如果你使用 kot Lin——有访问视图的 Android 扩展，就像数据绑定一样工作，但是开销更少

感谢阅读！