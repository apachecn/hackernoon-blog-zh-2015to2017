# React JS 中基于 props 的初始化状态的常见缺陷

> 原文：<https://medium.com/hackernoon/common-pitfall-in-initialising-state-based-on-props-in-react-js-d56795a944aa>

![](img/b2dd7a3eb37c52d6cd856eb5e03b7a34.png)

最近我在做 [React JS](https://hackernoon.com/tagged/react-js) 项目时遇到了这个问题，当时我正在通过 props 将父[组件](https://hackernoon.com/tagged/component)状态的一部分传递给子组件。然后我用这个道具来设置我的子组件的初始状态(在做了一些其他的计算之后)。因此，问题是当父组件在状态改变时重新呈现时，子组件没有用更新的状态值呈现。比如说—

```
// our child component
class ChildComponent extends Component {
    constructor(){
        super(props);
        this.state = { count:this.props.value };
    }

    render(){
        return(
            <div>
                <p>I have {this.state.count} candies!</p>
            </div>
        );
     }
}// In parent component// Assuming this.state.value = 5
//This will render correctly "I have 5 candies!"
<ChildComponent value = {this.state.value} />// Now parent state "value" changed to 10
// This time child will again render "I have 5 candies!"
<ChildComponent value = {this.state.value} /> 
```

## 为什么会这样呢？

组件的构造函数只执行一次，当组件被挂载的时候。当组件重新渲染时，它不会被执行。当父组件被重新渲染时，它不会破坏和重新创建子组件。React 将[重用](https://reactjs.org/docs/reconciliation.html)之前渲染的子组件，并且不运行构造函数。

因为我们只在构造函数中设置子组件的状态，当子组件重新渲染时，不会调用这个构造函数。因此，即使在从父组件接收到新的属性时重新呈现子组件，状态也不会更新。这就是为什么子组件中的 state“count”的值仍然是“5”。

## 救援时组件将收到道具

来自 React 官方文档

> `componentWillReceiveProps()`在一个挂载的组件接收新的道具之前被调用。如果您需要更新状态以响应属性更改(例如，重置它)，您可以比较`this.props`和`nextProps`，并在此方法中使用`this.setState()`执行状态转换

**nextProps** 是从父组件接收的最新属性。

我们将使用这种生命周期方法，并在子组件收到来自父组件的新道具时更新它的状态。我们将比较当前 Props 值和下一个 props 值，如果它们不同，我们将使用 **setState()** 方法更新子组件的状态。

```
 class ChildComponent extends Component { constructor(){ ... } componentWillReceiveProps(nextProps){
        if(nextProps.value !== this.props.value){
            this.setState({count:nextProps.value});
        }
    } render(){...}
}
```

尽管这种解决方案是可行的，但 React 推荐了一种不同的方法，您可能想看看。称为[提升状态上升](https://reactjs.org/docs/lifting-state-up.html)。