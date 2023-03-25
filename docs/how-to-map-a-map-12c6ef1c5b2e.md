# 如何绘制地图

> 原文：<https://medium.com/hackernoon/how-to-map-a-map-12c6ef1c5b2e>

2016 年接近尾声，ES6 无处不在。但是 ES6 带来了一些新的语法和 API。本文旨在回答一个非常具体的问题:如何将地图映射到数组中？

> *TL；*博士:用 Array.from(map，([key，value]) = > value)

如果你正在读这篇文章，说明你对更完整的答案感兴趣。为了清楚起见，我们在这里讨论的是 JavaScript [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 类型。map 的实例不提供 Map 方法。这篇文章是关于如何实现一个。

还感兴趣吗？如果是这样，我们将首先讨论这样做的必要性，主要是为什么你可能更喜欢使用地图而不是常规对象。

然后，我们将讨论进行这种映射的不同方法。

# 贴图与对象

> 在进一步讨论之前，我们先试着回答一个简单的问题:为什么会有人在 JavaScript 中使用 Map？

从另一种语言来看，比如说 *Java，*使用 [Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Global_Objects/Map) 似乎是显而易见的。但如果你来自 ES6 之前的世界呢？为什么要在物体上使用贴图？

需要说明的是，我并不提倡在任何地方都使用地图。一如既往，这是开发人员的选择，以决定什么最符合当前的情况。为了帮助你决定，我会在这里列出一些你可能更喜欢使用地图而不是普通物体的点。

## 键入的键

> 简而言之:你可以使用任何类型作为地图的键。不得使用 Object 或 Array 或其他对象的任何复杂类型属性。也许只使用字符串是个更好的主意。

一个普通的 JavaScript 对象，简单地用`{}`创建，非常灵活。对于每个键，您可以为其分配任何其他 JavaScript 类型:

```
const obj = {}; 
obj.first = ‘myvalue’;
obj.arr = [];
obj.number = 2;
obj.deep = {};
obj.map = new Map();
```

此外，尽管您可以在某些地方读取内容，但您可以向对象添加一些动态键:

```
const mykey = 'foo' + Math.random();
const obj = {};
obj[mykey] = 'bar';
```

甚至，使用新的 ES6 计算属性语法:

```
const mykey = 'foo' + Math.random();const obj = {
  [mykey]: 'bar'
}
```

最后三个例子应该向你证明了普通的 JS 对象是非常灵活的。仍然有很大的不同:

> 尽管 Map 可以有任何类型的键，但是来自 JavaScript 对象的键将被转换为字符串

这种差异可能是副标题，但如果理解不正确，可能会导致有趣的错误

```
const map = new Map();
map.set(2, 'two');
map.set('2', 'TWO');
// Map {2 => "two", "2" => "TWO"}const obj = {};
obj[2] = 'two';
obj['2'] = 'TWO';
obj;
// Object {2: "TWO"}
```

在这种情况下，根据上下文，这两种行为都是有用的，但是现在，让我们试着猜测如果你试图分配一个对象会发生什么:

```
const obj = {}
const key1 = { id: 'foo' }
const key2 = { id: 'bar' }obj[key1] = 'KEY1';
obj[key2] = 'KEY2';obj[key1];
// KEY2"
```

在前面的示例中，您为一个对象分配了由该对象提供的两个不同的属性值，但是当您尝试访问第一个属性值时，您将获得第二个属性值。这可能看起来很奇怪，但是如果你仔细阅读的话，这应该是符合逻辑的。如前所述，对象属性在开始使用前被转换成字符串。人们可以想象，这种转换是使用“JSON.stringify”或类似的东西来完成的，但事实要简单得多:我们只需执行“new String(prop)”。toString()` 0

```
String(key1).toString() 
// "[object Object]"String(key2).toString() 
// "[object Object]"new String(key1).toString() === new String(key2).toString()
```

有了前面的例子，现在就很容易理解为什么使用复杂类型(如 Object、Arrays 或 Map)作为属性名不是一个好主意了。如前所述，您可以在访问您的属性之前使用序列化层，但是如果您的 key 对象在赋值和访问之间被修改，那么这会给您带来与 map 不同的行为。

# 内置方法和属性

我们之前已经看到，如果您需要使用复杂的键作为属性名，您最好使用 Map。但是有更多的原因。其中之一是内置方法的可用性。

地图对象最有用的内置属性可能是`size`。它可能不需要进一步解释，只需返回地图中元素的当前数量。为什么有人更喜欢做`map.size`而不是`Object.keys(obj).length`，这可能很明显。

其他的实例方法，比如`map.keys()`、`map.values()`和`map.entries()`，也非常类似于我们用相应的静态对象方法得到的，但是有一个主要的区别:它们返回迭代器而不是数组。我不会在这里深入讨论细节，但是我会在本文的第二部分回到这个问题。

最后，提供了另一个方法作为帮助函数，它是`map.has(key)`，并且对应于`obj.hasOwnProperty(key)`方法。

`Map`还有一些方法。下一节将提供其中大部分的详细信息。

# 使用它有一个共享参考

除了将复杂对象作为关键特性之外，使用 Map 的原因之一是将它作为跨对象的共享引用，这是一种纯粹的 POO 风格。

这个映射可以是单个的，或者简单地跨实例共享，但是可以被实例修改。这与 JavaScript 世界一成不变的趋势完全相反，但同样，一切都与上下文有关。

来到便便世界，`map.get`和`map.set`会觉得很自然。此外，在纯 CRUD 风格中，您可能希望能够从地图中删除一个元素。这里你可以使用`map.delete`方法，你必须承认，这种方法比`delete obj.key`感觉要简单得多

最后，您还有`map.clear`方法，它允许您将地图重置为空状态。

# 循环

Map 对象最流行的特性之一是迭代特性。事实上，Map 和他的新伙伴一起到来了，新的`for...of`语法。

`for...of`语法允许您编写代码，例如:

```
let iterable = new Map([["a", 1], ["b", 2], ["c", 3]]);

for (let entry of iterable) {
  console.log(entry);
}
// [a, 1]
// [b, 2]
// [c, 3]

for (let [key, value] of iterable) {
  console.log(value);
}
// 1
// 2
// 3
```

> 摘自[https://developer . Mozilla . org/en-US/docs/Web/JavaScript/Reference/Statements/for...的# iteration _ over _ a _ Map](https://developer.mozilla.org/en-US/docs/Web/JavaScript/Reference/Statements/for...of#Iterating_over_a_Map)

就我个人而言，我不太喜欢 C 风格的迭代，更喜欢使用高阶函数。看来我不是唯一的一个，因为这也是著名的 [Airbnb 风格指南](https://github.com/airbnb/javascript#iterators--nope)的一部分。

# 绘制地图的不同方法

不，我们有了基本的地图，我们现在将尝试解决最初的问题

## 为了…的

如果您喜欢`for...of`语法，您可能不喜欢函数式方法，也不喜欢不可变方法。如果是这样，那么您可能只是想使用下面的语法来映射一个映射。

```
const iterable = new Map([["a", 1], ["b", 2], ["c", 3]]);const squareValues = [];for (let [key, value] of iterable) {
  squareValues.push(value * value);
}
squareValues;
// [1, 4, 9]
```

> 如果你这样做，我会很难过，因为整篇文章的目标就是让你避免使用这种语法。

我们以前没有描述过它，但是这种语法是可能的，因为 Map 是一个可迭代的对象——这就是`map.keys()`和`map.values()`的返回。

## 为每一个

在已经描述过的 Map 实例方法之上，还有一个尚未讨论的方法:`map.forEach`

这里提供的 forEach 方法与数组提供的 forEach 方法非常相似。使用与 Array 相同的语法，实现 map 方法相当容易。

```
**const** mapMap **=** **function**(map, mapFunction){

  **const** toReturn **=** [];
  map.forEach(**function**(value, key){ // be careful to the args order
    toReturn.push(mapFunction(value, key));
  })

  **return** toReturn;
}
```

使用这种语法对我来说没问题，但是仍然显示了一个循环和`toReturn`数组的变异。这在 utils 文件中是可以的，但是我们可能不希望到处都这样污染我们的代码。我的目标是一个纯粹的原生函数式语法。

## 数组. from

我们最近讨论了这样一个事实，即我们所追求的`map`方法在 Array 上是可用的。也许一个好的解决方案是将我们的 map 实现为一个数组，以便调用 Map 方法。此外，我们还描述了映射是可迭代的。

此外，自 ES6 规范以来，新的静态方法现在在 Array 上可用。其中之一就是`Array.from`法。它允许复制一个数组，将一个类似数组的对象转换成一个数组，也可以将一个可迭代的对象转换成一个数组。

人们现在可以这样写:

```
Array.from(p).map( ([key, value]) => value * value )
```

我认为这开始感觉很好。我们将地图转换成数组，然后在上面应用地图。我们还能希望什么？这样做是不是只有一个操作？好消息，这是可能的。

实际上，可以用第二个可选参数调用 Array.from 方法，对应于一个映射方法。

然后我们可以做:

```
Array.from(p, ([key, value]) => value * value)
```

对我来说，这是回答最初问题的最佳方式:“如何在地图上绘制地图”

# 结论

有时候用地图代替一个物体真的很有意义。当出现这种情况时，有时仍然需要应用 map 方法将一个 Map 变换成一个数组的变换对象。在这样做的时候，对我来说最好的方法是使用带有两个参数的 Array.from 方法，一个用于映射，另一个用于映射函数。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)，[在脸书上点赞/给我们发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！