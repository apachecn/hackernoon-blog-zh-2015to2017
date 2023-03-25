# matreshka . js 2—TL；博士；医生

> 原文：<https://medium.com/hackernoon/matreshka-js-2-tl-dr-dad0e941201a>

![](img/873c18dccd8e5af18402e8dac3d39631.png)

*网址:*[*matreshka . io*](https://matreshka.io/?c) *示例:*[*Github/matreshkajs/Examples-and-tutorials*](https://github.com/matreshkajs/examples-and-tutorials) *关于 Github:*[*Github/matreshkajs/matreshkajs*](https://github.com/matreshkajs/matreshka)

提醒一下:Matreshka.js 是单页面应用程序的替代 JavaScript 框架。

# 主要功能

函数将一个对象的属性绑定到 HTML 节点。

```
**const** object = { name: 'Brendan' };
**const** node = document.querySelector('.name'); Matreshka.bindNode(object, 'name', node);
object.name = 'Doug';
```

属性改变也会改变绑定节点，反之亦然(例如，如果用户键入某些内容)。js 处理所有现成的 HTML 表单元素。文档中的[描述了自定义元素绑定。](https://matreshka.io/#!Matreshka-bindNode)

**calc** 函数使一个属性依赖于其他属性。

```
**Matreshka**.calc(**object**, 'fullName**'**, ['firstName', 'lastName'], (firstName, lastName) **=>** {
    **return** `${firstName} ${lastName}`
});**object**.firstName = 'Brendan';
**object**.lastName = 'Eich';
*// …* console.log(**object**.fullName); *// “Brendan Eich”*
```

当源属性发生更改(*名字*或*姓氏*)时，目标属性(*全名*)会重新计算。

**calc** 和 **bindNode** 使得定义长的依赖链成为可能:属性 *a* 依赖于元素 *e1* ，属性 *b* 依赖于属性 *a* ，属性 *c* 依赖于元素 *e2* 和 *e3* 以及属性 *b* 因此，属性 *b* 的改变也会改变 *e1* 、 *e2* 、 *e3* 等等…

你可以把这种依赖想象成一个 Excell 表。你每时每刻都在想一个公式，而不是所有细胞的众多依赖关系。因此，您得到的 bug 更少，因为没有必要把所有的“表”(应用程序)逻辑都记在脑子里。在[文档](https://matreshka.io/#!Matreshka-calc)中，你会找到更多关于**计算**的信息。

功能上的**监听事件，而**触发器**功能触发它们。**

```
**Matreshka**.on(**object**, 'something', () => {
  alert('something is happened')
});**Matreshka**.trigger(**object**, 'something');
```

上的**函数可以监听属性改变事件来执行自定义代码。通过这种方式，您可以向依赖链添加另一个东西(例如 fetch request)。**

```
**const** { on } = **Matreshka**;on(**object**, 'change:fullName', **async** () => {
  **await** fetch('/api/name', { method: 'post', body: this.fullName });
  // …
});
```

Matreshka.js 的活动是一个广泛而有趣的话题。有了它们，可以监听对象的委托事件(例如，监听对象树深处的变化)、DOM 事件、委托的 DOM 事件等。你可以在[“事件”](https://github.com/matreshkajs/examples-and-tutorials/tree/master/events)文章中读到他们。

**调解**功能增加了运行时验证和属性值转换。例如，您可以定义一个确定类型的属性，您可以将属性限制在一个指定的范围内，如果需要，您可以抛出一个异常。

```
**Matreshka**.mediate(object, 'x', x => String(x));
object.x = 42;
console.log(object.x, **typeof** object.x); *// “42”, “string”*
```

# **级**

**Matreshka** 类实例具有相同的方法集，但有一个不同之处:**此**用于代替对象参数。

```
**class** **User** **extends** **Matreshka** {
  constructor() {
    **super**();
    **this**.bindNode('fullName', '.full-name');
    **this**.calc('fullName', ['firstName', 'lastName'],
      (firstName, lastName) => `${firstName} ${lastName}`);
    *// also chained call can be used there:
    // super().bindNode(...).calc(...);* }
}
```

本课程将课程 **Matreshka 结合在一起。对象**、 **Matreshka。数组**如下所述，使它们有一个共同的方法集。

类 **Matreshka。对象**负责数据的键值类型。在 Matreshka.js 术语中，“数据”表示开发人员可能想要发送到本地数据库的服务器或存储中的属性。因此，框架需要知道哪些属性负责数据(例如用户名)，哪些负责临时应用程序状态(例如 DOM 元素是否隐藏)。此类属性由**add datakey**方法定义。

```
**class** **User** **extends** **Matreshka**.**Object** {
  constructor() {
    **super**();
    **this**.firstName = 'Brendan';
    **this**.lastName = 'Eich';
    **this**.language = 'JavaScript';
    **this**.addDataKeys(['firstName', 'lastName']); *// '{“firstName”: “Brendan”, “lastName”: “Eich”}'* 
    console.log(**JSON**.stringify(**this**));
  }
}
```

关于 **Matreshka 更详细。对象**类你可以在那里读到[。](https://github.com/matreshkajs/examples-and-tutorials/tree/master/matreshka-object)

**Matreshka。Array** 负责框架中类似数组的集合。除了从 **Matreshka** 继承的方法外，还有 **Matreshka 的实例。Array** 包含本机 Array 的所有方法(推送、拼接、映射、缩减……)。

类似于主干网。集合中，可以定义一个"模型"(Model**)属性。区别在于它可以是任何 JavaScript 类。**

```
**class** **Users** **extends** **Matreshka**.**Array** {
  get **Model**() { **return** **User**; }
  constructor() {
    **super**();
    **this**.push({ firstName: 'Brendan', lastName: 'Eich' }); 
    console.log(**this**[0] instanceof **User**); *// true* }
}
```

当集合的项目更改时，它可以自动呈现。为此，您需要设置在何处插入新创建的 DOM 元素以及如何呈现它们。要设置呈现元素的位置，需要将容器元素绑定到**沙箱**或**容器**属性(区别在文档中描述)。要声明如何渲染 DOM 元素，您应该定义虚拟方法 **itemRenderer** (或 **renderer** 用于**模型**类)。

```
**class** **Foo** **extends** **Matreshka**.**Array** {
  itemRenderer(){ 
    return '<div>Hello, world!</div>'
  } constructor() {
    **super**();
    **this**.bindNode('sandbox', '.sandbox-node');
    *// inserts <div>Hello, world!</div> to .sandbox-node*
    **this**.push({});
  }
}
```

你可以在那里阅读更详细的关于**玛特莱什卡的信息。阵**级。

所有函数和类都可以作为 CJS/ES2015 模块导入。

```
**import** calc **from** 'matreshka/calc';
**import** MatreshkaArray **from** 'matreshka/array';
```

# 按指定路线发送

一个名为[的库负责路由。它将属性绑定到页面地址的部分。](https://github.com/matreshkajs/matreshka-router)

```
**Matreshka**.initRouter(**object**, ‘**/a/b/c/**’);
**object**.**a** = 'foo';
**object**.**b** = 'bar';
**object**.**c** = 'baz'; *// makes location.hash to be #!/foo/bar/baz/*
```

# 结论

Matreshka.js 填补了初级和高级之间的空白，允许在 web 开发人员职业生涯的开始编写模块化和可扩展的应用程序。开始之前你需要知道的一切都是 JavaScript。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客们开始他们下午的方式。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受提交](http://bit.ly/hackernoonsubmission)并很高兴[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果您喜欢这个故事，我们建议您阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实视为理所当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)