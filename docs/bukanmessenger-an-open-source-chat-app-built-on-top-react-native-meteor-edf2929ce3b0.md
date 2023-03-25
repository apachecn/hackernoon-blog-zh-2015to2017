# “BukanMessenger”是一款基于 React Native 和 Meteor 开发的开源聊天应用

> 原文：<https://medium.com/hackernoon/bukanmessenger-an-open-source-chat-app-built-on-top-react-native-meteor-edf2929ce3b0>

![](img/571fd1e0e8077b9c1918d56ca66bb7f0.png)

# tldr

BukanMessenger 是一款建立在 React Native 和 Meteor Js 之上的 Messenger 应用。它是可定制的，每周都会有新的功能推出。

![](img/6ab1c1c32a6ff71d32fe1c8d426cf2e5.png)

BukanMessenger Demo

当电报在我国被正式禁止时，我真的很失望和难过。为什么这么好的[技术](https://hackernoon.com/tagged/technology)就因为一件不合理的事情就要被禁止？

所以我决定做一个类似 telegram 的东西，我觉得一定是开源的！虽然我相信它离电报还很远。但是 ofc 我们或者你可以在未来把它做得越来越好！！Btw，叫“BukanMessenger”。

我希望有了“BukanMessenger”，人们可以很容易地建立自己的信使。这是可以实现的，因为它建立在 React Native 之上，我认为 React Native 比原生 iOS (obj C)或 Android (JAVA)更容易学习。

# 为什么是 BukanMessenger？

*   它的反应原生，所以它默认支持 android 和 iOS！
*   简单的代码，所以你可以很容易地对它做出贡献。
*   Meteor 是最受欢迎的 javascript 框架之一，它非常容易使用

# 先决条件

*   确保你已经在你的系统中安装了 NodeJs【https://nodejs.org/en/ 
*   和 ofc 反应本土[https://facebook.github.io/react-native/](https://facebook.github.io/react-native/)
*   别忘了流星 Js 作为后端[https://www.meteor.com/](https://www.meteor.com/)

# 安装和配置

反应原生[https://github.com/radiegtya/BukanMessenger](https://github.com/radiegtya/BukanMessenger)(前端)

```
$ git clone https://github.com/radiegtya/BukanMessenger
$ cd BukanMessenger
$ npm install
$ react-native run-ios #or react-native run-android
```

流星[https://github.com/radiegtya/BukanMessengerBackend](https://github.com/radiegtya/BukanMessengerBackend)(后端)

```
$ git clone https://github.com/radiegtya/BukanMessengerBackend
$ cd BukanMessengerBackend
$ meteor npm install
$ meteor #run your meteor on [http://localhost:3000](http://localhost:3000)
```

打开“app/Config.js”，将 meteorUri 变量设置为您的 Meteor 服务器运行于

```
export const meteorUri = ‘ws://localhost:3000/websocket’;
```

# 支持我们:)

*   明星[https://github.com/radiegtya/BukanMessenger](https://github.com/radiegtya/BukanMessenger)储备库⭐️
*   我们已准备好为您/您的公司提供使用/修改 BukanMessenger 的培训。
*   购买我们关于 BukanMessenger 基本发展的截屏(即将推出)
*   比如我们的 facebook 页面[https://www.facebook.com/ega.sdr](https://www.facebook.com/ega.sdr)

我希望这篇博客对你有所帮助，无论是定制“BukanMessenger”还是仅仅了解聊天软件是如何工作的。如果你喜欢这个博客或想讨论更多，请留下评论或直接联系我:

*   WA/电报:+6285641278479
*   电子邮件:[radiegtya@yahoo.co.id](mailto:radiegtya@yahoo.co.id)

顶部见…