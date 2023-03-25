# [ Javascript ]破解坚果，覆盖 Object.constructor()

> 原文：<https://medium.com/hackernoon/javascript-cracking-nuts-override-object-constructor-48a73628b7e6>

![](img/532ae0c6fc557afeca1de6e8d03a0598.png)

unsplash.com

> 我一如既往地喜欢 javascript。但是每次当我被要求用 OOP 模式编程时，我都很受伤。所有这些主题:对象、类、原型、继承等等。，是开发者的痛，这是 JS“比较散漫的脾气”和“自动类型转换强迫症”的个性造成的。

**这是一个来自**[***stack overflow***](http://stackoverflow.com/questions/9267157/why-is-it-impossible-to-change-constructor-function-from-prototype)***:***的例子

第一，兔子是一个对象，具有公共有价值的 ***跳跃******默认值【是】*** 。

```
function Rabbit() {
 ***this.jumps = "yes";*** };
var rabbit = new Rabbit();
console.log(rabbit.jumps);                    // yes
console.log(Rabbit.prototype.constructor);    
// outputs exactly the code of the function Rabbit();
```

第二，我们要更改 Rabbit()中的代码，使 var ***跳转*** 与不同的 ***默认值“no”。*** 嗯，修改对象的默认值，有 ***对象*** 。***【prototype . constructor()***，直觉告诉我修改构造函数，问题就解决了。 ***但*** 却不是。

```
Rabbit.prototype.constructor = function Rabbit() {
 ***this.jumps = "no";*** };
console.log(Rabbit.prototype.constructor);    
// again outputs the code with new this.jumps = "no";
var rabbit2 = new Rabbit();
// create new object with new constructor
***console.log(rabbit2.jumps);                   // still*** ***yes***
```

## 发生的事情是 Rabbit.prototype.constructor 只是一个指向原始构造函数(函数 Rabit(){…})的指针，因此“类”的用户可以从实例中检测到构造函数。

由[胡安](http://stackoverflow.com/users/227299/juan-mendes)

如果你想重定义一个构造函数，就这么做

```
var oldProto = Rabbit.prototype;
Rabbit = function() {
   this.jumps = "no";
};Rabbit.prototype = oldProto;
```

## 你也喜欢坚果系列:

[【express js】override RES . send](https://hackernoon.com/nodejs-express-js-manipulating-response-before-going-back-to-user-5e96ad8d84ca#.zf7rx3el8)
[【express js】将 IP 放入黑名单](/@peterchang_82818/expressjs-cracking-nuts-black-list-ip-4787a0850e49#.bxj93og4k)

喜欢这个故事？对别人有帮助吗？这有助于我知道你是否想看到更多关于他的主题，并帮助人们看到这个故事， ***当点击下面的心*** 。

## *参考:*

***为什么不能从原型改变构造函数？*** [http://js-bits . blogspot . tw/2010/08/JavaScript-inheritance-done-right . html](http://js-bits.blogspot.tw/2010/08/javascript-inheritance-done-right.html)

***2010 年 8 月 17 日星期二 Javascript 继承完成正确*** [http://stack overflow . com/questions/9267157/why-is-it-impossible-to-change-constructor-function-from-prototype](http://stackoverflow.com/questions/9267157/why-is-it-impossible-to-change-constructor-function-from-prototype)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)