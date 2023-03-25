# 掌握 ES6:析构

> 原文：<https://medium.com/hackernoon/getting-to-grips-with-es6-destructuring-e5b5ddb34990>

![](img/15530bc782ac2762fe058768d6f5d0bc.png)

## 第四部分:理解析构-引用和缺省。

[*你可以在这里查看本系列之前的部分。*](https://medium.com/@andrewjrhill/latest)

## 前言

这篇文章是为希望更好地理解 EcmaScript 6 ( [ES6](https://hackernoon.com/tagged/es6) )中引入的**析构**的初级到中级开发人员设计的“入门”读物。

作为我的“掌握 ES6”系列的一部分，我的目标是创建一个参考，其中包含简单明了的解释，以帮助我们理解这些概念，并将它们直接应用到我们当前的[开发](https://hackernoon.com/tagged/development)过程中。

每篇文章都建立在前几篇文章中学到的概念之上。因此，如果你遇到一个你不认识的新概念，你可以考虑看一看[以前的文章](https://medium.com/@andrewjrhill/latest)。

作者:安德鲁·希尔。
你可以在 [LinkedIn](http://www.linkedin.com/in/andrewjrhill) 、 [Twitter](https://twitter.com/andrewjrhill) 、 [Instagram](https://www.instagram.com/andrewshills/) 、 [GitHub](https://github.com/Sntax/) 上找到我。

# 解构

析构赋值语法允许我们从数组或对象中快速提取数据，并将数据赋给任何给定的变量。

## 以传统方式引用对象值

以下示例说明了在 ES6 和析构出现之前如何引用对象数据:

```
var city = {
  name: 'Cape Town',
  population: 987007,
  website: 'capetown.gov.za',
};var name = city.name;
var population = city.population;
var website = city.website;console.log(name, population, website);// Output: "Cape Town 987007 capetown.gov.za"
```

> **示例 1:** 使用更传统的方法引用对象值。

这种潜在需要保存对来自同一个对象的值的引用的模式导致了重复的、不太简洁的代码。

## 通过析构引用值

ES6 析构允许我们用更简洁的语法达到同样的结果。我们可以将第一个示例重写如下:

```
const city = {
  name: 'Cape Town',
  population: 987007,
  website: 'capetown.gov.za',
};const { name, population, website } = city; console.log(name, population, website);// Output: "Cape Town 987007 capetown.gov.za"
```

> **例 2:** 通过析构引用对象值的更干净、更简洁的方式。

使用析构，只需要一个变量声明来定义`city`对象中的所有顶级变量。

我们还能够在 ES6 中销毁数组。唯一的区别是，当析构一个数组时，我们需要用方括号代替花括号:

```
const city = ['Cape Town', 987007, 'capetown.gov.za'];const [name, population, website] = city;console.log(name, population, website);// Output: "Cape Town 987007 capetown.gov.za"
```

> **例 3:** 通过析构引用数组值。

有时，您可能会发现自己面对的对象被不直观的键值对所曲解。让我们把下面的例子看作是我们通过一个写得很差的 API 检索的数据集:

```
const city = {
  x: 'Cape Town',
  y: 987007,
  z: 'capetown.gov.za',
};const { x, y, z } = city;
const cityWithCountry = `${x}, South Africa` console.log(cityWithCountry);// Output: "Cape Town, South Africa"
```

> **例 4:** 一个设计糟糕的数据集，带有难以描述的键，会降低我们代码的可读性。

这个数据集中难以描述的标识符最终会导致代码非常难以阅读和维护，特别是如果不加注释的话。

假设我们无法修改初始的`city`对象键，那么可以通过析构将数据有效地“*重命名为“*”(引用)，提高代码可读性:

```
const city = {
  x: 'Cape Town',
  y: '987007',
  z: 'capetown.gov.za',
};const {
  x: name,
  y: population,
  z: website} = city;const area = `${name}, South Africa` console.log(area);// Output: "Cape Town, South Africa"
```

> **例 5:** 通过析构重命名对象键，提高代码可读性。

## 解构深度嵌套的值

有趣的是，析构不限于对象或数组的顶级值。我们可以通过遵循相同的析构模式来访问深度嵌套的值:

```
const city = {
  name: 'Cape Town',
  population: 987007,
  website: 'capetown.gov.za',
  weather: {
    temperature: {
      celcius: 19,
      fahrenheit: 66,
    },
    windSpeed: {
      kph: 27,
      mph: 17,
    },
    humidity: '77%',
  },
};const { weather: { temperature }} = city; console.log(temperature);// Output: { celcius: 19, fahrenheit: 66 }
```

> **例 6:** 深度嵌套的对象数据，通过析构，很容易被引用。

使用上面的例子，我们能够通过析构`city`对象中的`weather`键来访问深度嵌套的`temperature`值。

## 通过析构设置默认值

可以在析构时创建缺省值(或回退值),以考虑预期但缺失的数据。考虑下面的例子:

```
const city = {
  name: 'Cape Town',
  population: 987007,
  website: 'capetown.gov.za',
};const {
  name,
  population,
  website = 'No Website Available',
  country = 'South Africa',
} = city;const cityWithCountry = `${name}, ${country}`;console.log(website, cityWithCountry);// Output: "capetown.gov.za"
//         "Cape Town, South Africa"
```

> **例 7:** 默认值可以设置为备用值，以考虑数据集中可能丢失的数据。

在上面的例子中，`cityAndCountry`引用需要一个`country`变量，但是在我们的`city`数据集中没有提供。结果，我们在析构中设置的默认值生效了，对`country`的引用指向默认值`South Africa`。

*如果一个值存在于被析构的对象中，通过析构分配的默认值总是被忽略。*

为了进一步说明这一点，已经为`city`对象中的`website`键赋值。`website`引用将指向对象的值(`capetown.gov.za`)，而不是默认值(`No Website Provided`)，因为对象中已经存在一个值。

## 函数签名内的析构

函数签名中也允许析构:

```
const cityPopulation = ({ name } = {}, website) => {
  console.log(`Learn more about ${name} at ${website}`);
};cityPopulation({ name: 'Cape Town' }, 'capetown.gov.za');// Output: "Learn more about Cape Town at capetown.gov.za"
```

> **例 8:** 析构函数参数。

这确保了我们的函数总是能够访问任何预期的值。下面是在析构参数上设置默认值的函数示例:

```
const cityPop = ({ name = 'Unknown', population=0 } = {}) => {
  console.log(`${name} has a population of ${population}`);
};cityPop({ name: 'Cape Town', population: 987007 });// Output: "Cape Town has a population of 987007"cityPop();// Output: "Unknown has a population of 0."
```

> **例 9:** 在函数签名内设置默认值。

你可能已经注意到析构的参数被分配给一个空的对象`={}`。这样做是为了确保我们的函数调用(没有参数)不会因出现`Uncaught TypeError`而失败，如下例所示:

```
const cityPop = ({ name = 'Unknown', population=0 } = {}) => {
  console.log(`${name} has a population of ${population}`);
};cityPop({ name: 'Cape Town', population: 987007 });// Output: "Cape Town has a population of 987007"cityPop();// Output: -> Uncaught TypeError: Cannot match against 'undefined' or 'null'.
```

Example 10: Destructured parameters fail to fallback to defaults when not assigned to an empty object.

感谢阅读。如果您喜欢这篇文章，请考虑推荐它来支持本系列的未来安装。👏

[*对学习更感兴趣？单击此处查看本系列的前几部分。*](https://medium.com/@andrewjrhill/latest)

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)