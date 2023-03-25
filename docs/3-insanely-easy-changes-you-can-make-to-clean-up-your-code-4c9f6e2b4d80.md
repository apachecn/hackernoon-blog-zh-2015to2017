# 你可以做 3 个非常简单的改变来清理你的代码

> 原文：<https://medium.com/hackernoon/3-insanely-easy-changes-you-can-make-to-clean-up-your-code-4c9f6e2b4d80>

![](img/dddcf6fdfce5246c22a3f7b1b227cb30.png)

Photo by [Maico Amorim](https://unsplash.com/@maicoamorim)

一个[代码](https://hackernoon.com/tagged/code)重构总是给我一种成就感。尽管主要的重构是最令人满意的，但是每个[项目](https://hackernoon.com/tagged/project)并不总是保证它们。为了提高代码可读性，我对我的所有项目*进行了 3 次快速简单的重构:*

## 1.清除格式

代码的整体格式使得快速导航到感兴趣的区域成为可能。一致的缩进、换行符和模式有助于程序员浏览大块的代码。以下面的草率格式化的代码为例:

```
Inventory inventory = new Inventory();
for (int i = 0; i < cars.Count; i++){
    inventory.Cars.Add(cars[i]);

    var owner = owners.Where(x => x.VIN == cars[i].VIN).OrderByDescending(x => x.PurchaseDate).FirstOrDefault();inventory.PreviousOwners.Add(new Owner { VIN = cars[i].VIN,
                                        Name = owner.Name});
}
```

和这个比较一下:

```
Inventory inventory = new Inventory();for (int i = 0; i < cars.Count; i++)
{
    inventory.Cars.Add(cars[i]);

    var owner = owners.Where(x => x.VIN == cars[i].VIN)
                        .OrderByDescending(x => x.PurchaseDate)
                        .FirstOrDefault(); inventory.PreviousOwners.Add(new Owner 
                                     {
                                        VIN = cars[i].VIN,
                                        Name = owner.Name
                                     });
}
```

上面的第二个例子一致地缩进行，添加新行，并遵循一致的编码模式。这使得快速浏览代码变得很容易。

书籍有通过格式化定义的章节、标题和段落，这样很容易一眼就能找到需要的内容——格式化代码也使查找内容变得容易。

## 2.重命名类、方法和变量

类、方法和变量的命名应该有助于程序员理解代码中发生了什么。对象的范围越小，越允许使用较短的名字(比如在只有一两行的循环中用“I”作为计数器)。

在编写程序的“初稿”时，很容易使用无信息的名字，但是使用想到的名字并不总是最好的选择。看一下下面的例子:

```
public IEnumerable<string> GetData(int year)
{
    var result = CallApi("/Cars", year);     IEnumerable<string> output = new IEnumerable<string>(); foreach(var c in result)
    {
        output.Add(c.Make);
    }
}
```

对比:

```
public IEnumerable<string> RetrieveCarMakes(int year)
{
    var inventory = CallApi("/Cars", year); IEnumerable<string> carMakes = new IEnumerable<string>();

    foreach(var car in inventory)
    {
        carMakes.Add(car.Make);
    }
}
```

使用有意义的名字会让其他人(或者你的未来)更容易理解你的代码在做什么。

## 3.拆分长表达式

当您进入代码编写的最佳状态时，很容易将命令链接在一起或使用单行语法来加快编写速度。很多时候，一天后我回头看我的代码，我会看到类似这样的长表达式。看看这两行表达式:

```
bool hasHighSaleProbability = (daysOnLot < 60) ? true : (color == "Red" ? true : false);var highSaleProbabilityVehicles = Inventory.Where(x => x.DaysOnLot < 60 or x.Color == "Red").Select(x => new { Make = x.Make, Model = x.Model, Year = x.Year }).Distinct().Select(x => new RecentInventoryView { YearDropdown = new SelectListItem { Text = x.Year, Value = x.Year }, MakeModelDropdown = new SelectListItem { Text = x.Make + " " + x.Model, Value = x.Make + " " + x.Model } });
```

与这个被分成多行的表达式相比:

```
bool hasHighSaleProbability = false;if (daysOnLot < 60 || color == "Red")
{
    hasHighSaleProbability = true;
}var distinctMakesModelsYears = Inventory
   .Where(x => x.DaysOnLot < 60 or x.Color == "Red")
   .Select(x => new 
   { 
    Make = x.Make, 
    Model = x.Model, 
    Year = x.Year 
   })
   .Distinct()
   .ToList();var recentInventoryView = distinctMakesModelsYears
    .Select(x => new RecentInventoryView 
    { 
     YearDropdown = new SelectListItem 
     { 
      Text = x.Year, 
      Value = x.Year 
     }, 
     MakeModelDropdown = new SelectListItem 
     { 
      Text = x.Make + " " + x.Model, 
      Value = x.Make + " " + x.Model 
     } 
    });
```

尽管第一段代码在技术上更短，行数更少，但它几乎不可读。第二个代码片段分解了 if 逻辑，并将所有链接的方法分解成更多的逻辑变量。结果呢？更容易理解的代码逻辑。

你有什么经常做的“快速重构”吗？请在下面的评论中分享！

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)，并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)