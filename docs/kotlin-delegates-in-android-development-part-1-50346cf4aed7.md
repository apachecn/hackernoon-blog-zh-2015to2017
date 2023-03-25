# Android 开发中的 Kotlin 代表—第 1 部分

> 原文：<https://medium.com/hackernoon/kotlin-delegates-in-android-development-part-1-50346cf4aed7>

![](img/8f066cf27964961f1d4dbe474c804034.png)

## 惰性和映射标准委托和 SharedPreferences 自定义委托

使用 Kotlin 的一些特性很容易写出干净易读的代码。由于有了数据类、属性、扩展函数和委托，Kotlin 类通常比同等的 Java 类更小，更容易阅读。在这篇文章中，我们将看到如何使用 Kotlin 委托属性来简化 Android 代码。如果你不熟悉这个主题你可以看看[官方文档](https://kotlinlang.org/docs/reference/delegated-properties.html)或者去[这个帖子](https://proandroiddev.com/delegation-in-kotlin-e1efb849641)。

## 懒惰代表

Kotlin 标准库包含许多有用的委托，例如 [lazy](https://kotlinlang.org/docs/reference/delegated-properties.html#lazy) 函数可以用来创建一个只有在第一次使用时才初始化的属性。此委托有助于轻松初始化使用 Dagger 管理的属性:

```
private val navigationController by *lazy* **{** (*applicationContext* as GithubApp).component.navigationController() 
**}**
```

以标准方式使用 Dagger 不需要类似的东西，因为属性将通过调用组件上的`inject`方法来填充。在一个[演示项目](https://github.com/fabioCollini/ArchitectureComponentsDemo/blob/master/app/src/main/java/it/codingjam/github/MainActivity.kt)中，你可以在 [GitHub](https://github.com/fabioCollini/ArchitectureComponentsDemo) 上找到我正在尝试以一种简化的方式使用 Dagger，活动和片段依赖是从 Dagger 组件中手动检索的。

前一个例子的代码可以简化为定义两个扩展属性来从一个`Context`或一个`Fragment`中检索 Dagger 组件:

```
val Context.*component*: AppComponent
    get() = (*applicationContext* as GithubApp).component

val Fragment.*component*: AppComponent
    get() = *activity*.*component*
```

现在可以用一种简单的方式定义该属性:

```
private val navigationController by *lazy* **{** *component*.navigationController() 
**}**
```

如果你的类只包含几个注入的字段，你可以使用一个惰性委托来定义它们，你不需要定义和调用`inject`方法，属性不需要任何注释(所以不涉及注释处理),它可以被声明为私有的，不需要`lateinit`关键字。

## 映射代理

Kotlin 标准库中定义的另一个委托是 [map 委托](https://kotlinlang.org/docs/reference/delegated-properties.html#storing-properties-in-a-map)。使用它，可以以静态方式使用键值映射。有时，我们需要使用一组预定义的键来管理地图，例如使用 Firebase Cloud Messaging，服务器发送的参数在地图中可用:

```
class MyMessagingService : FirebaseMessagingService() {

    override fun onMessageReceived(message: RemoteMessage?) {
        super.onMessageReceived(message)
        val data = (message?.*data* ?: *emptyMap*()).*withDefault* { "" }
val title = data["title"]
        val content = data["content"]

        *print*("$title $content")
    }
}
```

使用定义为字符串的键容易出错，我知道你可以在某个地方的常量中定义它们，但是定义静态的 final 字段(或者 Kotlin 对象中的某个东西)是 Java 1.4 的风格。我们可以使用一个具有两个属性的类来改进这段代码，这两个属性是使用 map 委托管理的:

```
class NotificationParams(val map: Map<String, String>) {
    val title: String by map
    val content: String by map
}
```

可以使用该类重写代码，如果字段中有拼写错误(对应于映射键)，它将不会编译:

```
override fun onMessageReceived(message: RemoteMessage?) {
    super.onMessageReceived(message)
    val data = (message?.*data* ?: *emptyMap*()).*withDefault* **{** "" **}

**    val params = NotificationParams(data)

    *print*("${params.title} ${params.content}")
}
```

## 共享偏好

在 GitHub [上有](https://github.com/MarcinMoskala/PreferenceHolder) [已经有](https://github.com/MarcinMoskala/KotlinPreferences)[许多](https://github.com/yongjhih/kotlin-extensions) [库](https://github.com/takuji31/Koreference)可以用来简化 Kotlin 中 SharedPreferences 的用法。让我们来看看自定义委托如何对编写保存在共享首选项中的属性非常有用。首先，让我们编写一个定义委托的`SharedPreferences`类的扩展函数:

```
fun SharedPreferences.int(
    defaultValue: Int = 0,
    key: String? = null
): ReadWriteProperty<Any, Int> {
  return object : ReadWriteProperty<Any, Int> {
      override fun getValue(thisRef: Any, property: KProperty<*>) =
          getInt(key ?: property.name, defaultValue)

      override fun setValue(thisRef: Any, property: KProperty<*>, 
              value: Int) =
          edit().putInt(key ?: property.name, value).apply()
  }
}
```

如果您不熟悉 Kotlin 结构(即使您熟悉它们)，这段代码也不容易理解。它定义了一个新的扩展方法`int`，可以在一个`SharedPreferences`对象上调用，这个方法返回一个`ReadWriteProperty`，定义如何读写属性。有两个可选参数:默认值和用于在共享首选项中存储该值的键(如果没有提供键，则使用属性名)。

使用这种方法，我们可以定义一个带有与共享首选项关联的字段的类:

```
class MyClass(prefs: SharedPreferences) {
    var count by prefs.*int*()
}
```

每次调用该属性时，都会从共享首选项中读取(或写入)值。

我们可以为其他类型定义类似的方法，为了避免复制和粘贴，我们可以使用一个泛型方法(我知道，这比前面的定义可读性更差):

```
private inline fun <T> SharedPreferences.delegate(
    defaultValue: T, 
    key: String?,
    crossinline getter: SharedPreferences.(String, T) -> T,
    crossinline setter: Editor.(String, T) -> Editor
): ReadWriteProperty<Any, T> {
  return object : ReadWriteProperty<Any, T> {
    override fun getValue(thisRef: Any, property: KProperty<*>) =
        getter(key ?: property.name, defaultValue)

    override fun setValue(thisRef: Any, property: KProperty<*>, 
            value: T) =
        edit().setter(key ?: property.name, value).apply()
    }
}
```

附加参数是两个函数(都是扩展函数，以简化调用`delegate`函数时提供它们的方式):

*   从`SharedPreferences`对象中读取值的 getter
*   将值写入`Editor`的设置器

这个函数被定义为`inline`以避免运行时开销，使用这个关键字`getter`和`setter`参数不被翻译成字节码中的类(更多信息可在[官方文档](https://kotlinlang.org/docs/reference/inline-functions.html)中获得)。

现在，我们可以轻松地为所有可以在共享首选项中编写的类型编写方法(在 Kotlin 中，泛型也可以用于基本类型，这一点非常有用):

```
fun SharedPreferences.int(def: Int = 0, key: String? = null) =
   *delegate*(def, key, SharedPreferences::getInt, Editor::putInt)

fun SharedPreferences.long(def: Long = 0, key: String? = null) =
   *delegate*(def, key, SharedPreferences::getLong, Editor::putLong)//...
```

这些委托可用于编写一个存储令牌的类，并计算令牌保存的次数(不是太有用，只是一个例子):

```
class TokenHolder(prefs: SharedPreferences) {
    var token by prefs.*string*()
        private set

    var count by prefs.*int*()
        private set

    fun saveToken(newToken: String) {
        token = newToken
        count++
    }
}
```

当`count++`被调用时，我们从共享的首选项中读取值，并保存增加 1 的值，就像这样(但是以可读和紧凑的形式！):

```
prefs.edit().putInt("count", prefs.getInt("count", 0) + 1).apply()
```

`SharedPreferences`是一个 Android SDK 类，使用这个委托我们在许多类中使用它，其中一些可能是应用程序的业务逻辑类。许多开发人员喜欢保持这些类与 Android 无关，以便使用 JVM 测试来测试它们。使用这个委托，我们的类在 JVM 上是可测试的，即使我们使用的是 Android SDK 类。我们可以使用一个`SharedPreferences`假实现编写一个 JVM 测试，该实现使用一个映射来存储值:

```
@Test fun shouldCount() {
    val prefs = FakeSharedPreferences()
    val tokenHolder = TokenHolder(prefs)

    tokenHolder.saveToken("a")
    tokenHolder.saveToken("b")

    assertThat(tokenHolder.count).isEqualTo(2)
    assertThat(prefs.getInt("count", 0)).isEqualTo(2)
}
```

你可以在这里找到`FakeSharedPreferences` [的 Kotlin 版本](https://github.com/NaluLabs/prefs-delegates/blob/master/fake-prefs/src/main/java/com/nalulabs/prefs/fake/FakeSharedPreferences.kt)和[日历库](https://github.com/android/platform_packages_apps_calendar/blob/master/tests/src/com/android/calendar/FakeSharedPreferences.java)的原 Java 版本(59 Vs 166 行代码！).

编辑:我基于这篇文章的例子创建了一个小库来使用 Kotlin 代理共享偏好，你可以在 GitHub 找到它:[github.com/NaluLabs/prefs-delegates](https://github.com/NaluLabs/prefs-delegates)

这就是这篇文章的第一部分，在第二部分我们将看到如何使用 Kotlin 委托来简化架构组件的使用(你可以在 GitHub 上预览这个[演示项目](https://github.com/fabioCollini/ArchitectureComponentsDemo))。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)