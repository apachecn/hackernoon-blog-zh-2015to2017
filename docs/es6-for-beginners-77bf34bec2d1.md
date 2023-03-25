# 初学者 ES6 第 3 部分

> 原文：<https://medium.com/hackernoon/es6-for-beginners-77bf34bec2d1>

![](img/e998e6f8d19efbce155a16e57bfebdde.png)

ES6 for beginners

> 这篇文章最初发表在我的博客 [Es6 上，面向初学者](https://srebalaji.rocks/es6-for-beginners-part-3/)

文章的第一部分出现了[这里](https://hackernoon.com/es6-for-beginners-f98120b57414)

文章的第二部分出现了[这里](https://hackernoon.com/es6-for-beginners-part-2-ee8a77f7f4c7)

我将在本文中讨论的主题

1.  阵列地图
2.  阵列过滤器
3.  数组缩减
4.  模板文字
5.  进口和出口
6.  析构对象和数组
7.  扩展和超级

**阵列图**

Map 运算符用于对数组中的所有元素执行特定的操作，它返回一个包含修改过的元素的数组。

这很容易实现。让我们看一个例子。

```
let arr = [1,2,3,4,5];let modifiedArr = arr.map(function(element, index, arr) {
  return element * 10;
});
console.log(modifiedArr);Output:
[10, 20, 30, 40, 50]
```

正如你所看到的**图**采用了一个有三个参数的函数。

1.  第一个参数是元素本身。
2.  第二个参数是元素的索引。
3.  第三个参数是整个数组。

还要注意，我们最终必须返回一些值。这将是该元素的修改值。如果你没有返回任何东西，那么特定的元素将是未定义的。

我想补充的另一点是，第二个和第三个参数只是可选的。只有第一个参数是必需的。

```
let modifiedArr = arr.map(function(element) {
  return element * 10;
});
```

正如你所看到的，map 采用了一个只有一个参数的函数。

行..让我们试着用箭头函数来写地图操作符。

```
let modifiedArr = arr.map((element, index) => {
  console.log("index "+index);
  return element * 10;
});
console.log(modifiedArr);Output:
index 0
index 1
index 2
index 3
index 4
[10, 20, 30, 40, 50]
```

我想我不用解释太多。很简单。

最后一个例子。

```
let modifiedArr = arr.map(element => element * 10);
console.log(modifiedArr);
```

酷吧:)

如果你很了解 arrow 函数，应该很容易理解。但是如果你不明白，我会试着解释。

这里有两点需要说明。

1.  如果你有一个单参数的函数，你不需要()。在我们的例子中，**元素**是参数。
2.  如果你用单行作为函数体，你不需要{}，JS 也会在执行函数后隐式返回值。你不必使用**返回**关键字。

**阵列过滤器**

数组过滤器用于根据某种条件过滤整个数组。数组过滤器获取数组中的每个元素，并检查给定的条件。如果元素通过了条件，它将元素保留在数组中，否则它将删除该元素。

让我们看一些例子。

```
let arr = [1, 2, 3, 4, 5, 6]
let modifiedArr = arr.filter(function(element, index, array) {
  return element % 2 == 0
});
console.log(modifiedArr);Output:
[2, 4, 6]
```

正如你在上面的例子中看到的，**过滤器**像**映射**一样有三个参数。

我们必须为数组的每个元素返回一个布尔值。如果你最后没有返回任何布尔值，那么过滤器会把它当作**假**并删除该元素。

行..我们用箭头函数试试。

```
let modifiedAarr = arr.filter((element, index) => element%2 == 0)
```

希望你明白了。别忘了注意，只有第一个参数是强制的。另外两个参数是可选的。

**阵列缩小**

Array reduce 用于聚合数组的所有元素并返回一个值。

让我们看一些例子

```
let arr = [1,2,3,4,5,6]
let total= arr.reduce(function(sum, element, index, array) {
  return sum + element;
},0);
console.log("total is "+total);Output:
total is 21
```

与“过滤”和“映射”不同，reduce 采用一个带有四个参数的函数和一个附加元素。在我们的例子中是 0。

让我们看看它是如何工作的。

第一个参数是聚合器元素。它有任意给定点的元素之和。它的初始值被定义为附加元素。在我们的例子中是 0。

然后，第二、第三和第四个参数与过滤器和地图相同。

像过滤器和地图你必须返回最终结果。

让我们看另一个例子。

```
let arr = [1,2,3,4,5,6];
let totalSum = arr.reduce(function(sum, element, index, array) {
  console.log(sum+"+"+element+"="+sum+element);
  return sum + element;
}, 10);
console.log("Total sum is "+ totalSum);Output:
10 + 1 = 11
11 + 2 = 13
13 + 3 = 16
16 + 4 = 20
20 + 5 = 25
25 + 6 = 31
Total sum is 31
```

希望你得到了这个例子。注意，我已经将聚合器元素 **sum** 的初始值设置为 10。

让我们用箭头函数写同样的代码。

```
let totalSum = arr.reduce((sum, element) => element+sum, 0)
```

希望你明白了。它是直接向前的。

与 filter 和 map 不同，前两个参数是必需的。另外两个是可选的。

这里有一个 [jsfiddle](https://jsfiddle.net/g167zvnu/) 可以玩。:)

**模板文字**

如果您知道其他脚本语言，如 ruby、python，那么模板文字对您来说并不陌生。

它们是较新的语法，使添加表达式变得容易。

模板文字用于执行任何 JS 表达式。

让我们看一些例子

```
let name = "Jon Snow";
let msg = `My name is ${name}`;
console.log(msg);Output:
My name is Jon Snow
```

简单的权利。

也可以有多行字符串。

```
let msg = `My name
is ${name}`;console.log(msg);Output:
My name
is Jon Snow
```

让我们看看其他例子

```
let name = "Srebalaji";
let languages = () => {return "Ruby, Js, Java, Python"}let msg = `My name is ${name}
My age is ${20+3}
And I code in ${languages()}`Output:
My name is Srebalaji
My age is 23
And I code in Ruby, Js, Java, Python
```

希望你明白了。直截了当。

您可以轻松添加任何表达式，也可以添加多行字符串。

**进出口**

在 ES6 中导入和导出模块是您将在现代前端库中看到的有用特性之一。

我强烈推荐在这个 [Plunk](https://plnkr.co/edit/iklRbKgYobAnAW4QtFDX) 中玩转这个功能。环境已经在 plunk 中设置好了。

行..那么在 ES6 中导入和导出是如何工作的呢？

导出在模块中用于显式导出一些变量、函数或类。(即)如果您导出一个变量，它可以在其他模块中使用。

导入用于从其他模块导入变量、函数、类。

如果你不和我在一起。让我们看一些例子。

```
**app.js**
export let name = "Jon"
export let age = 23**index.js**
import {name, age} from './app'
console.log(name);
console.log(age);**index.html**
<script src="./index.js"></script>Output:
Jon
23
```

在上面的例子中，我们定义了两个变量 **name** 和 **age** 并将其导出。

在另一个文件中，我们导入了变量并访问了它们的值。

简单的权利。

让我们深入一点

```
**app.js**
export default const name = "Jon"**index.js**
import name from './app.js'
console.log(name);Output:
Jon
```

在上面的代码中，你可以看到我们使用了一个新的关键字 **default** 。如果需要从模块中导出单个值、函数或对象，通常会使用默认值。并且一个模块中只能有一个默认值。

关于默认值还有一点。因为一个模块中只有一个默认值，所以在导入过程中可以使用任何名称来引用它。

例子

```
import n from './app.js'
console.log(n);Output:
Jon
```

如你所见，我们在这里引用了默认值 **n** 。

我们再深入一点。

```
**app.js**
let a = 10;
let b = 2;
let sum = () => a+b;
export {a,b}
export default sum**index.js**
import * as variables from './app'
import addition from './app' // default value
console.log(variables.a);
console.log(variables.b);
console.log(addition());Output:
10
2
12
```

在上面的例子中，你可以看到我们已经导出了两个变量和一个函数。我们已经使用*导入了所有变量。

导入时需要记住两件事。

1.  如果您使用*来导入值，那么您必须使用**别名**(即引用导入值的名称)。在我们的例子中，我们使用了**变量**作为别名。
2.  使用*导入值不会导入默认值。你要单独导入。

```
import addition, * as variables from './app'
```

如果需要在一行中导入默认值和其他值，可以使用上面的语法。

希望你明白了。:)

**析构对象和数组**

析构是 ES6 中有用的特性之一。而且用起来很简单。

让我们举一些例子。

```
let person = {firstName: "Jon", lastName: "Snow", age: 23}const {firstName, lastName, age} = personconsole.log(firstName);
console.log(lastName);
console.log(age);Output:
Jon
Snow
23
```

在上面的代码中，你可以看到我们有一个带有多个键的对象 **person** 。

我们已经创建了三个变量 firstName，lastName，age(与对象键相同。)从对象本身。

简单地说，我们通过从对象中提取键创建了三个变量。

让我们看一些其他的例子

```
let person = {firstName: "Jon", lastName: "Snow", age: 23}const {firstName} = person
console.log(firstName);Output:
Jon
```

在上面的例子中，您可以看到我们只从对象中提取了所需的值。

```
let person = {firstName: "Jon", lastName: "Snow", age: 23}const {firstName: name, age} = person
console.log(name);
console.log(age);Output:
Jon
23
```

在上面的例子中，你可以看到我们已经定义了一个新的变量 **name** ，它被赋予了 **firstName** 。

希望你明白了。很简单。

让我们看看如何解构数组。

```
let arr [1, 2, 3, 4]const [a, b, c, d] = arr;console.log(a);
console.log(b);
console.log(c);
console.log(d);Output:
1
2
3
4
```

希望你得到了上面的代码。很简单。

我们将数组中的每个元素赋给一个变量。

让我们看另一个例子。

```
let arr = [1,2,3,4,5,6]let [a,b,,d,e] = arrconsole.log(a);
console.log(b);
console.log(d);
console.log(e);Output:
1
2
4
5
```

在上面的代码中，你可以看到我们跳过了数组的第三个元素。除此之外，一切都和前面的例子一样。

让我们看另一个例子。

```
let person = {firstName: "Jon", lastName: "Snow", age: 23}let displayName = ({firstName, lastName:last}) => {
  console.log(`${firstName} - ${last}`);
}displayName(person);Output:
Jon - Snow
```

希望你明白了。直截了当。

**扩展和超级**

如果你有编写 OOPS 的经验，那么 extend 和 super 对你来说并不陌生。

**扩展**用于从主类创建子类。子类继承主类的所有属性，也可以修改主类的属性。

```
class Person{
 constructor(firstName, lastName, age) {
   this.firstName = firstName;
   this.lastName = lastName;
   this.age = age;
 }
 displayName() {
  return `${this.firstName} - ${this.lastName}`;
 }
}class Employee extends Person {
 constructor(firstName, lastName, age, salary) {
  super(firstName, lastName, age);
  this.salary = salary;
 }
 displaySalary() {
  return `${this.salary}`;
 }
 displayName() {
  return super.displayName();
 }
 displayAge() {
  return this.age;
 }}let manager = new Employee("Jon", "Snow", 23, 100);
console.log(manager.displaySalary());
console.log(manager.displayName());
console.log(manager.displayAge());Output:
100
Jon Snow
23
```

在上面的代码中，你可以看到我们用一个构造函数和一个简单的方法定义了一个类 **Person** 。

然后我们定义了另一个类**雇员**，它是从**人**继承的子类。我们使用了**扩展**来实现这一点。希望你明白这一点。

然后我们使用了 **super** 关键字来调用父类的构造函数。我们还使用 **super** 调用了父类中声明的方法。

**注意:**调用**超级**后才能在子类中使用**这个**。如果你在子类中调用 **super** 之前使用 **this** ，你将得到**reference error**。

因此，我们在上面的代码中实现了三件事

1.  我们使用**扩展**从父类创建一个子类。
2.  我们使用了 **super** 来调用父类的构造函数。
3.  我们使用了 **super** 来调用父类中定义的方法。

希望你收到了:)

这里有一把可以玩的小提琴。

如果你喜欢这篇文章，试着给点掌声并分享它:)

下面是我写的关于 ES6 的相关文章

[https://hackernoon.com/es6-for-beginners-f98120b57414](https://hackernoon.com/es6-for-beginners-f98120b57414)

[https://hacker noon . com/es6-适合初学者-part-2-ee8a77f7f4c7](https://hackernoon.com/es6-for-beginners-part-2-ee8a77f7f4c7)