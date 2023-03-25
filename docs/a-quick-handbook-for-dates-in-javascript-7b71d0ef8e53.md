# JavaScript 日期快速手册

> 原文：<https://medium.com/hackernoon/a-quick-handbook-for-dates-in-javascript-7b71d0ef8e53>

## JavaScript 有一个内置的表示时间的 **Date** 对象。怎么用，什么时候有用？

![](img/93d06592ec57e4059ff0b1c89105a9be.png)

Han Chau — unsplash.com

假设您正在构建一个显示您所在地区所有公共汽车和火车时刻表的应用程序。用户可以选择一个[旅程](https://hackernoon.com/tagged/journey)，并且你希望能够告诉他他的旅程需要多长时间。要做到这一点，你必须用到达时间减去离开时间。

简单地使用小时的书面表达是不会有效的:你将如何计算晚上 8 点出发、第二天早上 7 点 58 分到达的夜班火车的行程？你将如何处理 20 点 50 分从尼斯出发、近两天后到达莫斯科的东方快车？

你必须拥有**这两个特定时间点的唯一表示**，这就是日期对象的用武之地。

![](img/3593544ae3bd124f334ff649a4509d49.png)

Ugur Akdemir — unsplash.com

**Javascript 中的 Date 对象存储每个日期的唯一表示**:一个非常大的数字，它保存了从 1970 年 1 月 1 日 00:00:00 世界时(UTC)开始的毫秒数，也称为纪元时间。客观地说，一天包含 86，400，000 毫秒…

计算机不是理解日期和时间的概念，而是简单地记录自纪元时间以来的每一个时间点。在 [Javascript](https://hackernoon.com/tagged/javascript) 中，当请求当前日期时，它被返回为自 1970 年 1 月 1 日 00:00:00 以来经过的毫秒数。

> 1970 年 1 月 1 日是一切开始的时候

Javascript 使您能够创建在特定时间和日期初始化的 Date 对象，或者简单地反映运行浏览器的计算机上的当前系统时间。

## **使用 Javascript 日期对象**时，需要注意两件重要的事情:

*   当从浏览器反映日期和时间时*你完全绑定到用户计算机的日期和时间*:如果用户没有在他的计算机上正确设置日期，你的日期对象将反映这些错误的设置。
*   虽然您可以读取用户计算机上设置的系统日期和时间，并根据您从计算机中读取的日期在 date 对象中设置您自己的更改，但是您*不能*更改计算机的系统日期和时间:用户的系统日期和时间将始终保持不变。

在处理表示特定日期的 Date 对象的实例时，有许多方法允许您对其进行操作。大多数方法只允许您获取和设置对象的年、月、日、小时、分钟、秒和毫秒字段。但是在本文中，我们将只关注日期对象的*创建*阶段。

## 如何构造日期对象？

Javascript Date 对象的创建与 Javascript 中的其他对象非常相似:使用 *new* 关键字和一些可选参数来初始化 Date 对象的实例(您自己的副本)。

创建 Date()对象的语法如下:

```
var myDate = new Date(optional parameters);
```

# 很高兴知道

*   与输入格式无关，默认情况下，Javascript 将根据您所在的时区以全文字符串格式输出日期，例如:

```
Fri Sep 02 2016 17:24:56 GMT+0200 (CEST)
```

*   当将正确的参数传递给日期对象构造函数时，您将获得相对于纪元时间的相应时间点的表示。然而，如前所述，**计算机日期将与您的时区相关，javascript Date 对象使用您计算机上设置的日期来输出其结果**。

一旦创建了 date 对象，就有两种方法来构造想要反映的日期:**将参数传递给创建的 Date 对象实例**，或者**使用 Javascript Date 对象**固有的 set 方法。

让我们在这里讨论如何实现这两个方法。

# I .向日期对象的实例传递参数

Javascript 提供了四种创建日期对象的方法:

**1/不传递任何参数，这将生成当前日期，精确到毫秒**

```
var today = new Date();
// Prints Sun Jun 18 2017 17:24:56 GMT+0200 (CEST)
```

如果没有提供参数，它将为当前日期和时间创建一个对象。如果不传递参数，将显示我的浏览器根据我的计算机设置解释的当前日期。

**2/传递对应于从纪元时间**开始的毫秒数的数字

```
var  milliseconds = new Date(5000); 
// Prints Thu Jan 01 1970 01:00:05 GMT+0100 (CET)
```

传递参数 5000 将创建一个表示 1970 年 1 月 1 日午夜过 5 秒的日期。

**3/传递一个具有特定格式的字符串，该字符串将被解释为日期**

```
var dateAsString = new Date(dateString);
```

日期对象一般有四种类型的 Javascript 字符串输入格式**:**

## **ISO 日期(国际标准)，写作:“2016–09–02”**。

ISO 8601 是表示日期和时间的国际标准。在 Javascript 中将字符串传递给日期对象时，这是首选的日期格式。

示例:

→完整日期(年-月-日)

```
var dateISO = new Date(“2016–03–25”); 
// Prints Fri Mar 25 2016 01:00:00 GMT+0100 (CET)
```

→年和月(YYYY-MM)

```
var dateISO = new Date("2016-03"); 
// Prints Tue Mar 01 2016 01:00:00 GMT+0100 (CET)
```

→年份(年)

```
var dateISO = new Date("2016");
// Prints Fri Jan 01 2016 01:00:00 GMT+0100 (CET)
```

→加上小时、分钟和秒钟(YYYY-MM-DDTHH:MM:SS)

```
var dateISO = new Date("2016-03-25T12:00:00");
// Prints Fri Mar 25 2016 13:00:00 GMT+0100 (CET)
```

## 短日期，写为:“2015 年 3 月 25 日”或“2015 年 3 月 25 日”

短日期通常使用“MM/DD/YYYY”语法传递给 Date 对象，但也可以写成“YYYY/MM/DD”。

→“MM/DD/YYYY”格式

```
var dateShort = new Date("03/25/2016");
// Prints Fri Mar 25 2016 00:00:00 GMT+0100 (CET)
```

→“YYYY/MM/DD”格式

```
var dateShort = new Date("2016/03/25");
// Prints Fri Mar 25 2016 00:00:00 GMT+0100 (CET)
```

# 很高兴知道

*   在所有短日期和 ISO 日期格式中，月写在日之前。
*   如果您输入不带前导零的月份和日期数字(例如，用 2 代替 02)，它们仍会被解释并输出正确的日期)

## 长日期，写为:“2016 年 3 月 25 日”或“2016 年 3 月 25 日”。

长日期字符串格式最常用的语法是“日、月、年”。然而，日、月和年的名称可以按任何顺序书写。

月份名称可以用全称(三月)或缩写(三月)书写，并且不区分大小写。

还有一点需要注意:如果在字符串中添加逗号或任何类型的分隔符，它们将被忽略。

```
var date1 = new Date("3 march 2016"); 
var date2 = new Date("3 MARCH, 2016");
var date3 = new Date("2016 3 March");
var date4 = new Date("3 2016 mar");
var date5 = new Date("March 2016 3");
var date6 = new Date("March*2016,3");// All of the above will print Thu Mar 03 2016 00:00:00 GMT+0100 (CET)
```

## 完整日期，写为:“2016 年 03 月 03 日星期四”。

JavaScript 将接受“完整 JavaScript 格式”的日期字符串，这种格式与用于输出日期对象内容的格式相同。

```
var dateFull = new Date("Mon Mar 02 2015 00:00:00 GMT+0100 (CET)");
// Prints Mon Mar 02 2015 00:00:00 GMT+0100 (CET)
```

在我们的例子中，与时区相关的括号(如 CET)是可选的，如果不正确，将被忽略。

# 很高兴知道

## **字符串参数和时区:注意**

例如，当您传递表示日期的字符串时，如果没有指定时区，此日期将被转换为与您所在的时区相匹配。
换句话说:如果日期/时间是以 GMT(格林威治标准时间)创建的，那么如果用户从美国中部浏览，该日期/时间将被转换为 CDT(美国中部夏令时)。

因此，当你通过例如:

```
var newYear = new Date(“2016”);
```

根据您所在的时区，输出可能会在 2015 年 1 2 月 31 日和 2016 年 1 月 1 日之间有所不同。

## Date 对象将字符串参数转换为数字

当一个字符串作为参数传递给 Date 对象时，它会被转换成一个数字(从纪元时间开始的毫秒数)。

如果字符串格式不合法，并且不被 Date 对象接受，则该参数将作为“NaN”(不是数字)传递，并导致“无效日期”错误。

**4/以正确的顺序传递每个参数，用逗号分隔**

```
var d = new Date(year, month, day [,hour,minute,second,millisecond ]);
```

所有这些参数都是整数，每个都代表完整日期 javascript 格式的一部分。

虽然前三个参数(年、月和日)原则上是强制的，但是如果只传递年和月参数，Javascript 可以解释日期。在这种情况下，第三个参数(day)将被解释为等于 1。

最后四个参数(小时、分钟、秒、毫秒)是可选的，如果没有指定，则取 0。当指定了这四个可选参数之一时，还必须指定其左侧的所有其他参数。

→注意:如果给 Date 对象一个参数，该参数将被视为毫秒计数，而不是第一个参数(年份)。

让我们一个接一个地回顾这些参数，看看它们如何被用作 Date 对象的参数:

## **→年份**

请指定完整的年份(例如，使用 1998 年而不是 98 年)。但是，请注意，如果您使用 0 到 99 之间的两位数日期，1900 将被添加到整数中(98 将成为 1998)。

## →月份

month 参数是从零开始的(与 day 参数相反)。因此，该整数将介于 0(1 月)和 11(12 月)之间

## →日

day 参数表示一个月中的某一天。它从 1 开始(与 month 参数相反),整数将介于 1 和 31 之间。

## →小时

hour 参数以 24 小时制表示一天中的小时。该整数将介于 0 和 23 之间。

## →分钟

分钟参数以 60 分钟为刻度。该整数将介于 0 和 59 之间。

## →第二

第二个参数是 60 秒。该整数将介于 0 和 59 之间。

## →毫秒

毫秒参数的刻度为 1000 毫秒。该整数将介于 0 和 999 之间。

```
var theDate = new Date(2016,6,17,21,15,30,0);
// Prints Sun Jul 17 2016 21:15:30 GMT+0200 (CEST)
```

注意，由于月份参数的**零基结构，月份号(6)对应的是七月而不是六月。**

# 二。使用“set”方法在一个日期内设置组件的值

可以在 date 实例上使用的 Date 对象的方法的特点是使用`set`方法来设置日期的特定组成部分。
这些方法允许你为日期的一部分设置值:年、月、日、小时、分钟、秒和毫秒。

例如，让我们创建一个表示当前日期的 Date 对象的实例，并回顾一下我们可以应用于这个新创建的`myDate`变量的 set 方法:

```
var myDate = new Date();
// Prints Sat Sep 03 2016 16:22:20 GMT+0200 (CEST)
```

## → setFullYear()

设置年份(可选月和日)

```
myDate.setFullYear(2001);
// Prints Mon Sep 03 2001 16:28:40 GMT+0200 (CEST)
```

## → setMonth()

设置月份(0–11)

```
myDate.setMonth(2);
// Prints Thu Mar 03 2016 16:33:48 GMT+0100 (CET)
```

## →设置日期()

将一个月中的某一天设置为数字(1–31)

```
myDate.setDate(25);
// Prints Sun Sep 25 2016 16:22:20 GMT+0200 (CEST)
```

## →设置时间()

设置小时(0–23)

```
myDate.setHours(9);
// Prints Sat Sep 03 2016 09:31:23 GMT+0200 (CEST)
```

## →设置分钟数()

设置分钟数(0–59)

```
myDate.setMinutes(1);
// Prints Sat Sep 03 2016 16:01:02 GMT+0200 (CEST)
```

## →设置秒数()

设置秒数(0–59)

```
myDate.setSeconds(30);
// Prints Sat Sep 03 2016 16:36:30 GMT+0200 (CEST)
```

## → setMilliseconds():

设置毫秒数(0–999)

## →设置时间()

以毫秒为单位设置自 1970 年 1 月 1 日以来的时间

# 结论

在 Javascript 中，您有许多选择来构建自己的 Date 对象实例，甚至有更多的方法通过本机内置方法来检索和显示它。

在操作日期时，令人困惑的是用户所处的不同时区的管理，以及对用户的计算机设置缺乏控制。

然而，从博客评论到在线订单再到预订日历，正确使用 Date 对象是构建可信和可靠的 web 应用程序的最重要的 Javascript 组件之一。

想了解更多？查看我关于 JavaScript 基础的其他文章:

*   [**JavaScript 中的吊装:快速指南**](https://hackernoon.com/hoisting-in-javascript-a-quick-guide-cc4d9597bbd7)
*   [**轻松理解 JavaScript 变量作用域**](https://hackernoon.com/understand-javascript-variable-scope-with-ease-221a6d41dc43)
*   [](https://hackernoon.com/get-your-head-around-this-73c23653b102)**搞清楚“这个”**
*   **[**如何放心使用 JavaScript 闭包**](https://hackernoon.com/how-to-use-javascript-closures-with-confidence-85cd1f841a6b)**
*   **[**掌握 JavaScript**](https://hackernoon.com/grasp-by-value-and-by-reference-in-javascript-7ed75efa1293) 中的“按值”和“按引用”**
*   **[**像老板一样使用 JavaScript 数组**](/@lenafaure/work-with-javascript-arrays-like-a-boss-97207a042e42)**

**我希望您喜欢 JavaScript 中对日期的解释。**

**请随意评论并喜欢这篇文章，以便其他人可以在 Medium 上轻松找到它！**

**[![](img/3183c9cfe7a8644bc0a59d2e3e57fa27.png)](http://eepurl.com/dvio9L)****[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 AMI 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**