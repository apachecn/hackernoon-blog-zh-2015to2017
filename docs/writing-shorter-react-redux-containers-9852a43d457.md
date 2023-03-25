# 编写更短的 React-Redux 容器

> 原文：<https://medium.com/hackernoon/writing-shorter-react-redux-containers-9852a43d457>

![](img/3ca127d342438a2e3f78ac08a006c1f8.png)

如果你整天写 react + redux 代码，很可能你的手指已经习惯于打出这种模式的`mapStateToProps`、`mapDispatchToProps`和`connect`:

As described in the redux docs: this pattern produces a “Container” which is a component that listens to changes in redux store and re-renders automatically.

没关系。功能是可重用的，一切都很简单，有什么不喜欢的呢？

作为一个实验，我写了这个，并最终保留了它，因为它使样板文件更加简洁:

```
import {connect} from 'react-redux'const reduxify = (container) =>
    connect(
        container.mapStateToProps,
        container.mapDispatchToProps,
    )(container.render)
```

我们所做的就是一次传入`mapStateToProps`、`mapDispatchToProps`和`render`函数，而不是使用两步链式函数调用。

下面是新的、更短的语法，它使用`reduxify`函数来创建一个“容器”:

大约 9 行的样板文件变成了大约 6 行，但更重要的是，它并没有降低原始文件的良好品质:可测试性&函数易于重用。

![](img/bb7f76c8802a617e6140f5b60bb17470.png)

# 测试和重复使用

也许你想在不止一个地方重用`mapStateToProps`，或者你想单元测试它，不用担心！

它们只是函数，你可以在任何你想要的地方定义它们，导入它们，并把它们放入`reduxify`。对象[属性值速记](https://ariya.io/2013/02/es6-and-object-literal-property-value-shorthand)允许它保持清晰和简洁。

```
import {mapStateToProps} from './other-component.js'const Component = reduxify({
    mapStateToProps,
    render: (props) =>
        <div>...</div>
})
```

这里有一个在 reduxify 调用之外定义的`mapStateToProps`的例子:

一位有帮助的读者指出，如果你使用动作创建器，那么`mapDispatchToProps`甚至不一定是一个函数:

# 下载`reduxify`库

哈哈开个玩笑，我不会给你一个 6 行函数的`npm`包，只是[复制/粘贴](https://gist.github.com/pirate/0aa0e1b9fe0f0c989241e90cb51e20a8):)