# 如何建立自己的反应系统

> 原文：<https://medium.com/hackernoon/how-to-build-your-own-reactivity-system-fc48863a1b7c>

![](img/54439eb546da599490b3b9286158b789.png)

*想了解 Vuex？检查我的* [*手动课程*](https://bit.ly/31v8NPq) *！。给我发消息成为早期评论者，免费获得。*

几个月前，我在[`update()` *。*该方法使用旧值和新值作为参数调用构造函数中提供的回调函数。当 *Dep#notify()* 方法在它的值改变后对它的每个订阅者调用`update`时，就会用到它。](https://medium.com/u/1b199ed2dfd#depend() </em>方法调用，我们将在下一节更详细地讨论这个方法。</li><li id=)

[下面是我们的 ***观察者*** 类的代码:](https://medium.com/u/1b199ed2dfd#depend() </em>方法调用，我们将在下一节更详细地讨论这个方法。</li><li id=)

# [`defineReactive()`](https://medium.com/u/1b199ed2dfd#depend() </em>方法调用，我们将在下一节更详细地讨论这个方法。</li><li id=)

[根据](https://medium.com/u/1b199ed2dfd#depend() </em>方法调用，我们将在下一节更详细地讨论这个方法。</li><li id=)[源代码](https://github.com/vuejs/vue/blob/61187596b9af48f1cb7b1848ad3eccc02ac2509d/src/core/observer/index.js)、`defineReactive()`、*、*“定义一个对象上的反应属性”。这是通过向给定对象的给定属性添加 getters 和 setters 来实现的。每个属性都有一个 ***Dep*** 实例与之相关联。每当访问一个反应对象的属性时，getter 调用 *Dep#depend()，*将当前目标 ***Watcher*** 作为订阅者添加到属性的 ***Dep*** 实例中。每当属性被更改时，setter 调用 *Dep#notify()* ，后者调用属性的 ***Dep*** *的每个订阅者的 *update()* 方法。*下面是 *defineReactive()* ，基于源代码:

# 这一切是如何协同工作的

现在我们已经写了反应系统的每一部分，但是它是如何工作的呢？系统的每个部分都与所有其他部分紧密相连，因此很难理解。让我们一步一步地看看在我们的 ***观察器*** 类的示例用法中发生了什么。

我们将从设置好一切开始:

```
const foods = { apple: 5 }// make foods reactive, register deps for each property
walk(foods)// Instantiate the watcher, which takes a getter and a callback
const foodsWatcher = new Watcher(() => foods.apple,
                                 () => console.log('change')
                                 )
```

首先， ***观察器*** 类的构造函数运行如下:

```
this.value = this.get()
```

下面是*观察者#get()* :

```
pushTarget(this) // Imported from dep.js
const value = this.getter()
popTarget() // Imported from dep.jsreturn value
```

首先，它调用`pushTarget()` 函数，将`this`****`foodsWatcher`)赋值给`Dep.target`。然后，它调用`this.getter()` *，*第一个函数传递给 ***观察器*** 构造器。`foodsWatcher` 的 getter 只是返回`foods.apple`的值。由于`foods.apple`是由`defineReactive()` *，*激活的，它也将运行激活的 getter:****

```
**// adds Dep.target as a subscriber to the property's dep instance
dep.depend()return value**
```

****这将`foodsWatcher`注册为与`foods.apple`关联的 ***Dep*** 实例的订阅者。所以现在在`foodsWatcher`和`foods.apple`之间有一个连接。****

****这有什么帮助？假设我们改变`foods.apple`。****

```
**foods.apple = 6**
```

****这样做将调用`foods.apple`上的 setter。setter 运行`dep.notify()`，而在 dep 的每个订户上调用`update()` 。由于`foodsWatcher`是 ***Dep*** 实例的订阅者，因此`dep.notify()` 调用将触发`foodsWatcher`上的更新方法。 *Watcher#update()* 是做什么的？****

```
**update() {
  const value = this.get()
  const oldValue = this.value
  this.value = value this.cb(value, oldValue)
}**
```

****它更新其对当前值的了解，然后调用构造函数中提供的回调。记得我们指定的回调是****

```
**() => console.log('change')**
```

****所以，当我们改变`**foods.apple**`时，我们的反应系统会让我们知道！最酷的是，这种情况的发生并没有每毫秒进行一次脏检查。它的发生不需要我们显式地设置状态。它就是*起作用了*，根本不用我们去想它，就像一个电子表格。这就是 Vue 的反应系统如此不可思议的原因。****

****如果你想在一个地方看到我们反应系统的所有代码，我做了一个非常酷的演示。感谢阅读！****

# ****资源****

*   ****我去的[前端硕士课程](https://frontendmasters.com/live-event/vue-js-advanced-features-ground/)(如果你有前端硕士订阅的话)****
*   ****Vue 的[关于反应性的文件](https://vuejs.org/v2/guide/reactivity.html)****
*   ****埃文在 2016 年做的关于反应性的演讲****
*   ****Vue 的[源代码](https://github.com/vuejs/vue/tree/dev/src/core/observer)(终极参考)****