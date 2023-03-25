# 从 JavaScript 炒作中退一步来理解 ES6 中的范围解析

> 原文：<https://medium.com/hackernoon/a-beautiful-example-to-master-es6-scope-resolution-7f948619fe95>

## 解释 ES6 中的 JavaScript 范围解析。

![](img/fec6522cd226e2d5478b4f0afad44cbf.png)

JavaScript 炒作是真的！这种语言本身、它的社区以及它的生态系统正在飞速发展。此外，如此多不同知识来源的存在使得语言反映了网络的无政府状态。在这种情况下，人们可能会花费数年的时间用 JavaScript 编码和构建项目，而没有真正理解该语言的关键概念，例如范围解析。

让我们来看看这个例子:

```
for(var i=0; i<3; i++){
      setTimeout( function foo() { console.log(i) }, 100);
}
```

你认为输出是多少？

如果这是你第一次看到这样的例子，你的答案是

```
0
1
2
```

那么您可能想看看这篇文章，了解更多关于 JavaScript 范围解析和 ES6 引入的新的很酷的变化。

# 什么是范围？

范围是代码中与变量相关的空间。我们可以把变量和函数看作有生命的生物，把作用域看作它们的栖息地。

# ES6 之前的 JavaScript 范围

在 ES6 之前，变量的最小作用域单位是函数。换句话说，变量的范围可以是最近的包含函数，也可以是全局范围。

使用*变量*的示例:

```
var x = "hello world"; // scope of x: global
function foo() {
  var a = 1;  // scope of a: foo
  if(a > 0) {
    var b = 3; // scope of b: foo
  }
  {
    var e = 10; // scope of e: foo
  }
  while(a < 2) {
    var d = 5; // scope of d: foo
  }
}
```

# const 和 let 关键字以及块范围的引入

自从引入了 *const* 和 *let* 关键字后，变量可以拥有的最小范围单位现在是*块范围。*事实上， *const* 和 *let* 在作用域上的行为完全相同:如果变量是用 *const* 或 *let* 声明的，那么变量可以存在的最小作用域单位就是一个由花括号{}包围的代码块。

前一个带有*的例子让:*

```
let x = "hello world"; // scope of x: global
function foo() {
  let a = 1;  // scope of a: foo
  if(a > 0) {
    let b = 3; // scope of b: if statement
  }
  {
    let e = 10; // scope of e: surrounding block
  }
  while(a < 2) {
    let d = 5; // scope of d: while loop
  }
}
```

使用*常量*:

```
const x = "hello world"; // scope of x: global
function foo() {
  const a = 1;  // scope of a: foo
  if(a > 0) {
    const b = 3; // scope of b: if statement
  }
  {
    const e = 10; // scope of e: surrounding block
  }
  while(a < 2) {
    const d = 5; // scope of d: while loop
  }
}
```

# 一个美丽的例子

## 带 var

```
for(var i=0; i<3; i++){
      setTimeout( function foo() { console.log(i) }, 100);
}
```

这是一个 for 循环，变量 *i* 从 0 迭代到 2。在每次迭代中，用回调函数调用 setTimeout，该函数在 100 毫秒后输出 *i* 的值。

虽然我们很自然地期望这个输出:

```
0
1
2
```

，运行这段代码会奇怪地输出:

```
3
3
3
```

## 刚刚发生了什么？

为了理解刚才发生了什么，我们可以分别表示循环的每一次迭代，并在执行迭代之前标记 *i* 的值:

迭代#1

```
{ // i = 0

      setTimeout( function foo() { console.log(i) }, 100); 
}
```

步骤 1-100 毫秒后，引擎已经完成了循环的执行，现在全局作用域中的 *i* 是 3。然后，引擎继续执行回调函数 foo 中的代码。

步骤 2-引擎查看是否在函数 foo 中声明了 *i* ,但没有找到。

步骤 3-引擎进行查找，查看是否在迭代的包含块中声明了*I*with**let**或 **const** ，但没有找到。

步骤 4-引擎进行查找，看看 *i* 是否在全局范围内声明，并发现它是 3。

步骤 5-因此输出将是 3

迭代#2

```
{ // i = 1

      setTimeout( function foo() { console.log(i) }, 100); 
}
```

引擎重复上一次迭代中解释的 5 个步骤，输出为 3。

迭代#3

```
{ // i = 2

      setTimeout( function foo() { console.log(i) }, 100); 
}
```

引擎重复前面迭代中解释的 5 个步骤，输出为 3。

## 同样的例子有*让*

现在让我们看一个同样的例子，用*让*关键字。

```
for(let i=0; i<3; i++){
      setTimeout( function foo() { console.log(i) }, 100);
}
```

执行此代码会产生以下输出:

```
0
1
2
```

为了理解为什么，让我们以同样的方式跟踪执行:

迭代#1

```
{ // i = 0

      setTimeout( function foo() { console.log(i) }, 100); 
}
```

步骤 1-100 毫秒后，引擎已经完成了循环的执行，现在全局范围内的 *i* 是 3。然后，引擎继续执行回调函数 foo 中的代码。

步骤 2-引擎查看是否在函数 foo 中声明了 *i* ，但没有找到。

步骤 3-引擎进行查找，查看是否在迭代的包含块内声明了 *i* 和 **let** 或 **const** ，并且发现它等于 0。

步骤 4-因此输出将为 0。

迭代#2

```
{ // i = 1

      setTimeout( function foo() { console.log(i) }, 100); 
}
```

引擎重复前一次迭代中的 4 个步骤，输出为 1。

迭代#3

```
{ // i = 2

      setTimeout( function foo() { console.log(i) }, 100); 
}
```

引擎重复前面迭代中的 4 个步骤，输出为 2。

# 最后的想法

在学习任何新的编程语言时，范围解析无疑是一个需要掌握的概念。尽管人们看不到它的直接用途，但理解范围解析和 ES6 带来的变化在以下方面非常有帮助:

*   通过避免由于不理解范围而导致的不必要的错误来提高效率。
*   更快地调试代码，并准确地知道在哪里进行调查。
*   提高代码质量。