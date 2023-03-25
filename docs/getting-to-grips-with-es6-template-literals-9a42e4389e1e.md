# 掌握 ES6:模板文字

> 原文：<https://medium.com/hackernoon/getting-to-grips-with-es6-template-literals-9a42e4389e1e>

![](img/428762b9d3039100524fb1d1f21549e5.png)

## 第三部分:理解标准的、嵌入的、多行的和标记的模板文字。

[*点击此处查看第四部分:*理解解构:引用与默认。](/@andrewjrhill/getting-to-grips-with-es6-destructuring-e5b5ddb34990#.3zcf16qq9)

## 前言

这篇文章是为希望更好地理解 ES6 模板文字的初级到中级开发人员设计的“入门”读物。

作为我的“掌握 ES6”系列的一部分，我的目标是创建一个参考，其中包含简单明了的解释，以帮助我们理解这些概念，并将它们直接应用到我们当前的开发过程中。

由安德鲁·希尔撰写。
你可以在 [LinkedIn](http://www.linkedin.com/in/andrewjrhill) 、 [Twitter](https://twitter.com/andrewjrhill) 、 [Instagram](https://www.instagram.com/andrewshills/) 、 [GitHub](https://github.com/Sntax/) 上找到我。

# 模板文字

模板文字(在 EcmaScript 规范的早期版本中也称为模板字符串)，由 [MDN 定义为允许嵌入表达式的字符串文字。它们为我们提供了更简洁的语法，让我们能够更好地控制如何显示字符串数据。](https://developer.mozilla.org/en/docs/Web/JavaScript/Reference/Template_literals)

## 传统的方式

以下示例说明了在 ES6 和模板文字出现之前，如何向字符串添加变量:

```
var age = 26;console.log('Andrew is ' + age + ' years old!');// Output: "Andrew is 26 years old!"
```

该模式适用于任何 [JavaScript](https://hackernoon.com/tagged/javascript) 表达式:

```
var currentYear = 2016;
var birthYear = 1990;console.log('Andrew is ' + currentYear - birthYear + ' years old!');// Output: "Andrew is 26 years old!"
```

要求使用连接是不利的，因为它迫使我们打破我们的字符串，以便把我们的表达式。在 [ES6](https://hackernoon.com/tagged/es6) 之前，JavaScript 缺乏简单地将表达式*插入字符串的能力。许多其他语言中已经存在多年的一个特征。*

## 加入新的！

为了构造一个模板文字，我们需要使用反斜线(```)来代替单引号或双引号。表达式通过用美元符号和花括号(`${}`)括起来插入到字符串中。

了解了这一点，我们可以将第一个示例重写如下:

```
const age = 26;console.log(`Andrew is ${age} years old!`);// Output: "Andrew is 26 years old!"
```

我们也可以将第二个示例重写如下:

```
const currentYear = 2016;
const birthYear = 1990;console.log(`Andrew is ${currentYear - birthYear} years old!`);// Output: "Andrew is 26 years old!"
```

*真好看！*

## 多行模板文字

在任何 JavaScript 代码基础上，多行字符串总是有点碍眼。每一个新行都需要转义字符或连接。一个传递给谷歌地图的[基本信息窗口很好地说明了这一点:](https://developers.google.com/maps/documentation/javascript/infowindows)

```
var title = 'Coffee Shop';
var description = 'Drink coffee!';var infoWindow = '<div>' +
                   '<h3>' + title + '</h3>' +
                   '<p>' + description + '</p>' +
                 '</div>';console.log(infoWindow);// Output: "<div><h3>Coffee Shop</h3><p>Drink coffee!</p></div>"
```

让我们看看使用模板文字编写时是什么样子的:

```
const title = 'Coffee Shop';
const description = 'Drink coffee!';const infoWindow = `<div>
                      <h3>${title}</h3>
                      <p>${description}</p>
                    </div>`;console.log(infoWindow);// Output: "<div>
              <h3>Coffee Shop</h3>
              <p>Drink coffee!</p>
            </div>"
```

info window 代码的模板文本版本明显更短，更易读。我们的输出甚至在不使用换行符的情况下保留了换行符。

## 嵌入模板文字

模板文字的一个不太为人所知的特性是它们能够被*插入到另一个模板文字*中。虽然这可能不是你经常遇到的事情，但知道这一点肯定是有帮助的。

我们通过使用三元运算符调整模板文本的结果来说明这一点，如下所示:

```
const hungry = true;
const foodType = 'pizza';const foodState = 
  `Andrew is ${hungry ? `hungry for ${foodType}!` : `full!`}`;console.log(foodState);// Output: "Andrew is hungry for pizza!"
```

## 标记的模板文字

模板文字有一种称为*标记模板文字的高级形式。*本质上，标记的模板文字通过允许我们用命名函数修改模板的输出，给了我们更多的控制。

考虑下面的例子:

```
const name = 'Damon';function altered(string, ...values) {
  return 'Wait, what?';
}const text = altered`The dogs name is ${name}.`;console.log(text);// Output: "Wait, what?"
```

在上面的例子中，函数`altered`覆盖了`text`模板文本的内容，因为函数返回了字符串`Wait, what?`。命名函数`altered`直接控制模板字符串输出的内容。

为了创建一个带标签的模板文字，我们首先定义一个返回某个值的命名函数。该值不必是字符串。通过将函数名放在模板文字的反斜线之前来调用函数。

标记模板中使用的函数可以接收两个参数，`strings`和`...values`。第一个参数包含一个字符串数组。字符串数组中的最后一项总是空字符串。在我们的例子中，我们的`strings`参数包含以下值:

```
console.log(strings[0]) // Output: "The dogs name is"
console.log(strings[1]]) // Output: ""
```

第二个参数包含传递给字符串的任何变量中存储的值。在我们的例子中，`...values`参数包含以下值:

```
console.log(values[0]) // Output: "Damon"
```

这些论点考虑到了一些强大的用例。下面是一个例子，我们循环字符串数组中的每一项，并将`<strong>`标签应用于传递到字符串中的任何变量:

```
function strong(strings, ...values) {
  let finalString = '';

  strings.forEach((string, i) => {
    if (string !== '') {
      finalString += `${string}<strong>${values[i]}</strong>`;
    }
  })

  return finalString;
}var name = 'Damon';
var text = strong`The dogs name is ${name}`;console.log(text);// Output: "The dogs name is <strong>Damon</strong>"
```

感谢阅读。如果您喜欢这篇文章，请考虑推荐它来支持本系列的未来安装。👏

[*点击此处查看第四部分:*理解解构:引用与默认。](/@andrewjrhill/getting-to-grips-with-es6-destructuring-e5b5ddb34990#.3zcf16qq9)