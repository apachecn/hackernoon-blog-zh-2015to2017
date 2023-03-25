# Firebase 中的无限滚动

> 原文：<https://medium.com/hackernoon/infinite-scrolling-in-firebase-e28bfbc53578>

本文将向您展示如何在 JavaScript 和 firebase 中实现无限滚动，而无需修改现有的 firebase 数据结构。

我试图让这篇文章尽可能地通用并适用于您选择的任何框架/库，所以希望它能让您很好地理解在自己的项目中实现它所需要经历的步骤。

注意:如果你不想继续下去，并急于看到代码，你可以跳到文章的底部。

![](img/2c2ff82926d78dee23ae553b331ebb7e.png)

## **概要**

开始之前，让我们定义一下预期行为:

*   初始获取:检索 5 个最新的项目
*   后续获取:检索接下来的 5 个最新项目

让我们还定义我们将假装从 firebase 中获取的测试数据——使用假数据将更容易可视化查询发生了什么:

```
// our firebase database
items: { 
   firstInsertedItem: { … }, // oldest
   SecondInsertedItem: { … }, 
   ThirdInsertedItem: { … },
   FourthInsertedItem: { … }, 
   FifthInsertedItem: { … }, 
   SixthInsertedItem: { … }, 
   SeventhInsertedItem: { … },
   EighthInsertedItem: { … }, 
   NinethInsertedItem: { … }, 
   TenthInsertedItem: { … }, // newest
 }// in case you were wondering about the order that I put the items // in, that's just to make it easier to follow along
// and it would make no difference to us if firebase actually 
// stored those items in a different order internally
```

## **Firebase 按键**

让我们首先介绍 firebase 按键背后的魔力，以及它们如何让这一切成为可能。

事实证明，firebase 按键不仅仅是一些随机的字符序列。事实上，所有的 [firebase 按键](https://firebase.googleblog.com/2015/02/the-2120-ways-to-ensure-unique_68.html)都由时间戳和随机数据组成，这些数据以修改后的 base64 字母表编码，以**保存**它们的**时间顺序** **顺序**——即它们的插入顺序。

这是一个强大的特性，因为它允许我们利用 firebase 的排序查询。

既然我们已经定义了这个重要的特性，我们就可以继续学习教程了。

## **第一步**

为了让我们所有的获取，在第一次之后，返回我们期望它们返回的内容，我们需要保存某种对最早的先前获取的键的引用。

所以让我们定义一个变量，叫做:

```
let referenceToOldestKey = ‘’;
```

## **初始获取**

接下来，让我们创建一个查询来获取前 5 个最新插入的项目:

```
firebase.database().ref(‘items’)
 .orderByKey()
 .limitToLast(5)
 .once(‘value’)
 .then((snapshot) => { … } )
 .catch((error) => { … } );
```

该查询执行以下操作:

- ***orderByKey*** 将项目按其关键字按时间顺序排序(从最老到最新)
-***limit to last***从末尾选择 5 个项目(从第 5 个开始)

返回的对象将如下所示:

```
{ 
   SixthItemFromTheEnd: { … },
   SeventhItemFromTheEnd: { … },
   EighthInsertedItem: { … },
   NinethInsertedItem: { … },
   TenthInsertedItem: { … },
 }
```

接下来，我们需要颠倒这个对象的顺序，使最新的项目在顶部而不是底部。

我们这里有几个选项，取决于您喜欢如何管理您的应用程序状态:

*   反转对象
*   将对象转换为对象数组并反转数组
*   保持对象原样，但在向用户显示时进行浅复制并当场反转

我们将选择第二个选项，因为这是存储这类数据最流行的方式。

所以我们给**补充以下内容。那么我们的获取函数的**:

```
let arrayOfKeys = Object.keys(snapshot.val())
   .sort()
   .reverse();let results = arrayOfKeys
   .map((key) => snapshot.val()[key]);
```

注意，我们在反转键的顺序之前对它们进行了排序——这样做是为了确保正确的顺序，因为 JavaScript 不保证对象键的顺序。

接下来，我们需要用 5 个键中最老的键来初始化我们的引用，以便当用户滚动到页面底部时，我们的下一个获取函数知道从哪里继续:

```
referenceToOldestKey = arrayOfKeys[arrayOfKeys.length-1];
```

(最老的键在最后一个位置，因为记住 ***limitToLast*** 按时间顺序返回项目，我们颠倒了它。)

现在我们已经完成了第一次提取:

```
// You can do what you want to do with the data, i.e.
// append to page or dispatch({ … }) if using redux
```

## **下一次取货**

好了，我们的用户刚刚点击了页面底部，让我们通过创建以下查询为他们获取接下来的 5 个最新项目:

```
firebase.database().ref(‘items’)
 .orderByKey()
 .endAt(referenceToOldestKey)
 .limitToLast(6)
 .once(‘value’)
 .then((snapshot) => { … } )
 .catch((error) => { … } );
```

该查询执行以下操作:

*   ***orderByKey*** 按关键字按时间顺序(从最早到最新)对项目进行排序
*   ***endAt*** 选择从第一个添加到我们的引用变量(**含**)中的所有项目
*   ***limitToLast*** 从末尾选择 6 个项目(从第 6 个开始)

返回的对象将如下所示:

```
{ 
   firstInsertedItem: { … },
   SecondInsertedItem: { … },
   ThirdInsertedItem: { … },
   FourthInsertedItem: { … },
   FifthInsertedItem: { … },
   SixthInsertedItem: { … },  // our reference is included!
 }
```

*   但是，为什么我们限制到 6 个最新的项目，而不是 5 个，就像我们在第一次获取中做的那样？

因为 ***endAt*** 是 **inclusive** 这意味着我们的**引用键**包含在返回的对象中。因此，如果我们限制为 5 个，我们将最终只有 4 个新项目，因为 1 个将是重复的，因此我们需要请求 6 个，然后在客户端处理第 6 个的删除。

好了，现在我们看到了查询是如何工作的，让我们反转返回的对象，然后删除重复的对象。

```
let arrayOfKeys = Object.keys(snapshot.val())
   .sort()
   .reverse()
   .slice(1);let results = arrayOfKeys
   .map((key) => snapshot.val()[key]);
```

为什么 ***切①***？因为在 ***反转()*** 之后，我们的副本从最后一个位置移到了第一个位置。我们怎么知道它一开始是在最后的位置？因为记住钥匙是按时间顺序归还的。

最后，我们需要用当前获取的最旧的键来更新我们的引用:

```
referenceToOldestKey = arrayOfKeys[arrayOfKeys.length-1];
```

现在我们已经完成了第二次提取:

```
// You can do what you want to do with the data, i.e.
// append to page or dispatch({ … }) if using redux
```

本教程到此结束，感谢您的阅读，希望您学到了一些新东西。下面是完整的代码示例。

## **完整的代码示例**

注意:**内代码重复。然后，两个查询中的**是有意使其更容易阅读，不需要到处寻找相关代码，如果代码被移到一个单独的函数中，则需要这样做。

```
let referenceToOldestKey = ‘’;if (!referenceToOldestKey) { // if initial fetch

  firebase.database().ref(‘items’)
   .orderByKey()
   .limitToLast(5)
   .once(‘value’)
   .then((snapshot) => {       // changing to reverse chronological order (latest first)
      let arrayOfKeys = Object.keys(snapshot.val())
         .sort()
         .reverse(); // transforming to array
      let results = arrayOfKeys
         .map((key) => snapshot.val()[key]); // storing reference
      referenceToOldestKey = arrayOfKeys[arrayOfKeys.length-1];

      // Do what you want to do with the data, i.e.
      // append to page or dispatch({ … }) if using redux })
   .catch((error) => { … } );

 } else {

  firebase.database().ref(‘items’)
   .orderByKey()
   .endAt(oldestKeyReference)
   .limitToLast(6)
   .once(‘value’)
   .then((snapshot) => { // changing to reverse chronological order (latest first)
     // & removing duplicate
     let arrayOfKeys = Object.keys(snapshot.val())
         .sort()
         .reverse()
         .slice(1); // transforming to array
      let results = arrayOfKeys
         .map((key) => snapshot.val()[key]); // updating reference
      referenceToOldestKey = arrayOfKeys[arrayOfKeys.length-1]; // Do what you want to do with the data, i.e.
      // append to page or dispatch({ … }) if using redux })
   .catch((error) => { … } );

 }
```

希望这篇文章没有完全浪费你的时间，你实际上从中学到了一些东西:)。

顺便说一下，如果你是一个推特用户，你可以通过 [linasmnew](https://twitter.com/linasmnew) ，*联系我(我是那里的新成员*😅 😄)