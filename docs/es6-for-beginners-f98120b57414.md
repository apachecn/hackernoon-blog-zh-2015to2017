# 面向初学者的 ES6

> 原文：<https://medium.com/hackernoon/es6-for-beginners-f98120b57414>

![](img/33b0247847d0c0087722b20beadeaf82.png)

ES6 for beginners

> 这篇文章最初发表在我的博客 [Es6 上，面向初学者](https://srebalaji.rocks/es6-for-beginners/)

在这篇文章中，我将介绍 ES6 的一些新特性。如果你是 ES6 新手或者学习前端框架的话会很有帮助。

我将在这篇文章中讨论的话题

1.  让和 Const
2.  箭头功能
3.  默认参数
4.  for of 循环
5.  传播属性
6.  地图
7.  设置
8.  静态方法
9.  Getters 和 Setters

**让**

let 类似于 var，但是 let 有作用域。let 只能在它所定义的块级中访问。

```
if (true) {
 let a = 40;
 console.log(a); //40
}
console.log(a); // undefined
```

在上面的例子中，变量“a”是在 If 语句中定义的，所以在函数外部是不可访问的。

另一个例子:

```
let a = 50;
let b = 100;
if (true) {
 let a = 60;
 var c = 10;
 console.log(a/c); // 6
 console.log(b/c); // 10
}
console.log(c); // 10
console.log(a); // 50
```

**常数**

Const 用于给变量赋一个常数值。并且该值不可更改。已经修好了。

```
const a = 50;
a = 60; // shows error. You cannot change the value of const.const b = "Constant variable";
b = "Assigning new value"; // shows error.
```

考虑另一个例子。

```
const LANGUAGES = ['Js', 'Ruby', 'Python', 'Go'];LANGUAGES = "Javascript"; // shows error.LANGUAGES.push('Java'); // Works fine.
console.log(LANGUAGES); // ['Js', 'Ruby', 'Python', 'Go', 'Java']
```

这可能有点令人困惑。

这样考虑。每当定义一个常量变量时，Javascript 都会引用该变量的值的地址。在我们的例子中，变量“语言”实际上是指分配给数组的内存。所以你不能改变这个变量来引用其他的内存位置。在整个程序中，它只引用数组。

**箭头功能**

ES6 中的功能有一点改变。我指的是语法。

```
// Old Syntax
function oldOne() {
 console.log("Hello World..!");
}// New Syntaxvar newOne = () => {
 console.log("Hello World..!");
}
```

新的语法可能会有点混乱。但我会试着解释语法。

语法有两个部分。

第一部分只是声明一个变量，并为它分配函数(即())。它只是说变量实际上是一个函数。

然后第二部分是声明函数的主体部分。带花括号的箭头部分定义了正文部分。

另一个带参数的例子。

```
let NewOneWithParameters = (a, b) => {
 console.log(a+b); // 30
}
NewOneWithParameters(10, 20);
```

我想我不需要对此做出解释。这很简单。

**默认参数:**

如果您熟悉 Ruby、Python 等其他编程语言，那么默认参数对您来说并不陌生。

默认参数是在声明函数时默认给出的参数。但是它的值可以在调用函数时改变。

例子

```
let Func = (a, b = 10) => {
 return a + b; 
}
Func(20); // 20 + 10 = 30
```

在上面的例子中，我们只传递一个参数。该函数使用默认参数并执行该函数。

考虑另一个例子:

```
Func(20, 50); // 20 + 50 = 70
```

在上面的示例中，该函数采用两个参数，第二个参数替换默认参数。

考虑另一个例子:

```
let NotWorkingFunction = (a = 10, b) => {
 return a + b;
}
NotWorkingFunction(20); // NAN. Not gonna work.
```

当你调用带有参数的函数时，它们会按顺序被赋值。(即，第一个值赋给第一个参数，第二个值赋给第二个参数，依此类推)..

在上面的例子中，值 20 被赋给参数‘a ’,而‘b’没有任何值。所以我们得不到任何输出。

但是，

```
NotWorkingFunction(20, 30); // 50;
```

工作正常。

**For of 循环**

为..的与的非常相似..稍加修改。

为..of 遍历元素列表(例如 Array)并逐个返回元素(不是它们的索引)。

```
let arr = [2,3,4,1];
for (let value of arr) {
 console.log(value);
}Output:
2
3
4
1
```

请注意，变量“value”输出数组中的每个元素，而不是索引。

另一个例子

```
let string = "Javascript";
for (let char of string) {
 console.log(char);
}Output:
J
a
v
a
s
c
r
i
p
t
```

是的。它也适用于字符串。

**传播属性**

顾名思义，传播属性有助于传播表达式。简单地说，它将元素列表转换为数组，反之亦然。

没有跨页属性的示例:

```
let SumElements = (arr) => {
 console.log(arr); // [10, 20, 40, 60, 90]let sum = 0;
 for (let element of arr) {
 sum += element;
 }
 console.log(sum); // 220\. 
}SumElements([10, 20, 40, 60, 90]);
```

上面的例子很简单。我们声明一个函数来接受数组作为参数并返回它的和。很简单。

现在，考虑具有传播属性的相同示例

```
let SumElements = (...arr) => {
 console.log(arr); // [10, 20, 40, 60, 90]let sum = 0;
 for (let element of arr) {
 sum += element;
 }
 console.log(sum); // 220\. 
}SumElements(10, 20, 40, 60, 90); // Note we are not passing array here. Instead we are passing the elements as arguments.
```

在上面的例子中，spread 属性将元素列表(即参数)转换为一个数组。

另一个例子:

```
Math.max(10, 20, 60, 100, 50, 200); // returns 200.
```

Math.max 是一个简单的方法，从给定的列表中返回最大的元素。它不接受数组。

```
let arr = [10, 20, 60];
Math.max(arr); // Shows error. Doesn't accept an array.
```

所以让我们用我们的救世主。

```
let arr = [10, 20, 60];
Math.max(...arr); // 60
```

在上面的示例中，spread 属性将数组转换为元素列表。

**地图**

Map 保存键值对。它类似于一个数组，但是我们可以定义自己的索引。而索引在地图中是唯一的。

示例:

```
var NewMap = new Map();
NewMap.set('name', 'John'); 
NewMap.set('id', 2345796);
NewMap.set('interest', ['js', 'ruby', 'python']);NewMap.get('name'); // John
NewMap.get('id'); // 2345796
NewMap.get('interest'); // ['js', 'ruby', 'python']
```

我认为上面的例子是不言自明的。

地图的其他有趣特性是所有索引都是唯一的。我们可以用任何值作为键或值。

示例:

```
var map = new Map();
map.set('name', 'John');
map.set('name', 'Andy');
map.set(1, 'number one');
map.set(NaN, 'No value');map.get('name'); // Andy. Note John is replaced by Andy.
map.get(1); // number one
map.get(NaN); // No value
```

地图中使用的其他有用方法:

```
var map = new Map();
map.set('name', 'John');
map.set('id', 10);map.size; // 2\. Returns the size of the map.map.keys(); // outputs only the keys. 
map.values(); // outputs only the values.for (let key of map.keys()) {
 console.log(key);
}Output:
name
id
```

在上面的例子中，map.keys()返回映射的键，但是它是在迭代器对象中返回的。意思是不能照原样展示。它应该只通过迭代来显示。

另一个例子:

```
var map = new Map();for (let element of map) {
 console.log(element);
}Output:
['name', 'John']
['id', 10]
```

上面的例子不言自明。森林..of 循环输出数组中的键值对。

我们可以稍微优化一下。

```
var map = new Map();for (let [key, value] of map) {
 console.log(key+" - "+value);
}Output:
name - John
id - 10
```

**集**

集合用于存储任何类型的唯一值。简单的..！

例子

```
var sets = new Set();
sets.add('a');
sets.add('b');
sets.add('a'); // We are adding duplicate value.for (let element of sets) {
 console.log(element);
}Output:
a
b
```

请注意，不会显示重复的值。将显示唯一的值。

还要注意集合是可迭代的对象。我们必须遍历元素来显示它。

其他有用的方法:

```
var sets = New Set([1,5,6,8,9]);sets.size; // returns 5\. Size of the set.
sets.has(1); // returns true. 
sets.has(10); // returns false.
```

在上面的例子中，大小是不言自明的。还有另一个方法“has ”,它根据给定元素是否出现在集合中返回一个布尔值。

**静态方法**

你们大多数人已经听说过静态方法。ES6 中引入了静态方法。定义和使用它非常容易。

示例:

```
class Example {
 static Callme() {
 console.log("Static method");
 }
}
Example.Callme();Output:
Static method
```

注意，我没有在类中使用关键字“function”。

我可以调用这个函数，而不用为这个类创建任何实例。

**吸气器和设置器**

Getters 和 setters 以及 ES6 中引入的有用特性之一。如果你在 JS 中使用类，它会派上用场。

没有 getters 和 setters 的示例:

```
class People {constructor(name) {
 this.name = name;
 }
 getName() {
 return this.name;
 }
 setName(name) {
 this.name = name;
 }
}let person = new People("Jon Snow");
console.log(person.getName());
person.setName("Dany");
console.log(person.getName());Output:
Jon Snow
Dany
```

我认为上面的例子不言自明。我们在 People 类中有两个函数，帮助设置和获取人名。

getters 和 setters 的例子

```
class People {constructor(name) {
 this.name = name;
 }
 get Name() {
 return this.name;
 }
 set Name(name) {
 this.name = name;
 }
}let person = new People("Jon Snow");
console.log(person.Name);
person.Name = "Dany";
console.log(person.Name);
```

在上面的例子中，你可以看到在类 People 中有两个函数，分别具有“get”和“set”属性。“get”属性用于获取变量的值,“set”属性用于设置变量的值。

你可以看到调用 getName 函数时没有括号。调用 setName 函数时不带括号，就像给变量赋值一样。

谢谢你的时间。希望你喜欢这篇文章。:) :)

我已经在第二部分讲述了**承诺**和**异步/等待**。仔细讨论

[](https://hackernoon.com/es6-for-beginners-part-2-ee8a77f7f4c7) [## 面向初学者的 ES6 第 2 部分

### 本文涵盖了 ES6 的一些特性，比如承诺、异步/等待

hackernoon.com](https://hackernoon.com/es6-for-beginners-part-2-ee8a77f7f4c7) 

在下一部分中，我将讨论更有趣的主题，比如数组映射、数组过滤、归约、导入/导出、析构等。仔细讨论

[](https://hackernoon.com/es6-for-beginners-77bf34bec2d1) [## 初学者 ES6 第 3 部分

### ES6 数组过滤器、数组映射、数组缩减、模板文字、导入和导出、析构对象和数组…

hackernoon.com](https://hackernoon.com/es6-for-beginners-77bf34bec2d1) 

*原载于 2017 年 9 月 29 日*[*medium.com*](/@srebalaji/es6-tutorial-for-beginners-5f3c4e7960be)*。*