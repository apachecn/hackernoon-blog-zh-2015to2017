# React —可重用组件状态

> 原文：<https://medium.com/hackernoon/react-reusable-component-state-805163dc355>

## 如何在组件之间共享状态和方法。

![](img/8933e4fa3854e49902a096a7f2866351.png)

到现在大家都学会了，用构图而不是继承。然而，对于某些事情，尤其是对于不熟悉函数式[编程](https://hackernoon.com/tagged/programming)的开发人员来说，可能不清楚如何正确地使用组合。

一个常见的问题是如何**重用**组件之间的状态和共享方法。

我用一个列表来举例。列表定义如下，有状态中的`nextItem()`、 `prevItem()`、`onChange(selectedItem)`方法、`selectedItem`等基本内容。它期待`props`中的`items`，当然最后会呈现一个列表。

```
**class** List **extends** Component {
  constructor(props) {
    **super**(props);
    **this**.**state** = {
      **selectedItem**: 0,
    }
  }

  componentWillUpdate({onChange, items}, nextState) {
    **if** (onChange && nextState.**selectedItem** !== **this**.**state**.**selectedItem**) {
      onChange(items[nextState.**selectedItem**]);
    }
  }

  nextItem() {
    **const** {**selectedItem**} = **this**.**state**;
    **const** {items} = **this**.**props**;

    **if** (items.length === **selectedItem** + 1) {
      **return**;
    }

    **this**.setState({**selectedItem**: **selectedItem** + 1});
  }

  prevItem() {
    **const** {**selectedItem**} = **this**.**state**;

    **if** (**selectedItem** === 0) {
      **return**;
    }

    **this**.setState({**selectedItem**: **selectedItem** - 1});
  }

  render() {
    **const** {items} = **this**.**props**;
    **const** {**selectedItem**} = **this**.**state**;

    **return** (
      <**ul**>
        {
          items.map((item, index) => (
            <**li className=**{**`**${**selectedItem** === index ? **'selected'** : **''**}**`**}>
              {item}
            </**li**>
          ))
        }
      </**ul**>
    );
  }
}
```

现在，当我们有了一个清晰的列表，这里有一个问题。

**问:如何创建 ListVertical 和 ListHorizontal？**

带 [HOC](https://hackernoon.com/tagged/hoc) (高阶组件)`withList` HOC。所以基本上，我们要做的是分离并传递我们需要的东西给包装的组件。看好了。

```
**function** *withList*(WrappedComponent) {
  **return class extends** Component {
    constructor(props) {
      **super**(props);
      **this**.**state** = {
        **selectedIndex**: 0,
      }
    }

    componentWillUpdate({onChange, items}, nextState) {
      **if** (onChange && nextState.**selectedIndex** !== **this**.**state**.**selectedIndex**) {
        onChange(items[nextState.**selectedIndex**]);
      }
    }

    *nextItem* = () => {
      **const** {selectedIndex} = **this**.**state**;
      **const** {items} = **this**.**props**;

      **if** (items.**length** === selectedIndex + 1) {
        **return**;
      }

      **this**.setState({**selectedIndex**: selectedIndex + 1});
    };

    *prevItem* = () => {
      **const** {selectedIndex} = **this**.**state**;

      **if** (selectedIndex === 0) {
        **return**;
      }

      **this**.setState({**selectedIndex**: selectedIndex - 1});
    };

    *selectItem* = (selectedIndex) => {
      **this**.setState({selectedIndex});
    };

    render() {
      **const** {selectedIndex} = **this**.**state**;

      **return** (
        <**WrappedComponent** {...**this**.**props**}
          **prevItem=**{**this**.*prevItem*}
          **nextItem=**{**this**.*nextItem*}
          **selectItem=**{**this**.*selectItem*}
          **selectedIndex=**{selectedIndex}
        />
      );
    }
  }
}
```

如您所见，被包装的组件将拥有所有可用的方法和`selectedIndex`。不是从`this`中使用，而是从`props`中使用。好多了！

对于包装的组件来说，能够改变所使用的父状态是很重要的。这样，您就可以保持生命周期，并可以创建新的方法来更新状态。这意味着完全重用状态。

> **注意** `*selectItem*`是额外实现的，这样被包装的组件可以改变`*selectedIndex*`，就像它是其状态的一部分一样。那是！重要。

让我们检查一下水平列表的外观。

```
**class** ListHorizontal **extends** Component {
  *customNextItem* = () => {
    *// Not really custom -_-* **const** {selectedIndex, selectItem} = **this**.**props**;
    selectItem(selectedIndex + 1);
  } render() {
    **const** {selectedIndex, prevItem, selectItem} = **this**.**props**;
    **return** (
      <**div**>
        <**img src="https://imgflip.com/s/meme/Troll-Face.jpg" alt="Horizontal list"** />
        <**div**>Selected item index: {selectedIndex}</**div**>
        <**span onClick=**{prevItem}>Prev item</**span**>
        <**span onClick=**{**this**.*customNextItem*}>Next item</**span**>
      </**div**>
    );
  }
}
```

就是这样！简单干净。一切都在`withList` HOC 中实现，现在可以很容易地应用于任何组件。HOC 最美好的一点就是，想包多少包多少！

家庭作业:现在列表看起来怎么样？

观察和预先评论:

*   我发现使用 HOC 比使用容器更灵活👎
*   我知道 CSS 可以实现横向和纵向列表😒
*   上面的代码还没有运行😱
*   不要让我写一个扩展列表的例子😝
*   缺点是使用`ref`😭

都读过。呜哇！