# 关于 ES8 您应该知道的 5 件事

> 原文：<https://medium.com/hackernoon/5-things-you-should-know-about-es8-e4ccd955d883>

![](img/8557e4d0d567055c0c855ba00f9a819f.png)

ECMAScript 2017 第八版(ES2017/ES8)已经正式发布，并于几周前出版，我们来搞清楚一些重要的变化。所有这些都可以在 Node 8 和没有 babel 或任何 polyfills 的最新版本的浏览器中使用。

[这里是详细的规格](https://www.ecma-international.org/publications/standards/Ecma-262.htm)。

# 对象.值

> 类似于 Object.keys，但用于值。

比如`**Object.keys**` do，`**Object.values**`方法返回给定对象自己的可枚举属性值的数组，而不是键。

# 对象.条目

> 如果我们合并 Object.keys 和 Object.values，那就是 Object.entries，它返回一个[key，value]数组。

`**Object.entries()**`方法返回给定对象自己的可枚举属性`[key, value]`对的数组。

# padStart

> 从开始填充一个字符串，直到达到给定的长度。

`**padStart()**`方法用另一个字符串填充当前字符串(如果需要，可以重复)，这样得到的字符串就达到了给定的长度。从当前字符串的开始(左侧)应用填充。

# 帕德恩

> 从末尾填充一个字符串，直到达到给定的长度。

`**padEnd()**`方法用一个给定的字符串填充当前字符串(如果需要，可以重复)，这样得到的字符串就达到了给定的长度。从当前字符串的末尾(右侧)应用填充。

# 帕德泰

![](img/d490f3b5a81f765cfb54ce65b1d4b7ce.png)

Just a delicious pad thai

# 函数参数列表中的尾随逗号

享受 ES2017！