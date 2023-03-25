# 构建可测试性的 JavaScript 代码

> 原文：<https://medium.com/hackernoon/structure-your-javascript-code-for-testability-9bc93d9c72dc>

**编辑:**在花了更多时间测试 [JavaScript](https://hackernoon.com/tagged/javascript) 应用程序后，我认为这篇文章是简化一个更加微妙的主题的幼稚尝试。这里展示的想法可能对初学者仍然有用，但在某些情况下可能不切实际。

> “没有测试的代码被设计破坏了”——雅各布·卡普兰-莫斯

测试不是可选的。这并不是一项耗时的任务，你可以省去它来提高工作效率。作为一名开发人员，交付高质量的软件是你的责任，没有自动化测试，质量是无法保证的。

也就是说，有些情况下测试并不简单，除非你有一些锦囊妙计。这些难以测试的代码通常涉及副作用或在不同环境中取不同值的全局变量。在本文中，我将在代码示例中展示其中一些问题，并提出一种编码模式来减轻 JavaScript 中的许多测试痛苦。

## 代码结构

假设您正在编写一个 [React Native](https://hackernoon.com/tagged/react-native) 组件(您不需要了解 React Native 就能理解这些)。您的组件需要一个函数，在给定像素宽度的情况下，计算相应的屏幕空间百分比。我喜欢将大部分逻辑放在 React 组件之外，所以让我们为此创建一个独立的 JavaScript 模块:

```
// Scaling.js
import { Dimensions } from 'react-native';export const widthInPxToPercentage = (widthInPx) => {
    const screenWidth = Dimensions.get('window').width;
    return (widthInPx * 100) / screenWidth;
};
```

这个函数可以工作，但是很难测试，因为它是不纯的。纯函数是指对于相同的输入，总是返回相同的输出的函数。相反，一个不纯的函数可能有副作用，或者依赖外部条件来产生一个值。为了使我们的函数更纯粹，我们可以重构它，以屏幕宽度作为参数。这种技术被称为*依赖注入* (DI)。不过，不要被这个花哨的术语吓到。我们不会使用依赖注入器或任何复杂的依赖管理机制。我们重构后的代码如下所示:

```
// Scaling.js
import { Dimensions } from 'react-native';export const widthInPxToPercentage = (screenWidth, widthInPx) => (widthInPx * 100) / screenWidth;
```

这就更可考了！然而，使用这种方法，我们模块的所有客户端都需要传入屏幕宽度。在这些情况下，我喜欢使用两个函数将实现与 API 分开:

```
// Scaling.js
import { Dimensions } from 'react-native';export const widthInPxToPercentageImplementation =
    (screenWidth, widthInPx) => (widthInPx * 100) / screenWidth;
export const widthInPxToPercentage =
    widthInPx => widthInPxToPercentageImplementation(Dimensions.get('window').width, widthInPx);
```

实现函数是一个很容易测试的纯函数，在模块之外是不可见的(稍后会详细介绍，不要担心多余的`export`)。在这个例子中，它非常简单，但是在现实世界中，你经常会发现自己把它们写成高阶函数，因为一些依赖关系会变成函数。

入口点是一个向外界公开的函数，它调用实现并传递依赖项，因此客户端不需要担心它们。这些通常是不需要进行单元测试的一行程序(尽管您的集成测试很可能会涉及到它们)。

现在，让我们添加我们的测试:

```
// Scaling.spec.js
import { widthInPxToPercentageImplementation } from './Scaling';test('test 10px is 10% of 100px wide screen', () => {
    // Given
    const screenWidth = 100;
    const widthToConvert = 10; // When
    const widthInPct = widthInPxToPercentageImplementation(screenWidth, widthToConvert); // Then
    expect(widthInPct).toEqual(10.0);
});
```

您现在已经通过使用 DI 使您的代码可测试了！

# 目录结构和可见性

一些人将他们的测试放在项目根目录下的一个`__tests__`目录中。我不喜欢这种方法，因为它不具有可移植性:如果您将代码复制到另一个项目，您需要从完全不同的地方获取实现和测试。相反，我喜欢将我的测试放在与我的代码相同的文件夹中(注意代码片段中的相对导入)。这使得您的测试代码更具可移植性，并鼓励您团队中的开发人员将测试视为开发过程中不可或缺的一部分。

另外，您可能注意到在`Scaling.js`中，我导出了实现函数和入口点。入口点需要对外界可见，但是实现不需要。然而，我没有办法在测试中使用实现函数，除非我导出它们，所以我必须导出它们。为了更好地组织模块的可见性，我在实现和测试文件旁边使用了一个`index.js`文件。在这个文件中，您可以通过使用一个`export ... from`语句来指定您想要向外界公开的 API。我们的`index.js`文件应该是这样的:

```
export { widthInPxToPercentage } from './Scaling';
```

这种方法有两个缺点，这两个缺点并不严重，但是您应该注意到:它产生了一点重复，并且它不阻止人们从外部世界访问该模块。

复制并不可怕，尽管我必须承认这感觉有点像维护`.h`文件。如果您的团队知道这个约定，并且您在导入模块时使用对目录的引用而不是实现文件，那么可见性应该不是问题。也就是导入`'src/Scaling'`而不是`src/Scaling/Scaling`:当导入一个带有`index.js`文件的文件夹时，JavaScript 会隐式导入`index.js`文件。

```
**PRO Tip:** If you're a Vim user you can run **:r !grep export ./Scaling.js | grep -v Implementation** inside your **index.js** file to automatically grab the functions you want to expose (you'll obviously need to edit that output to make a proper **export** statement).
```

至此，您的模块已经完成，如下所示:

```
src/Scaling/
├── Scaling.js
└── Scaling.spec.js
```

现在，您有了一个以可测试的方式构建代码的模式，同时鼓励其他人也这样做。这绝不是一个放之四海而皆准的解决方案，您的团队可能需要自定义约定，但重要的是它们确实存在。

你觉得这种模式怎么样？你知道解决这个问题的更好的方法吗？在评论里或者推特上告诉我 [@bug_factory](https://twitter.com/bug_factory) 。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！