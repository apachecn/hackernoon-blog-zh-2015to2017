# Android —如何使用“foreach”添加梯度依赖关系

> 原文：<https://medium.com/hackernoon/android-how-to-add-gradle-dependencies-using-foreach-c4cbcc070458>

![](img/83717e492a0d5776e155850721c7166a.png)

Android 中一个有几个模块的大项目也有几个需要配置的 Gradle 文件，这是一项单调乏味的任务。通常做的事情是给每个模块添加依赖关系，在这篇文章中，将解释如何使用一个单独的 Gradle 文件来控制所有模块的依赖关系。

我们的目标是转换这个文件

变成这个

## 依赖文件

为了完成这个转换，首先创建一个文件来控制所有的项目依赖项。

在根项目中创建一个名为 **dependencies.gradle** 的文件，并指定将要使用的所有库版本。

一旦完成，让我们创建依赖关系定义映射。为了使文件尽可能有组织，将定义三个映射:一个用于 Android 库，另一个用于第三方库，最后一个用于测试库。

文件应该是这样的

现在，每个模块都需要创建一个列表，分别包含所有需要的依赖项。

例如，定义了一个名为 app 的模块，它将使用 AppCompat、Dagger、RxJava 和 RxAndroid，如下所示。

列表中的每个对象都包含一个定义依赖关系的映射及其配置。在这个例子中，使用了“编译”和“apt”配置，但是还有更多诸如“提供”、“测试编译”、“Android 测试编译”等…

现在打开 app 模块的 gradle 文件，使用下面的代码。

## 这是如何工作的？(◔_◔)?

通常在 dependencies 块中添加一个依赖项如下所示。

```
compile 'com.google.code.gson:gson:2.7'
```

直接调用 DependencyHandle 对象添加方法。

让我们来看看 add 方法。

```
[**add**](https://docs.gradle.org/current/javadoc/org/gradle/api/artifacts/dsl/DependencyHandler.html#add%28java.lang.String,%20java.lang.Object%29)([**String**](https://docs.oracle.com/javase/6/docs/api/java/lang/String.html?is-external=true) configurationName, [**Object**](https://docs.oracle.com/javase/6/docs/api/java/lang/Object.html?is-external=true) dependencyNotation)Adds a dependency to the given configuration.
```

这个方法将配置名(compile，testCompile，androidTestCompile…)作为第一个参数，将依赖项本身作为第二个参数。

依赖性可以以常规方式定义为字符串变量。

```
'com.google.code.gson:gson:2.7'
```

如果使用最新版本(Android Studio 的方式)，它可能被定义为一个地图。

```
[group: ‘com.google.code.gson’, name: ‘gson’, version: '2.7']
```

回到代码…

正在迭代之前创建的 appDependencies 列表，每次迭代都会添加一个依赖项及其配置。要用 Groovy 表示迭代中的当前元素，请使用' **it'** 变量。

这个过程应该在每个模块的 **build.gradle** 文件中重复，这样任何时候需要一个新的依赖项，只要把它添加到依赖项文件中相应的列表中。这就是如何用一个文件来控制所有的依赖关系。注意，这个文件可以在其他 Android 项目中重用。

# 模块之间的依赖关系

这个过程可以应用于添加模块之间的依赖性。

为此，首先应该在 **dependencies.gradle** 中指定所有模块。比如 app，数据，领域模块。

如果应用程序模块将使用域模块，则应按以下方式添加依赖关系:

## 选择

如果需要任何额外的选项，比如排除模块或使用 transitive，通常的做法是:

```
androidTestCompile ('com.android.support.test:runner:0.5') {
    exclude module: 'support-annotations'
}
```

要做的第一件事是在依赖项中添加一个额外的键值来表示选项。让我们看一个例子:

现在，需要修改应用程序模块内部的 build.gradle 文件，并更改 add 方法，以便添加一个闭包作为额外的参数来添加选项。

```
[**add**](https://docs.gradle.org/current/javadoc/org/gradle/api/artifacts/dsl/DependencyHandler.html#add%28java.lang.String,%20java.lang.Object,%20groovy.lang.Closure%29)([**String**](https://docs.oracle.com/javase/6/docs/api/java/lang/String.html?is-external=true) configurationName, [**Object**](https://docs.oracle.com/javase/6/docs/api/java/lang/Object.html?is-external=true) dependencyNotation, [**Closure**](http://docs.groovy-lang.org/docs/groovy-2.4.7/html/gapi/groovy/lang/Closure.html?is-external=true) configureClosure)Adds a dependency to the given configuration, and configures the dependency using the given closure.
```

build.gradle 文件应该如下所示:

## 插件

通过这个过程，也可以管理我们在每个模块中使用的插件，并从 dependencies.gradle 文件中控制所有插件，让我们来看一个例子，其中这个文件

可以用这个代替

只需在 **dependencies.gradle** 文件中创建一个插件列表，这些插件将被模块使用。

## 调用 dependencies.gradle

所有这些过程的工作方式是从位于根项目中的 build.gradle 文件调用 dependencies.gradle 文件。

```
apply from: 'dependencies.gradle'
```

从 GitHub 库中查看这个[示例](https://github.com/fabiomsr/centralize-dependencies-gradle)。

## 不足之处

这个过程中并不是一切都是美好的，这种添加管理依赖项的方式使得 Android Studio 无法通知库何时过期。

为了解决这个问题，您可以混合使用添加和管理依赖项的两种方法，并使用常规方法来管理 Android 库，以便在任何库过期时通知它。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

[![](img/be0ca55ba73a573dce11effb2ee80d56.png)](https://goo.gl/Ahtev1)