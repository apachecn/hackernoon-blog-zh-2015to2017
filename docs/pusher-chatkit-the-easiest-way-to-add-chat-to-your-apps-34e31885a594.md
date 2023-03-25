# Pusher Chatkit，将聊天添加到您的应用程序的最简单方式

> 原文：<https://medium.com/hackernoon/pusher-chatkit-the-easiest-way-to-add-chat-to-your-apps-34e31885a594>

![](img/d265e84c227efd638519fe0a5ab71b9d.png)

我们的全新产品 [Pusher Chatkit](https://pusher.com/chatkit) 现已推出公测版🎉！

无论您是希望在游戏中添加玩家对玩家的聊天、实时聊天支持，还是协作者群组聊天，您都可以使用 Chatkit 在创纪录的时间内完成。它附带了多种功能，使在您的应用程序中建立聊天变得轻而易举:

*   打字指示器
*   在线状态
*   消息历史
*   聊天室管理
*   精细的用户角色和权限
*   公共和私人房间
*   查询 [API](https://hackernoon.com/tagged/api) 来访问存储的消息

今天，我们只为 [iOS](https://hackernoon.com/tagged/ios) (Swift)提供一个 Chatkit SDK，但是我们的 Android (Kotlin)和 Javascript SDKs 都可以在预览版中获得(这里先睹为快[这里](https://github.com/pusher/chatkit-android)和[这里](https://github.com/pusher/chatkit-client-js))，应该很快就会加入我们的 Swift SDK 公测版。

# 但是，为什么呢？

正如我们所说的，数以千计的开发人员正在将聊天功能添加到他们的应用程序中，但是我们知道，即使在今天，从头开始构建聊天功能也不像想象中那么容易。我们将我们所学的一切用于帮助 GoGuardian、YouNow、Slaask、FreshDesk 和超过 7000 名使用我们现有 API 的其他开发人员将聊天功能添加到他们的应用程序中。

Chatkit 是我们成为多产品公司的第一步。我们的目标是继续为开发人员提供构建模块，使他们更有生产力，并使他们能够专注于对他们和他们的公司重要的事情——构建伟大的产品。我们热衷于为开发者解决问题，所以请密切关注即将发布的新产品

感谢创建 Chatkit 的团队:[卢卡·布拉托斯](https://twitter.com/lukabratos)、[汉密尔顿·查普曼](https://twitter.com/hamchapman)、[查理·科克伦](https://github.com/Charlesworth)和[维文·库马尔](https://twitter.com/vivangkumar)💪。

# 给我看一些代码

以下是如何将 Chatkit 集成到您的 iOS (Swift)应用程序中的示例。

```
// 1\. Connecting a client to the Chatkit servers
    let chatManager = ChatManager(
      instanceId: "your-chatkit-instance-id"
      tokenProvider: yourTokenProvider
    )

    chatManager.connect(delegate: yourDelegate) { currentUser, error in
      guard error == nil else {
        print("Error connecting: \(error.localizedDescription)")
        return
      }
      print("Successfully connected")
    }

    // 2\. Creating a room
    currentUser.createRoom(name: "my room name") { room, error in
      guard error == nil else {
        print("Error creating room: \(error.localizedDescription)")
        return
      }
      print("Created room called \(room.name)")
    }

    //  3\. Sending a message
    currentUser.addMessage(text: "Hey guys! ", to: myRoom) { message, error in
      guard error == nil else {
        print("Error adding message to \(room.name): \(error.localizedDescription)")
        return
      }
      print("Added message to \(myRoom.name)")
    }

    // 4\. Receiving messages
    func newMessage(message: PCMessage) {
      print("Received message: \(message.text) from \(message.sender.debugDescription)")
    }

    // 5\. you are done
```

现在就试试吧！ [**登录或创建您的账户**](https://dash.pusher.com/?createInstance=chatkit&name=Chatkit) 并访问我们全新闪亮的✨仪表盘。

# 即将出现

我们已经收到了一些很好的反馈，我们的产品路线图还包含其他强大的功能，例如:

*   为您处理富媒体上传和存储
*   跟踪已读回执
*   当用户离线时触发本机推送通知
*   发布 Android (Kotlin)和 Javascript SDKs
*   为 Go、Python、PHP 发布服务器端 SDK
*   …还有更多更多的⚡️.