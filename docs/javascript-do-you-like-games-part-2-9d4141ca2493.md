# JavaScript:你喜欢游戏吗？(第二部分)

> 原文：<https://medium.com/hackernoon/javascript-do-you-like-games-part-2-9d4141ca2493>

关于这个话题:[第 1 部分](https://blog.hellojs.org/javascript-do-you-like-games-part-1-c1320c2c52ec) |第 2 部分| [第 3 部分](https://blog.hellojs.org/javascript-do-you-like-games-part-3-6225ec264902) | [第 4 部分](/@radu.bogdan.gaspar/javascript-do-you-like-games-part-4-9d6e8adb5626) | [第 5 部分](/@radu.bogdan.gaspar/javascript-do-you-like-games-part-5-a79bb69ad9e1) | [第 6 部分](/@radu.bogdan.gaspar/javascript-do-you-like-games-part-6-7bff0b8ce05e)

![](img/e0ca8667cb4037a29bca5fc0730ddfe4.png)

是时候开始研究游戏的核心组件[引擎](https://hackernoon.com/tagged/engine)了。在本文结束之前，我们应该可以在 ***画布*** 上渲染一些东西！

> 注意:请注意，我将在 [GIT 库](https://github.com/raduGaspar/game-physx)历史树中添加特定代码片段的链接。这将使您更容易运行我在描述特定功能时所运行的代码。

我们将从没有依赖关系的类开始工作，比如: ***Game，AssetLoader*** 和***event dispatcher***然后进展到其他可能扩展这些的类。

**游戏*游戏*游戏**类游戏:

*   接受一个 ***场景*** 作为可选的构造函数参数
*   保留对所有已创建场景的引用
*   拥有添加和移除场景的公共方法
*   有一个公共的 ***loop()*** 方法，该方法将在所有存储的场景上触发 ***update()*** 方法
*   拥有暂停和恢复循环的公共方法；我们先把这些叫做: ***【播放()】******【暂停()******toggle pause()***

engine/Game.js

这里需要做一些改进，比如检查添加的场景是否是真实的**场景**实例，添加对没有 requestAnimationFrame 的浏览器的支持，可能会实现一个 flux 架构来存储数据并防止状态突变。

flux 架构可能会在晚些时候出现，但目前我们将该引擎视为 P.O.C .(概念验证)…这意味着我也不会费心添加对旧浏览器的支持，但如果你需要，你肯定可以使用 [rAF polyfill](https://gist.github.com/paulirish/1579671) 。

**AssetLoader** 类可以:

*   成为单例，这样我们就可以从任何其他类中检索资产
*   公开一个 load 方法，该方法将接收一组指向本地资产的路径；该方法应该返回一个承诺，这样我们就可以检测所有资产何时被加载
*   根据转换为 camel case 的名称存储资产
*   支持加载图像和音频文件

engine/utils/AssetsLoader.js

你通常会在单例类上实现一个 ***getInstance()*** 方法，这里我们只是在调用类构造函数时返回先前创建的实例。

这里我们还依赖于一个 **Utils** 类，它目前只处理资产“名称到骆驼大小写”的逻辑；看起来是这样的:

engine/utils/Utils.js

这实际上是一个静态类，为了实现这一点，我们只是在它的构造函数中抛出一个错误。这意味着试图调用 ***new Utils()*** 将会失败。这个类目前相当简单，因为它只有一个静态方法；我们以后可能会添加更多。

**EventDispatcher** 类可以:

*   成为单例，因为所有游戏类都需要能够引用同一个 **EventDispatcher** 实例。这通常不被认可，因为它基本上是一个全局事件调度器，但是如果我们以后想要实现 flux 架构，我们将需要它。
*   公开 ***on()*** 和 ***trigger()*** 方法，帮助我们订阅和发出事件。

engine/events/EventDispatcher.js

你可能会认为这个看起来比其他的更奇怪，这是有原因的。使用 ES6 语法扩展 singleton 有点棘手，在这里阅读我描述这个问题的文章。

我们还没完。在我们画出任何东西之前，我们还有一些编码工作要做。根据上一篇文章中的模式，我们现在可以专注于实现扩展 **EventDispatcher** 的**键盘**类。

**键盘**类可以:

*   单身
*   暴露告诉我们某个键是否被按下的 getters

暴露 *A 到 Z* 、*空格*、*箭头键*、 *tab* 、 *enter* 、 *shift* 、 *ctrl* 、 *alt* 、 *esc* 和功能键 *F1 到 F12* 的 getters 就足够了。

engine/inputs/Keyboard.js

这个类也使用 **KeyboardEvents** ，看起来像这样:

engine/events/KeyboardEvents.js

这个类包含一些静态方法，这些方法返回基本上作为常量的事件名。你可能想知道为什么我没有简单地做:

```
export const KEY_UP = 'keyup';
```

这是因为这种方法允许导入类，通过该类我们可以访问所有与**键盘**相关的静态 getters。我们不需要分别导入每个常量。

既然我们终于有了**键盘**类，接下来我们可以关注**画布**。

**画布**类可以:

*   单身
*   能够在调整窗口大小时调整自身大小
*   允许访问

    <canvas>标签及其所有属性</canvas>

*   暴露用于绘图的画布 CTX(2D 上下文)

engine/ui/Canvas.js

此时，我们可以通过使用 *ctx* 属性直接在**画布**上进行绘制，但是这样做既无聊又愚蠢，因为它不会使用我们在其他类中构建的任何逻辑。让我们把重点放在构建另一个能帮助我们的类上，即:**场景**。

**场景**类可能:

*   扩展**画布**并保留对自身的引用，以便在扩展**场景**的其他类中访问
*   允许初始化 x，y，宽度和高度属性，这将需要有一些默认值
*   允许添加和删除**显示对象**
*   导出一个 ***update()*** 方法，该方法将依次调用所有存储的 **DisplayObjects** 的 ***update()*** 方法
*   基于提供的 x、y、宽度和高度，在每次更新时清除自身
*   使所有绘制位置都相对于**场景**实例——这意味着如果**场景**有 *{ x: 20，y: 20 }* 并且我们给它添加了一个**显示对象**和 *{ x: 0，y: 0 }* ，那么**显示对象**将被绘制在**画布上的{ x: 20，y: 20 }处**
*   隐藏在其视口之外绘制的任何元素，视口由 x、y、宽度和高度定义
*   提供一个静态的 ***wrap()*** 方法，如果对象的 x 和 y 位置超出了**场景**的边界，该方法将重置对象的 x 和 y 位置

engine/ui/Scene.js

我们有一个**场景**，基于它的逻辑，我们可以向它添加一个元素，该元素将被更新和渲染…前提是该元素有 ***update()*** 和 ***render()*** 方法。

现在是我们期待已久的时刻。这就是你如何用超级复杂的方式画出两个移动的方块…

在 *js* 目录下(与*引擎*目录同级)新建一个文件夹，命名为 *square* 。使用以下逻辑创建一个 *index.js* 文件:

square/index.js

为了简洁起见，我在同一个文件中创建了 **Square** 类，但是我们现在有了一个在画布上创建和渲染静态或移动对象的基本引擎。

方块以预定的速度在 x 和 y 轴上移动，如果按下 SHIFT 键，速度会加倍。对象的位置被包装到父屏幕**上，这样你就可以随意摆弄了。不要忘记你继承了所有的键盘功能，所以享受其中的乐趣吧。**

获取文件并在此进行测试:

[](https://github.com/raduGaspar/game-physx/tree/81b1d84d941da3e3a234e7586c656e57662d6110) [## 红景天/游戏-physx

### 一个用于 JavaScript 游戏制作的小工具

github.com](https://github.com/raduGaspar/game-physx/tree/81b1d84d941da3e3a234e7586c656e57662d6110) 

显然还有一些改进要做，比如:对所有单例实例使用" *const* "而不是" *let* "，弄清楚如何在有多个实例的类中使用私有变量而不导致覆盖(如果你在想"[weak map](https://hackernoon.com/tagged/weakmap)"……那可能弊大于利)，以及为我们的游戏引擎使用一个存储；仅举几个例子。

我们将在下一篇文章中继续讨论运动在游戏中如何工作，覆盖精灵表、精灵、动画精灵、控制帧速率、显示对象枢轴点和另一个实现示例。