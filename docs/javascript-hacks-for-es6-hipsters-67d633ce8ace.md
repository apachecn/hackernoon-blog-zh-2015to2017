# ES6 潮人的 JavaScript 技巧

> 原文：<https://medium.com/hackernoon/javascript-hacks-for-es6-hipsters-67d633ce8ace>

继最初的[潮人 JavaScript hacks】之后，这里有一些新的好东西。2018 年写 JavaScript 其实又好玩了！](https://hackernoon.com/javascript-hacks-for-hipsters-624d50c76e8e)

下面是一张非 JavaScripter、非黑客、非 ES6er、非潮人的 PacMan 背景照片:

![](img/7a40a4dbd0ef9fca2726d41a210f4190.png)

Photo by [Erik Lucatero](https://unsplash.com/photos/FfL2yW0Rwes?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText) on [Unsplash](https://unsplash.com/?utm_source=unsplash&utm_medium=referral&utm_content=creditCopyText)

# 潮人黑客# 1——交换变量

使用`Array Destructuring`交换数值

```
let a = 'world', b = 'hello'
[a, b] = [b, a]
console.log(a) // -> hello
console.log(b) // -> world// Yes, it's magic
```

# 潮人黑客#2 —带析构的异步/等待

再一次，`Array Destructuring`很棒。结合`async/await`和承诺，使一个复杂的流程变得简单。

```
const [user, account] = await Promise.all([
  fetch('/user'),
  fetch('/account')
])
```

# 潮人黑客# 3——调试

对于任何喜欢使用`console.log` s 进行调试的人来说，这里有一些很棒的东西(是的，我听说过`console.table`):

```
const a = 5, b = 6, c = 7
console.log({ a, b, c })// outputs this nice object:
// {
//    a: 5,
//    b: 6,
//    c: 7
// }
```

# 潮人黑客# 4——一个内衬

对于数组操作来说，语法可以更加简洁

```
// Find max value
const max = (arr) => Math.max(...arr);
max([123, 321, 32]) // outputs: 321// Sum array
const sum = (arr) => arr.reduce((a, b) => (a + b), 0)
sum([1, 2, 3, 4]) // output: 10
```

# 潮人黑客#5 —数组串联

可以使用扩展运算符代替`concat`:

```
const one = ['a', 'b', 'c']
const two = ['d', 'e', 'f']
const three = ['g', 'h', 'i']// Old way #1
const result = one.concat(two, three)// Old way #2
const result = [].concat(one, two, three)// New
const result = [...one, ...two, ...three]
```

# 潮人黑客# 6——克隆

轻松克隆阵列和对象:

```
const obj = { ...oldObj }
const arr = [ ...oldArr ]
```

更新:正如评论中提到的——这是一个浅层克隆。

# 潮人黑客#7 —命名参数

通过析构使函数和函数调用更具可读性:

```
const getStuffNotBad = (id, force, verbose) => {
  ...do stuff
}
const getStuffAwesome = ({ id, name, force, verbose }) => {
  ...do stuff
}// Somewhere else in the codebase... WTF is true, true?
getStuffNotBad(150, true, true)// Somewhere else in the codebase... I ❤ JS!!!
getStuffAwesome({ id: 150, force: true, verbose: true })
```

**已经全知道了？**

你是一个真正的时尚黑客，你可以阅读更多的[技巧&技巧来编写更好的代码](/@ketacode/the-non-secret-formula-for-writing-better-code-e41d1ff38682)。

再来说说 [**推特**](http://www.twitter.com/ketacode) **。**你也可以看看我的创业公司[鸟居](https://toriihq.com)我们在那里让“SaaS 头痛”消失。