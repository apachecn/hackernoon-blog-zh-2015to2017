# ke ducer——用 5 行 javascript 自动编写 redux reducers

> 原文：<https://medium.com/hackernoon/automate-writing-redux-reducers-with-5-lines-of-javascript-cecb79fb9a35>

![](img/952fbb3b7c09d25ee3a35dc0551de798.png)

Photo by [Alex Knight](https://unsplash.com/photos/2EJCSULRwC8?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

我喜欢与 [React](https://hackernoon.com/tagged/react) 一起工作，并对这种前端模式的发展感到惊讶。React 和 [Redux](https://hackernoon.com/tagged/redux) 是两项出色的技术。对他们来说，再多的赞美也是不够的。

但是我一直在想的一个问题是，我够干吗？

来自香草通量的世界，Redux 感觉简洁。但是几个大项目之后，我又在做同样的事情，一遍又一遍。整个周期中感觉特别冗长的一部分是编写 reducers。我们采取一个行动和一个有效载荷，然后发射一个突变。

我首先想消除编写 reducers 的过程，所以我想出了[互斥](https://github.com/shivekkhurana/mutex)——一个没有副作用的通量，但从未完成它，因为 [JayStack](https://github.com/jaystack) 想出了一个类似的库，叫做 [Repatch](https://github.com/jaystack/repatch) 。

回想起来，Repatch 的想法是一脉相承的，但极其成熟。如果我在 Mutex 上工作了大约 2 年，我会得到 Repatch。作为一个懒惰的程序员，我最好放弃自己的库，使用 Repatch。

# 但是接下来是遗留代码(和心智模型)的实现

我花了很多时间调整[分形——一个无限缩放的 react 应用程序结构，](https://hackernoon.com/fractal-a-react-app-structure-for-infinite-scale-4dab943092af),它依赖于 raw Redux。尽管 Repatch 符合我的理念，但我并不放心押注于此，因为:

*   紧密耦合的动作和 reducers 使得编写自动化测试变得困难。
*   重新修补意味着没有回头路。如果我构建了一个应用程序，但过了一段时间后感觉不太好，我就被困住了。

还有其他值得注意的项目，如 [redux-zero、](https://github.com/concretesolutions/redux-zero)，但第二点仍然适用。

# **我想要的东西**

理想情况下，我想要一个系统:

*   非常符合我们的分形模式
*   不会使构建膨胀
*   减少(或完全消除)编写 reducer 的需要
*   不影响商店的可测试性
*   交换机的成本应该是最低的

# 尤里卡。

在引导一个新项目并复制粘贴我常用的认证模块时，我发现我的大多数 reducers:

*   只拿了有效载荷并把它们放在我的州
*   reducer 是我定义的一个函数(用一个大的开关盒)

我想到了写一个函数，它生成一个默认的 reducer，将有效负载放到状态上。它叫 ke ducer(“Kay-Deu-Cer”；仅仅 5 行代码就消除了我 95%的 reducers)。

我很想为此建立一个 npm 模块，但感觉最好不要对 js 疲劳有更多的贡献，所以我只做了一个要点。

# Redux 如何在没有 keducers 的情况下工作

在一般的 Redux 设置中，典型的登录缩减器如下所示:

```
export default function reducer(state={}, action) {
  switch (action.type) {
    case 'auth.loginError':
      return {...state, loginErrors: action.payload.errors}; case 'auth.loginSuccess':
      return {...state, isUserLoggedIn: true}; default:
        return state;
  }
}
```

动作定义可能是这样的:

```
export function login(email, password) {
  return (dispatch) => {
    api
      .post('/auth/login', {email, password})
      .then((res) => {
        Promise
          .all([
            storage.setItem('token', res.token),
            storage.setItem('user', JSON.stringify(res.user)),
          ])
          .then(() => {
            dispatch({type: 'auth.loginSuccess'});
          })
        ;
      })
      .catch((err) => {
        dispatch({
          type: 'auth.loginError',
          payload: {
            errors: Object.values(err.data)
          }
        });
      })
    ;
  };
}
```

您只需将这个缩减器加载到一个存储中，并将其“提供”给根组件，然后`dispatch(login)`在点击事件上运行。

# Redux 如何与 keducers 一起工作

使用 keducer，无论您传递什么作为有效载荷，它都会被粘贴在您的状态之上。但是它确实希望你的动作是由`. (dot)`分隔的字符串，所以你可能必须在那里做一些工作。

## 第一步:设置一个模块

模块基本上是一个 ducks 结构术语，表示一个文件中的 actions + reducers。你可以在我的[分形结构](https://hackernoon.com/fractal-a-react-app-structure-for-infinite-scale-4dab943092af)文章中了解更多。

唯一值得注意的区别是缺少一个缩减器，而是导出一个默认的 keducer。注意，keducer 使用一个名为`auth`的键，这个键基本上告诉它监听所有带有`auth.`前缀的动作。

现在，keducer 将自动获取您的有效负载，并将其添加到 auth 树下的 state 中。

```
import config from 'config';
import api from 'utils/api';
import storage from 'utils/localStoragePromise';
import keducer from 'utils/keducer';export default keducer('auth'); // <---- Where the magic happensexport function login(email, password) {
  return (dispatch) => {
    api.post('/auth/login', {email, password})
      .then(res => {
        Promise.all([
          storage.setItem('token', res.token),
          storage.setItem('user', JSON.stringify(res.user)),
        ]);
        dispatch({
          type: 'auth.loginSuccess', payload: {token, user}
        });
      })
      .catch(err => {
        dispatch({
          type: 'auth.loginError', 
          payload: {loginErrors: err.data.err}});
      })
    ;
  };
}
```

## 步骤 2:设置您的根存储

因为 keducer 返回一个函数，所以这里不需要做什么新的事情。只是像平常一样组合减少器并将其传递给提供者。

```
const rootReducer = combineReducers({
  auth,
});const store = createStore(
  rootReducer,
  compose(
    applyMiddleware(thunk),
    window.devToolsExtension ? window.devToolsExtension() : f => f
  )
);
```

这应该足够开始了，但是如果您是 Redux power 用户，您可能会想:

*   “嗯，这扼杀了 Redux 的整个概念，因为你不可能在不同的 Redux 之间共享动作”
*   “如果状态依赖于以前的状态，你甚至不能更新状态(例如，当用户滚动到页面底部时，在列表中加载更多项目)”。

不要担心超级用户，keducer 会保护你。

## **步骤 3:访问旧状态或在 keducers 之间共享操作**

`keducer`函数接受第二个参数，这是一个类似于:

```
export default keducer('users', {
  'auth.loginSuccess': (state, payload) => ({
    ...state, ...payload, ...{customStuff: true}
  })
})
```

这个映射的关键字是您希望捕获的动作类型，值是获取状态和有效负载作为参数的函数。这个状态是本地 reducer 状态，而不是应用程序状态，所以如果你愿意的话，你可以期望完成所有正常的 redux 魔法(但是你很少会这么做)。

## 示范项目

我正在做一个，很快就会有了。越快是相对于这篇文章获得的掌声和观点的数量。

# 结束语

这 5 行代码节省了大量编写 reducers 的时间，但更重要的是像我这样的人(没有太多经验)能够想出如此显而易见的东西，而整个 js 社区都押注于冗长的版本。

此外，竞争标准和 js 疲劳阻止了我构建另一个模块。我对 js 被教授、使用和改进的方式有点恼火，这看起来像是一种时尚。

我去寻找更好的解决方案，并在 clojurescript 中找到了一个。我对它的理解没有 js 那么成熟，但是非常非常非常有前途。非常非常。这就像将 js 定位为 JVM，并得到 cogn itect(clo jure 背后的公司，Rich Hickey 在那里工作)的支持。

# 🤙

我目前正在大量试验它(clojurescript)，如果你想让我写它，请让我知道。

# 🙏🏼

感谢您花时间阅读我的作品。希望对你的项目有帮助。

# ❤️

您可能还想阅读:

*   [分形——无限尺度的反应 app 结构](https://hackernoon.com/fractal-a-react-app-structure-for-infinite-scale-4dab943092af)
*   [区块链就像主机金融系统的晶体管](https://hackernoon.com/blockchain-is-like-the-transistor-of-the-main-frame-financial-system-b77034c2762e)
*   [走在前面的 99](/@shivekkhurana/the-99-that-went-before-196c57e3714e)【诗】&
*   [毕业一年后学到的 14 条人生经验](/@shivekkhurana/14-life-lessons-learnt-after-1-year-of-graduation-423de966c4a0)