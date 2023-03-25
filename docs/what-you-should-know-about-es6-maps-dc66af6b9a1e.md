# 关于 ES6 地图你应该知道什么

> 原文：<https://medium.com/hackernoon/what-you-should-know-about-es6-maps-dc66af6b9a1e>

![](img/3a68e79a94a5d2ab9d0e8fce4602d358.png)

[JavaScript](https://hackernoon.com/tagged/javascript) ES6 引入了新的[数据结构](https://hackernoon.com/tagged/data-structure)，叫做*地图*。map*是的设计，作为使用对象文字来存储需要唯一键的键/值对的替代方法，并提供非常有用的迭代方法。*

# *作为“映射”的对象文字*

*在 JavaScript 中，没有什么比对象文字更基础的了。创建排序映射就像用代码声明它一样简单。*

```
*var map = {
  "key1":"value1",
  "key2":"value2"
}
map.key1; // == "value1"*
```

*这在许多情况下都行得通，但是使用对象文字作为映射会有问题。我们可能有破坏对象原型方法的键。*

```
*var map = {
  "key1":"value1",
  "key2":"value2"
}
map.toString() == "[object Object]"  // Correct
map.toString = "value3"
map.toString(); // Uncaught TypeError: map.toString is not a function*
```

*使用对象文字的另一个缺点是所有的键只能是字符串。这在许多情况下都有效，但是当试图使用一个原始值作为一个字符串时，系统会在后台将其转换为一个字符串。*

*这种在没有通知用户的情况下发生的魔术可能会导致意想不到的结果，例如，如果提供的键是一个数组。*

```
*map[[1,2,3]] = "value4" // the provided key is an array
map['1,2,3'] = "value4" // the system has made the key a string*
```

*使用对象文字(ES6 之前)的另一个问题是不能保证属性/键的顺序。仅仅因为您已经按照一定的顺序添加了键，并不意味着当您遍历这些键时，它们将保持该顺序。*

```
*var o = {}
o.a = 1
o.b = 2
o.c = 3for(key in o) console.log(key); 
// expected a,b,c - but not guaranteed to be in that order*
```

*对象也缺少 forEach 方法。如果您习惯于使用。forEach()对象不能以这种方式迭代。*

```
*o.forEach // undefined*
```

# *ES6 中的地图*

*ES6 地图为此提供了一个新的对象。您可以通过调用 **new Map([iterable])** 来创建一个新地图。Iterable 可以是数组或任何元素是键/值对的 iterable 对象。提供的键/值对被添加到新映射中。*

```
*var m = new Map()
m.set('a', 1)
m.set('b', 2)
// {"a" => 1, "b" => 2}*
```

# *地图属性和方法*

***地图提供了一个非常方便的地方。size** 属性来获取地图的大小。大小也非常方便地显示在 Chrome Dev 控制台中，以及地图的内容。*

```
*var m = new Map()
m.set('a', 1)
m.set('b', 2)
m.size; // 2  Make sure to use .set so that size updates correctlym; // Map(2) {"a" => 1, "b" => 2}*
```

# *map.clear()*

*清除所有条目的映射。*

# *map.delete(键)*

*删除一个键，如果删除成功，则返回。*

```
*var m = new Map()
m.set('a', 1)
m.set('b', 2)
m.delete('a'); // true
m.delete('c'); // false (key was not there to delete)*
```

# *map.get(key)，map.has(key)*

*获取密钥，并查找密钥是否存在。*

```
*m.get('a'); // 1
m.has('a'); // true
m.has('z'); // false*
```

# *map.forEach(fn)*

*提供了一种迭代地图的便捷方法。您可以非常容易地获得键、值和地图本身。*

```
*var m = new Map()
m.set('a', 1)
m.set('b', 2)
m.forEach((k, v, m) => console.log(`key:${k} value:${v} map:${m}`))// key:1 value:a map:[object Map]
// key:2 value:b map:[object Map]*
```

# *为..关于*

*迭代地图的另一个选项是使用**来..的语法，它可以很容易地提供对映射的键和值的访问。***

```
*for([key,value] of m) 
  console.log(key + '=' + value)
// a=1
// b=2*
```

# *m.keys()*

*返回一个完整的迭代器，因此您可以根据需要使用。下一个()*

```
*var m = new Map()
m.set('a', 1)
m.set('b', 2)var iter = m.keys()
iter.next(); // Object {value: "a", done: false}
iter.next(); // Object {value: "b", done: false}
iter.next(); // Object {value: *undefined*, done: true}*
```

# *m.values()，m.entries()*

*映射值和条目可以用同样的方式迭代。条目会给你一个[key，value]类型的数组。*

```
*var iter = m.values()
iter.next(); // Object {value: 1, done: false}
iter.next(); // Object {value: 2, done: false}
iter.next(); // Object {value: *undefined*, done: true}var iter = m.entries()
iter.next(); // Object {value: ["a", 1], done: false}
iter.next(); // Object {value: ["b", 2], done: false}
iter.next(); // Object {value: *undefined*, done: true}*
```

*映射为您提供了对需要在键、值或条目上执行的操作的大量控制。*

# *2d 阵列到地图*

*映射可以在构造函数中采用 2d 数组。每个数组条目的格式都应该是[key，value]。*

```
*var arr = [['a', 1], ['b', 2]]
var m = new Map(arr)m; // Map(2) {"a" => 1, "b" => 2}*
```

# *非字符串作为键*

*您可以使用各种对象作为映射键。系统不会像对对象文字那样自动将键转换成字符串。这为使用地图做有趣的事情提供了广泛的机会。例如，将文档对象设置为键。*

```
*var m = new Map()m.set(document, true) // uses the document object as a key
// Map(1) {#document {} => true}m.get(document) 
// true*
```

*映射是 ES6 的一个重要的新特性，可以在多种用例中用于存储键/值对。*

*[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)**[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)**[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)*

> *[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。*
> 
> *如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！*

*![](img/be0ca55ba73a573dce11effb2ee80d56.png)*