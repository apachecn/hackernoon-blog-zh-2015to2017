# 你用 redux 做的坏事——在更新生命周期方法中调度

> 原文：<https://medium.com/hackernoon/evil-things-you-do-with-redux-dispatch-in-updating-lifecycle-methods-ad116de882d4>

在我工作的代码库中，我们正在做一件邪恶的事情。一些不该做的事。我们在 [React 生命周期方法](https://facebook.github.io/react/docs/react-component.html#updating)中进行调度调用，例如 componentWillReceiveProps、componentWillUpdate 和 componentDidUpdate。

这里有一个这样的场景。我们有两个不同的组件来访问两种不同的状态。

```
class Address extends React.Component {
  render() {
    return <input value={this.props.address} onChange={this.props.changeAddress}/>;
  }
}function mapStateToProps(state) {
  return {
    address: state.address
  }
}function mapDispatchToProps(dispatch) {
  return {
    changeAddress: (address) => dispatch(changeAddress(address)),
  }
}connect(mapStateToProps, mapDispatchToProps)(Address);
```

```
class Zipcode extends React.Component {
  render() {
    return <input value={this.props.zipcode} onChange={this.props.changeZipcode}/>;
  }
}function mapStateToProps(state) {
  return {
    zipcode: state.zipcode
  }
}function mapDispatchToProps(dispatch) {
  return {
    changeZipcode: (zipcode) => dispatch(changeZipcode(zipcode)),
  }
}connect(mapStateToProps, mapDispatchToProps)(Zipcode);
```

现在假设我们想在 address 组件中添加一个警告，如果用户输入的地址不在他们输入的邮政编码中。我们将在 redux 状态下存储这些警报数据。我们有很多方法可以做到这一点。但是当(1)地址改变或者(2)邮政编码改变时，解决方案需要检查地址。

我们的应用程序使用 redux-saga，所以这些解决方案是从那个角度出发的。我还假设我们需要打一些[网络](https://hackernoon.com/tagged/network)电话来做实际的检查。因此，我们需要在一些异步中间件中完成这项工作。

## 解决方案 1

当调用 changeZipcode 或 changeAddress 时，我们调度另一个操作。例如，Zipcode 组件的 mapDispatchToProps 函数将更改为:

```
function mapDispatchToProps(dispatch) {
  return {
    changeZipcode: (zipcode) => {
      dispatch(changeZipcode(zipcode));
      dispatch(checkAddress());
    }
  }
}
```

然后，我们有一个 saga 等待 checkAddress 动作根据邮政编码对地址进行实际检查，并更新一些状态以便在 Address 组件中呈现。

我不太喜欢这个解决方案，因为 changeZipcode 和 changeAddress 都需要完全相同的逻辑。

## 解决方案 2

也许我们只是有一个等待 changeZipcode 或 changeAddress 操作的传奇，并在这些操作发生时进行检查。现在我们不需要 checkAddress 操作。

## 解决方案永远不要这样做

我们是这样做的。邮政编码根本不会改变。相反，我们在 Address 上添加了一个`componentWillReceiveProps`来检查地址或邮政编码是否改变。如果是，调度另一个操作来检查地址。

```
class Address extends React.Component {
  componentWillReceiveProps(nextProps) {
    if (this.props.zipcode !== nextProps.zipCode || this.props.address !== nextProps.address) {
      this.props.checkAddress();
    }
  } render() {
    return (
      <div>
        <p>{this.props.addressAlert}</p>
        <input value={this.props.address} onChange={this.props.changeAddress}/>;
      </div>
    );
  }
}function mapStateToProps(state) {
  return {
    address: state.address,
    addressAlert: state.addressAlert,
    zipcode: state.zipcode,
  }
}function mapDispatchToProps(dispatch) {
  return {
    changeAddress: (address) => dispatch(changeAddress(address)),
    checkAddress: () => dispatch(checkAddress()),
  }
}connect(mapStateToProps, mapDispatchToProps)(Address);
```

当我第一次看到这个时，我感到不寒而栗。我抬头问了很多次为什么。感觉很不对劲。 ***它使组件比它应该知道的方式更多*** 。为什么 Address 又关心 zipcode 了？？

这可能看起来没那么糟糕。事实上，你可能会说这很好。Address 关心 zipcode，因为当 zipcode 改变时，它需要更新地址警告。但问题是。您的组件不应该考虑外部状态转换。这种担忧存在于 [redux](https://hackernoon.com/tagged/redux) 的土地上，而且只有在那里。

## 潜在的问题

为了进一步说明我的观点，这里有一些潜在的问题。例如，您可能有一个非常热的状态，当它改变时，您的应用程序的很多部分都需要改变。您需要将该状态添加到关心它的每个组件中，然后编写 componentWillReceiveProps check。当两个组件看到状态改变时，如何防止它们分派相同的动作？

或者更糟的是，你可以有一连串的渲染。

1.  组件 A 首先调度
2.  组件 B 从组件 A 的分派中观察状态的变化，并进行第二次分派
3.  组件 C 从组件 B 的分派中观察状态的变化，并进行第三次分派
4.  诸如此类。

您的应用程序可以经历这些多步转换，但是很难追溯开始整个转换的第一个动作。

最后一件事。我并不是说这些更新生命周期的方法不好。当你的组件有自己的内部状态时，你绝对需要它们。或者在与非 react 库交互时需要做一些必要的 DOM 工作。但是它们应该 ***永远不要*** 用于启动你的 redux 状态的其他变化，以响应其他 redux 事件。

总之，让你的组件尽可能简单。最好的组件只是它的道具的功能。如果您曾经在更新生命周期方法(如 componentWillReceiveProps、componentWillUpdate 和 componentDidUpdate)时看到调度调用，那么肯定有更好的方法。