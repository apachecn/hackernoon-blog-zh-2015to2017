# 关注点分离——使用 JQuery 操作 DOM

> 原文：<https://medium.com/hackernoon/separation-of-concerns-manipulating-the-dom-using-jquery-3cc34ef05545>

本周我正在做一个游戏项目，每当页面刷新或点击按钮时，用户都会看到乱码。用户还可以查看猜测单词的提示。然后，用户可以将他们的答案输入到表单中，并提交该表单。如果回答正确，用户会得到一个显示在屏幕上的分数，该分数会根据他们的答案加减分。今天我们写了两个函数——random name 和 Shuffle。在这些函数中，我们错误地应用了 DOM 事件。后来，我从一个职业玩家那里了解到，最好的做法是分离关注点，并编写处理咬入大小问题的函数。函数 shuffle 接收一个字符串输入，并必须将该字符串转换为一个数组，其中每个字符都拆分为单独的字母。现在是一个数组，字母必须被打乱，然后重新组合在一起，形成一个新的打乱的单词。

第一步:给你的函数输入一些数据-

```
const dictionary = {
 “Mark Hamil”: “Luke Skywalker”,
 “Harrison Ford”: “Han Solo”,
 “Carrie Fisher”: “Princess Leia”,
 “Anthony Daniels”: “C-3PO”,
 “Daisy Ridley”: “Rey”,
 “Liam Neeson”: “Qui-Gon Jinn”,
 “Natalie Portman”: “Padme Amidala”,
 “Alec Guinness”: “Obi-Wan Kenobi”,
 “Felicity Jones”: “Jyn Erso”,
}
```

接下来，编写一个函数，它将从您的数据集生成一个随机名称。

```
const shuffle = function( randomName ) {
 let result = randomName.split(‘’)for( let i = 0; i < result.length; i++ ) {
 const randomIndex = Math.floor( Math.random() * result.length )
 const item = result[ i ]result[ i ] = result[ randomIndex ];
 result[ randomIndex ] = item
 }

 return result.join(‘’)
}
```

这是怎么回事？好吧，我们来分析一下。函数表达式 SHUFFLE 取数据集的随机名。然后使用内置方法 split 将数据集拆分成一个数组。当您使用这个内置方法 for [javascript](https://hackernoon.com/tagged/javascript) 时，您需要确保给它输入一个空字符串，以便将字符串拆分成各个字母。。split 还可以在想要拆分数组的地方接受一个参数。然后我们遍历数组。我们设置了一个 randomIndex 变量，该变量接受数组的长度，并将其相乘以获得一个基于数组长度的随机数。然后，我们将数组在该索引处的值赋给一个名为 item 的变量。然后，我们在该索引处获取结果，然后将其分配给一个随机索引号，最后将其设置为 item。然后，我们将新的结果数组连接在一起，形成加扰的字。咻。

```
//DOM EVENTS
$(document).ready( function() {
 $(‘#random-word-1’).text( shuffle( chooseRandomActor() ) )$( ‘#rescramble’ ).click( function( event ) {
 $(‘#random-word-1’).text( shuffle( chooseRandomActor() ) ) 
 })
})
```

现在是操纵 DOM 的时候了！首先，我们需要确保我们的 [jQuery](https://hackernoon.com/tagged/jquery) 事件在 DOM 准备就绪时触发。这是通过这行代码完成的:

```
$(document).ready( function() {})
```

接下来，选择需要显示随机单词的 html 元素。在这个 jQuery 事件处理程序中，我们还需要触发单词上的 shuffle 函数。这一行代码很好地处理了所有这一切:

```
$(‘#random-word-1’).text( shuffle( chooseRandomActor() ) )
```

最后，我们需要在用户单击 scramble 按钮时再次触发该事件:

```
$( ‘#rescramble’ ).click( function( event ) {
 $(‘#random-word-1’).text( shuffle( chooseRandomActor() ) ) 
 })
```