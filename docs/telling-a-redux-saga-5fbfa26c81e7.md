# 讲述一个传奇故事

> 原文：<https://medium.com/hackernoon/telling-a-redux-saga-5fbfa26c81e7>

在 [Meeshkan](https://meeshkan.com) ，我们是网络和移动应用中`[redux](http://redux.js.org/)`的大量用户。在首次使用了`[redux-promise](https://www.npmjs.com/package/redux-promise)`和`[redux-thunk](https://github.com/gaearon/redux-thunk)`中间件之后，我们现在很高兴地使用`[redux-saga](https://github.com/redux-saga/redux-saga)`作为我们处理异步操作的方式。如果你还没有在你的 React 应用中使用`[redux-saga](https://github.com/redux-saga/redux-saga)`，你应该放下你正在做的事情去阅读教程！

[传奇模式](https://www.youtube.com/watch?v=xDuwrtwYHu8)是一种通过纯函数描述副作用的好方法。有许多不同的方式来理解这个模式，但是在我自己的头脑中，我喜欢把它看作一个脚本。以这个传奇故事为例:

```
import { call, put } from 'redux-saga/effects';
import fetchKittenPictures from './kittens';function* fetchKittenPicturesSaga(action) {
  try {
    yield put({ type: 'FETCH_KITTEN_EXECUTING' });
    const critters = yield call(fetchKittenPictures);
    yield put({ type: 'FETCH_KITTEN_SUCCESS', payload: critters });
  } catch(e) {
    yield put({ type: 'FETCH_KItTEN_FAILURE' });
  }
}
```

把这个剧本给一个会讲述我的传奇故事的演员，她会得意洋洋地宣布:

```
KITTEN FETCH (a play in one act)Teller: I am executing a kitten fetch!
Teller: I am making a call to my kitten fetch api!
[she goes offstage, comes back]
Teller: I have my kittens.
[in the event that she breaks her leg, she yells "NO KITTENS!"]
```

但是如果我们把我们的传奇故事给已故的伟大的罗宾·威廉姆斯。他会遵循我们的剧本吗？不要！它可能看起来像这样:

```
KITTEN FETCH (a play in one act)Robin: I am executing a kitten fetch!
Robin: Wait, kittens don't fetch, dogs fetch. And why are we executing kittens? That's cruel.
[pauses for laughter]
Robin: Ah, we're talking about an API.  I could sure use an IPA!
[pauses for laughter]
Robin: I am making a call to my kitten fetch api!
[he goes offstage, comes back]
Robin: I have my kittens.
[in the event that he breaks his leg, he yells "NO KITTENS!"]
```

不同的讲述者会以不同的方式讲述这个故事。

在 React 开发的世界中，saga 包含核心业务逻辑，有时 saga 需要根据运行它们的平台稍作修改。例如，对于同一个故事，我们可能需要两个“出纳员”——一个负责网络，一个负责移动。他们需要能够分享他们的大部分“剧本”,同时根据他们讲述传奇故事的独特方式进行细微的调整。一种反模式是将所有不同的叙述方式硬编码成一个故事。更干净的解决办法是把这个故事交给一个讲述者，让她来讲述。

因此，我们设计了一个名为`saga-teller`的黑客工具来做这件事。这个 API 非常简单:

```
function sagaTeller(saga, rules);
```

第一个参数`saga`是出纳员将讲述的传奇故事，而`rules`是出纳员将如何修改传奇故事的蓝图。

规则对象本身的形式是:

```
// match
// note that put and call should be mutually exclusive{
  when: <'before' or 'after'>,
  put: <an action if we are responding to PUT>,
  call: <a function if we are responding to CALL>,
  effect: <any valid redux-saga effect like all, put, take...>
}// rules
{
  first: <call or put>,
  matches: <Array of match>,
  last: <call or put>
}
```

利用我们的小猫传奇，我们可以这样做:

```
const myVeryOwnFetchKittenSaga(fetchKittenSaga, {
  first: call(lookMomIKnowHowToUseSagas),
  matches: [
    {
      when: 'after',
      put: 'FETCH_KITTEN_EXECUTING'
      effect: put({type:'MOM_YOU_SHOULD_BE_MORE_PROUD_OF_ME'}),
    },
  ],
});
```

现在你知道了。现在，当我讲述传奇故事时，首先要做的是调用一个函数，告诉我妈妈我知道如何使用传奇故事。然后，`fetchKittenSaga`突突前进，和`FETCH_KITTEN_EXECUTING`发出一个`put`动作后，发出一个`MOM_YOU_SHOULD_BE_PROUD_OF_ME`动作。这样，原来的传奇保持完整，我的个人传奇可以在任何我想用的地方使用。这也很容易测试——您只需要断言 rules 对象包含正确的逻辑。

在一个更真实的例子中，这允许你写一个传奇，然后写一些规则，根据你是网络还是移动，Android 还是 iOS 等等来调整它的行为。注意`saga-teller`模式是一个[目标线](https://www.youtube.com/watch?v=XitfoHsdqUg)策略。你不应该用它来驱动你的应用程序。把它想象成你的传奇故事的一个补丁——它应该代表修改一个核心传奇故事所需的最少工作量，但是每当讲述者的版本使传奇故事本身相形见绌时，它就变成了一个笨拙的杂牌。在这种情况下，只需编写(并测试)两个不同的传奇。

下面是一个要点，展示了一个`saga-teller`的基本实现。底部的`tell`功能只是让你把多个柜员串在一起，就可以做`tell(mySaga).by(teller0, teller1, teller2)`了。把这想象成电话游戏，每个柜员听到前一个的版本，然后用他们的特殊添加修改它。

我肯定不会为此做任何东西，因为它是基于一个内部 API，在更新的情况下非常脆弱。但它足够短，你可以复制它，粘贴它，并修改它到你的心的内容。我们已经在 [Meeshkan](https://meeshkan.com) 使用了它的一个版本，取得了很大的成功。享受讲述你的传奇吧！

![](img/8aacf94a89f3457ee70d05f5e2a49b8a.png)