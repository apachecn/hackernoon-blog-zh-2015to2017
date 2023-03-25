# 从 ExNavigation 迁移到 React Navigation

> 原文：<https://medium.com/hackernoon/migrate-from-exnavigation-to-react-navigation-1af661ec5082>

![](img/326648f29a98431e9b92e7a78931cc73.png)

我用 ExNavigation 已经有一段时间了。这很容易设置，并一直为我工作。如果你在过去的几个月里查阅了自述文件，你可能会注意到它处于维护模式——这意味着它不会被更新(除非必要)，因为所有未来的资源都将对导航做出反应。此外，在最近的 React Native 版本中，您可能已经注意到弹出的警告，即*navigation experiment*已被否决(ExNavigation 是基于它构建的)，所以现在是开始迁移的好时机。

那么什么是 React 导航呢？这是一个路由解决方案，它基于从 React Native 的许多其他导航解决方案中获得的知识和经验。我不会在这里详细介绍，但如果你感兴趣，我有一个关于 React 导航的[入门指南。](https://hackernoon.com/getting-started-with-react-navigation-the-navigation-solution-for-react-native-ea3f4bd786a4)

今天，我想带大家了解一下从 ExNavigation 迁移到 React Navigation 的过程。到目前为止，我发现这是一个简单的过程，并且大大简化了我的代码。

完整的源代码可以在 [Github](https://github.com/spencercarli/ex-navigation-to-react-navigation) 上获得。在自述文件中，我有每个步骤的差异链接。如果你想一步一步地了解如何迁移，请观看视频，否则我将链接到源代码并简要描述我正在做的事情。

## 警报

虽然`this.props.navigator.showLocalAlert`的用法可能不经常/不被很多人使用，但它是我经常使用的，并且发现在进行迁移时会很快失效，所以它是我要替换的第一个东西。

为了做到这一点，我使用了`react-native-dropdownalert`包，然后[制作了一个高阶组件](https://github.com/spencercarli/ex-navigation-to-react-navigation/tree/replace-alert/app/components/Alert)，以使下拉方法在我的应用程序中任何需要的地方都可用([原始问题，代码为](https://github.com/testshallpass/react-native-dropdownalert/issues/30))。

我不会进入创建特设的细节——如果你想走这条路，可以复制并粘贴到你的应用程序中，只需注意`Alert`组件，安装`hoist-non-react-statics`和`react-native-dropdownalert`到你的项目中，如果你复制它。

完成后，我用 `[AlertProvider](https://github.com/spencercarli/ex-navigation-to-react-navigation/blob/9faf79529c9c36e0dfea976be30241042bf5c5fa/app/index.js#L11)`和[连接我想调用和提醒的任何组件](https://github.com/spencercarli/ex-navigation-to-react-navigation/blob/9faf79529c9c36e0dfea976be30241042bf5c5fa/app/screens/Screen1.js)。

[变化差异](https://github.com/spencercarli/ex-navigation-to-react-navigation/compare/replace-alert)

## 堆栈导航到堆栈导航

在 ExNavigation 中，StackNavigation 组件是大部分应用程序的核心。在 React 导航中，您将为此使用 StackNavigator。

更新非常简单。首先，您需要为您的应用程序创建适当的 StackNavigators，然后在之前使用 StackNavigation 组件的地方使用这些新的 StackNavigators。如果您仅仅停止将 StackNavigation 替换为 StackNavigator，您将会失去 ExNavigation 内置的一些功能，但是如果您一次完成所有操作(我想您会这样做)，您将不会有问题。

在您的所有屏幕上，您需要更换

```
static route = {
  navigationBar: {
    title: '...'
  }
}
```

随着

```
static navigationOptions = {
  title: '...'
}
```

以及您的应用程序需要的任何其他导航选项。您可能想要查看新名称的[堆栈导航文档](https://reactnavigation.org/docs/navigators/stack#Screen-Navigation-Options)。

最后，你会想要用`this.props.navigation.navigate('screenName');`替换任何一个`this.props.navigator.push('screenName');`。对我来说，这就像查找和替换一样简单。`navigate`将默认为推送型动画(在 iOS 上从右到左，在 Android 上从底部渐变)。

如果你想给新屏幕传递道具，语法是`this.props.navigation.navigate('screenName', { name: 'Spencer' });`

[变化差异](https://github.com/spencercarli/ex-navigation-to-react-navigation/compare/StackNavigation-to-StackNavigator?expand=1)

## 选项卡导航到选项卡导航器

我喜欢 TabNavigator 在 React Navigation 中的工作方式——它允许我编写更少的代码，并且更好地匹配应用程序运行的平台。

我所要做的就是[创建一个新的 TabNavigator，它的标签](https://github.com/spencercarli/ex-navigation-to-react-navigation/blob/82e90cdc0db42b310b7ff9fd8226b01745b8771b/app/config/routes.js#L55)与我当前的 TabNavigation 相同，并使用我们之前设置的 StackNavigator 作为屏幕。然后，我用新的 TabNavigator 替换我的应用程序入口点中的`Tabs`组件，一切就绪。

最后要做的事情是删除你写的所有代码，让 TabNavigation 工作。删除代码同时保持相同的功能是一件非常令人满意的事情。

[变化之差](https://github.com/spencercarli/ex-navigation-to-react-navigation/compare/StackNavigation-to-StackNavigator...TabNavigation-to-TabNavigator)

## DrawerNavigation 到 DrawerNavigator

用 DrawerNavigator 替换 DrawerNavigation 很像替换选项卡。

我要做的第一件事是创建一个新的 DrawerNavigator。[在本例中](https://github.com/spencercarli/ex-navigation-to-react-navigation/blob/9337a53e0cb0ad1077d47484492d52d6837c9a37/app/config/routes.js#L120)它的屏幕与我们之前设置的 TabNavigator 相同。

然后，我们可以继续在我们的应用程序入口点导入新的抽屉，一切都应该呈现良好。由于我们不再使用 ex-navigation，您可以[去掉](https://github.com/spencercarli/ex-navigation-to-react-navigation/compare/TabNavigation-to-TabNavigator...DrawerNavigation-to-DrawerNavigator#diff-2018087f584c4398b5c3a23fc0e5f9db) `[NavigationProvider](https://github.com/spencercarli/ex-navigation-to-react-navigation/compare/TabNavigation-to-TabNavigator...DrawerNavigation-to-DrawerNavigator#diff-2018087f584c4398b5c3a23fc0e5f9db)` [组件和我们拥有的虚拟路由器](https://github.com/spencercarli/ex-navigation-to-react-navigation/compare/TabNavigation-to-TabNavigator...DrawerNavigation-to-DrawerNavigator#diff-2018087f584c4398b5c3a23fc0e5f9db)。

你可能已经注意到导航栏中不再有打开抽屉的默认图标(尽管你可以滑动来打开它)，所以你会想要添加那个。

最后，您可以删除任何以前的抽屉相关的逻辑:)

[变化差异](https://github.com/spencercarli/ex-navigation-to-react-navigation/compare/TabNavigation-to-TabNavigator...DrawerNavigation-to-DrawerNavigator)

## 结论

这不是决定性的，但在转换一些应用程序时，这些是我发现自己在做的事情。将应用从 ExNavigation 转换为 React Navigation 并不复杂，因为 API 非常相似。

> 对 React Native 的更多提示和技巧感兴趣吗？[注册我的电子邮件列表](http://www.handlebarlabs.com/react-native-mailing/)，我会与你分享它们！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)