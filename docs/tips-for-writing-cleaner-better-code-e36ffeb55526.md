# 编写更简洁代码的技巧

> 原文：<https://medium.com/hackernoon/tips-for-writing-cleaner-better-code-e36ffeb55526>

![](img/b27924fe12f3d7485a9db9e775831a5f.png)

我决定写一篇文章，这将有助于初学者了解他们的错误，并把一些做法。

当然，这样的例子非常多，所以我只举几个。

## 1.常数

这个问题不仅关系到 javascript 的，也关系到一般的[编程的](https://hackernoon.com/tagged/programming)。考虑一个例子:

```
$elem.on('keydown', **function**(e) {
  **if** (e.keyCode == 27) {
    *//...* }
});
```

神奇的数字 27 是什么？经常编码的人会注意到——这是 ESC 键。但大多数开发者，尤其是初学者，要么不记得这些代码，要么根本不知道，面对代码时，被迫再次爬进搜索系统，浪费时间。
当然你可以在代码中添加注释，但是输入一个常量会更有效，例如:KEY_ESC = 27

## 2.**标识符**

通常我们需要获得一个元素的标识符(评论、帖子、用户等)。)来执行一些操作(例如，使用 ajax 评估注释),通常可以找到这种方法:

```
**var** id = $(**this**).attr('id').substring(8);
```

和前面的例子一样，开发人员必须猜测这个数字 8 是什么。

再比如(这行是从一个真实的项目里抄来的):

```
**var** last_id = $('#answer_pid' + id + ' li:first div').attr('id').substr(7);
```

html 布局中最微小的变化都会破坏代码。

好吧，还有一个:

```
<div class="comment" id="comment_123"></div>**var** id = $(**this**).attr('id').substring("comment_".length);
```

这更好(至少没有有线数字)，但是这种方法仍然将 js 代码过多地绑定到 html 上。

我们可以使用"**数据-*** "参数:

```
<div class="comment" data-id="123"></div>
```

现在获取标识符要容易得多:

```
**var** id = $(**this**).attr('data-id');
```

或者

```
**var** id = $(**this**).data('id');
```

## 3.$.邮政

我们都知道一种使用 ajax 的 jQuery 方法— $ .ajax。得到，$。负载，美元。帖子等。

添加这些函数是为了方便频繁执行的操作(上传脚本、json、执行 post 请求)，但是在实现中，所有这些方法都引用$ .ajax。

我个人从不使用速记功能，这就是原因。

在初学者或没有经验的代码中，您可以找到几个不同的阶段:

a.开始

```
$.post(url, data, **function**(data) {
  data = $.parseJSON(data);             
  *//...* });
```

b.添加“尝试-捕捉”块

```
$.post(url, data, **function**(data) {
  **try** {
      data = $.parseJSON(data);
  } **catch** (e) {
      **return**;
  }
  *//...* 
});
```

c.我们从文档中了解到，在 **$。post** 最后一个参数可以传递给 dataType(如果 success 函数不适合屏幕，它会在大量代码中消失)。

```
$.post(url, data, **function**(data) {
  *//...* }, 'json');
```

我很少能在初学者项目中看到错误处理程序，这主要是由于懒惰和不愿意花费额外的 5 分钟时间，或者开发人员只是确定不会有错误。

如果开发人员决定向$添加一个错误处理程序。帖子，通常是这样的:

```
$.post(url, data, **function**(data) {
  *//...* }, 'json').error(**function**() {
  *//...* 
});
```

这真的很难读。每次都编写一个错误处理程序是一项繁琐的任务，因此您可以为所有 ajax 请求设置一个默认的错误处理程序，例如:

```
$.ajaxSetup({
  error: **function**() {
    *// Error modal* } 
});
```

让我们回到$ .post .如上图所示，使用$。post 使得代码看起来很糟糕(尤其是数据类型在不可理解的地方)。让我们用$ .ajax 重写最后一个例子。

在我看来，这种方法可读性更强，也更容易维护。

```
$.ajax({
  type: "POST",
  url: url,
  data: data,
  dataType: "json",
  success: **function**(data) {
    *//...*
  },
  error: **function**() {
    *//...* }
});
```

## 4.多个元素的事件处理程序

有时有必要向页面元素添加事件处理程序(例如，“删除消息”按钮)。你经常会遇到这种方法:

```
$('.comment a.delete').click(**function**(){
  *//...* });
```

现在的问题是向新元素添加相同的处理程序(例如，向动态加载的注释)。我看到了很多解决方案，包括再次重新定义所有的处理程序:

```
$('.comment a.delete').unbind('click').click(**function**() {
  *//* });
```

jQuery 1.7 中有一个名为“ [on](https://www.w3schools.com/jquery/event_on.asp) ”的方法，它绑定事件处理程序，通过选择器过滤元素。

```
$('body').on('click', 'a.external', **function**(e) {  
  *//* The function will be called when you click on any link with the external class
});
```

重要的是要记住，这个处理程序也适用于动态创建的对象。应该明智地应用这种方法。例如，以下代码可能会导致浏览器的性能下降和速度变慢:

```
$('body').on('mousemove', selector, **function**() {
  *//...* });
```

## 5.命名空间事件

事实上，命名空间事件被添加到 jQuery 1.2 中——很少有人使用它(我想大多数人只是不知道它们)。

例如，我们有这样的代码:

```
$('a').on('click', **function**() {
  *// Handler 1* }); $('a').on('click', **function**() {
  *// Handler 2* });
```

例如，现在我们需要从链接中删除第二个处理程序。但是**$(‘一’)。off('click')** 将删除两个处理程序。

让我们使用命名空间事件:

```
$('a').on('click.namespace1', **function**() {
  *//Handler 1* }); 
$('a').on('click.namespace2', **function**() {
  *//Handler 2* });
```

现在可以通过调用

```
$('a').off('click.namespace2');
```

更多关于命名空间事件的信息可以在[这里](http://api.jquery.com/on/#event-names)找到。

这只是我在别人的代码中经常遇到的问题的一小部分。我希望这篇文章有助于提高代码的质量。

## ❤如果这篇文章有帮助，请点击小心脏！(在推特[上关注我](https://twitter.com/alik_chebotar)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)