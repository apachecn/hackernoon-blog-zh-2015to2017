# var，let，还是 const？

> 原文：<https://medium.com/hackernoon/js-var-let-or-const-67e51dbb716f>

## ES6 简介

你必须理解`var`才能领会`let` / `const`的好处。让我们倒回去。

# 复习:变量声明

有意识地在特定的**范围内**声明**变量，使用`var`声明**，以保持代码的清晰和可维护性，这一点很重要。

```
var x = "outside";
function foo() {
  var x = "inside";
  console.log(x);
}foo(); // inside
console.log(x); // outside
```

上面的代码使用`var`在函数内外正确地声明了`x`。`foo`里没有`var`会怎么样？

```
var x = "outside";
function foo() {
  x = "inside";
  console.log(x);
}foo(); // inside
console.log(x); // inside
```

啊哦！函数外的`x`被函数内的`x`覆盖了，因为我们没有指定`x`的作用域仅限于`foo`！

## 吊装最佳实践

在当前作用域的顶部使用`var`声明变量。

好的:

```
console.log('sup')
var i = 0;
```

更好:

```
var i = 0;
console.log('sup')
```

# 复习:吊装

使用`var`声明的变量总是被**提升**到它们的**范围**的顶部。

```
console.log(j); // ReferenceError: j is not definedconsole.log(i); // undefined
var i = 0;
```

**变量** `j`从未被**声明为**，所以我们得到一个错误消息“我从未听说过`j`！”。

`i` *因**吊装**而在测井前* **申报**。解释器是这样执行的:

```
var i;
console.log(i);
i = 0;
```

解释器将**变量声明**移动到**作用域**的顶部。

但是，变量*还没有* **赋值**给`0`。`undefined`说“我知道`i` *存在*，但是我不知道`i` *指向*什么值，因为你没有**把**赋值给任何东西”。

*补充 1:如果* `*var*` [*指的是一个函数*](/@PrintSupWorld/function-hoisting-declarations-vs-expressions-7f4d385a5d7f) *怎么办？
补充 2:提升并不会在物理上“移动”你的代码—* [*MDN*](https://developer.mozilla.org/en-US/docs/Glossary/Hoisting)

# 功能范围

`var`是**函数作用域** : 作用域限于定义它的函数。

```
function foo() {
  var i = 0;
}console.log(i); // ReferenceError: i is not defined
```

`i`只存在于`foo`中，所以我们得到一个错误:“我从未听说过`i`！”。

# 块范围

`var`是*而不是* **块范围的**:范围是*而不是*局限于定义它的块。

```
var i = 0
if (true) {
  var i = 1;
}console.log(i); // 1
```

`i`*是否仍在**内的“全局范围”内阻塞**。`i`的**值**被覆盖，这可能不是本意。*

*![](img/84398d281ca7257dabdff4151f71458a.png)*

# *让*

*`let`变量*是* **块范围的**！具体范围=错误少。*

```
*let i = 0;
if (true) {
  let i = 1;
}console.log(i); // 0*
```

*即使`i`在 **if 块**中被**赋值**到`1`，那个**赋值**是 **local** 到**块**，因此我们的【全局】`i`仍然是`0`。`if`块的作用域独立于全局作用域。*

# *const ==常数*

*`const`限制重写变量。*

```
*const i = 0;
i = 1; // TypeError: Assignment to constant variable.*
```

*`const`甚至不允许你声明一个没有赋值(常量)的变量！*

```
*const i; // SyntaxError: Missing initializer in const declaration*
```

*`const`和`let`一样，是**块作用域**。*

```
*if (true) {
  const i = 0;
}console.log(i); // ReferenceError: i is not defined*
```

*`const`*是否允许变量 [**突变**](/@PrintSupWorld/javascript-mutable-ce855ef455fd)**(JS 中只有对象/数组是可变的)。****

****数组突变:****

```
****const a = [1];
const b = a;
console.log(a === b); // true
b.push(2);
console.log(a === b); // true
console.log(a); /// [ 1, 2 ]****
```

****对象突变:****

```
****const obj = {};
obj.i = 1;
console.log(obj); // { i: 1 }****
```

# ****左/const 提升****

****`let`和`const`申报是*而不是*吊装！****

> ****编辑:技术上来说它们[被吊起](https://stackoverflow.com/questions/31219420/are-variables-declared-with-let-or-const-not-hoisted-in-es6)，但是它们没有被初始化为任何东西(`var`被初始化为`undefined`)。****

```
****console.log(a); // undefined
var a = 2;console.log(b); // Uncaught ReferenceError: b is not definedconsole.log(c); // Uncaught ReferenceError: c is not defined
let c = 2;****
```

****这可以防止变量*声明*放在变量*引用*之后。****

****`isEqualTo5`有一个 bug——它总是返回`true`。****

```
****function isEqualTo5(n) {
  return !(n - five);
  var five = 5;
}
console.log(isEqualTo5(4)); // true****
```

****问题是`five`直到被引用后才被赋值。它*是*声明的，所以当它在 return 语句中被引用时，它的值是`undefined`。****

```
****!(4 - undefined) === !(NaN) === true****
```

****这个 bug 可能很难抓到。`let` / `const`来救援了！****

```
****function isEqualTo5(n) {
  return !(n - five);
  const five = 5;
}
console.log(isEqualTo5(4)); // ReferenceError: five is not defined****
```

****`const`不提升声明→错误:声明前引用→防止 bug。****

****特定范围=更少的错误+更好的可读性/可维护性。****

## ****TLDR: `var` →🗑，`const` 哪里可以🔑，否则`let`也很酷！🕶****