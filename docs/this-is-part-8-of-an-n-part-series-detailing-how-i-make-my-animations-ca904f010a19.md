# 变化率

> 原文：<https://medium.com/hackernoon/this-is-part-8-of-an-n-part-series-detailing-how-i-make-my-animations-ca904f010a19>

这是 N 部分系列的第 8 部分，详细介绍了我如何制作我的动画。

[上一张](https://hackernoon.com/change-your-scales-97e4ee37c4d4)下一张

我们不想用冗长缓慢的动画让观众厌烦，所以让我们加快一点速度。目前我们每帧画一个点，所以最简单的方法是每帧画多个点，方法是创建一个名为`deltaX`的变量，并将其设置为帧数的五倍:

```
int deltaX = frameCount * 5;
```

然后将`frameCount`的所有引用替换为`deltaX`，结果是:

就这样。下面是完整的代码:

```
import java.time.*;FloatDict _data = new FloatDict();
LocalDate _startDate = LocalDate.of(1958, 3, 29);void setup()
{
  loadData();
  size(500,500);
  background(0);
  stroke(255,255,0);
}float yScale = 20.0;void draw()
{
  background(0);
  float xScale = 1.0;

  int deltaX = frameCount * 5;

  if(deltaX > width)
  {
    xScale = float(width) / float(deltaX);
  } for(int dataIndex = 1; dataIndex <= deltaX; dataIndex++)
  {
    int daysFromStart = (dataIndex - 1) * 7;
    LocalDate frameDate = _startDate.plusDays(daysFromStart);

    if(_data.hasKey(frameDate.toString()))
    {
      float co2 = _data.get(frameDate.toString());

      float x = dataIndex;
      float y = (co2 - 313.04) ;

      if(y * yScale > height) 
      {
        yScale = float(height)/y;
      }

      ellipse(x * xScale, height - (y * yScale), 1, 1);
    }
  }
}void loadData()
{
  String[] lines = loadStrings("weekly_in_situ_co2_mlo.csv");

  for (String line : lines) 
  {
    if( line.startsWith("\"") ) continue;

    String[] values = split(line, ',');
    String date = values[0];
    float co2 = parseFloat(values[1]);
    _data.set(date, co2);
  }
}
```

但是，我觉得开始时太快了，但结束时不够快。这意味着我们应该在动画过程中改变[速率](https://hackernoon.com/tagged/rate)。一种方法是使用`frameCount`来改变速率，但这很快变得复杂。

这种事情在动画中很常见，聪明的类型已经创建了简化这种事情的库。这些库被称为“宽松库”,我们将使用 Benedikt Gro 写的名为`Ani`的库。

要安装`Ani`,进入 Processing 的`Tools`菜单，在 Libraries 选项卡中选择`Add Tool...`,搜索`Ani`,然后点击 install。

这个库所做的是使用一个选择的算法在动画过程中改变一个特定变量的值。我们希望以稳定的方式将动画的速度从慢变快。为此，我们用一个变量替换上面代码中的常量值`5`。我调用这个变量`_change`，并将其初始值设置为`1.0`。

```
float _change = 1.0;void draw()
{
  int deltaX = int(frameCount * _change);
```

显然，这本身并没有多大作用。

现在让我们激活`Ani`。首先我们导入它:

```
import de.looksgood.ani.*;
Ani _ani;
```

`_ani`是包含`Ani`对象的变量。

然后我们在`setup()`中设置它:

```
 Ani.init(this);
  Ani.setDefaultTimeMode(Ani.FRAMES);
  _ani = new Ani(this, 1032, "_change", 3.0, Ani.EXPO_IN);
```

`this`是对我们 Keeling 动画的引用，让`Ani`库知道在哪里找东西。默认情况下，时间模式是基于秒的，但我发现使用帧更准确，因为我们可以处理成千上万的数据点，每帧可能需要一段时间来渲染。

于是`Ani`图书馆的肉和土豆就包含在这行字里:

```
_ani = new Ani(this, 1032, "_change", 3.0, Ani.EXPO_IN);
```

这意味着超过 1032 帧修改`_change`变量中的值，以指数形式结束值 3。记得我们定义了`_change`从上面的值`1.0`开始，所以这个值慢慢增加，直到 1032 帧后达到`3.0`。

完整的文档可在[这里](http://benedikt-gross.de/libraries/Ani/)获得。

运行这个程序，我们得到:

正如你所看到的，它开始时很好，很慢，很容易，然后速度迅速增加。

以下是完整的代码:

```
import java.time.*;
import de.looksgood.ani.*;
Ani _ani;FloatDict _data = new FloatDict();
LocalDate _startDate = LocalDate.of(1958, 3, 29);void setup()
{
  loadData();
  size(500,500);
  background(0);
  stroke(255,255,0);
  frameRate(30);

  Ani.init(this);
  Ani.setDefaultTimeMode(Ani.FRAMES);
  _ani = new Ani(this, 1032, "_change", 3.0, Ani.EXPO_IN);
}float yScale = 20.0;
float _change = 1.0;void draw()
{
  int deltaX = int(frameCount * _change);

  background(0);
  float xScale = 1.0;

  if(deltaX > width)
  {
    xScale = float(width) / float(deltaX);
  }

  for(int dataIndex = 1; dataIndex <= deltaX; dataIndex++)
  {

    int daysFromStart = (dataIndex - 1) * 7;
    LocalDate frameDate = _startDate.plusDays(daysFromStart);

    if(_data.hasKey(frameDate.toString()))
    {
      float co2 = _data.get(frameDate.toString());

      float x = dataIndex;
      float y = (co2 - 313.04) ;

      if(y * yScale > height) 
      {
        yScale = float(height)/y;
      }

      ellipse(x * xScale, height - (y * yScale), 1, 1);
    }
  }
}void loadData()
{
  String[] lines = loadStrings("weekly_in_situ_co2_mlo.csv");

  for (String line : lines) 
  {
    if( line.startsWith("\"") ) continue;

    String[] values = split(line, ',');
    String date = values[0];
    float co2 = parseFloat(values[1]);
    _data.set(date, co2);
  }
}
```

下周我们将添加文字和一些润色！

我通过反复试验得出了 1032。我肯定有计算的方法，但人生苦短。

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，请[阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！