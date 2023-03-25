# 把它放在那里

> 原文：<https://medium.com/hackernoon/putting-it-out-there-f830d3846a34>

这是 N 部分系列的第 11 部分，详细介绍了我如何制作我的动画。

[上一张](https://hackernoon.com/more-polish-59b1a13480a0) [下一张](/@kevpluck/patience-now-25d7e59a8c0a)

所以你已经做了一个[数据可视化](https://hackernoon.com/tagged/data-visualisation)但是你如何展示它呢？

为此，我们需要将[动画](https://hackernoon.com/tagged/animation)输出到某种视频文件中，然后找到愿意托管它的网站。

我发现 Processing 的默认视频创建有点乏味，所以我使用了一种稍微复杂一点但更高级的技术。你需要下载两样东西，一个叫做 *ffmpeg* 的视频制作工具和一个插入*处理*的库，后者使用那个叫做*的视频输出*。

先从 f[fmpeg.org](https://ffmpeg.org)下载 *ffmpeg* 。我用的是 windows，所以只需要把它解压到某个地方(我有一个名为`c:\tools\`的文件夹，我把它解压后重命名为`ffmpeg`。把`c:\tools\ffmpeg\bin`放到用户路径中——我用[eveditor.com](http://eveditor.com/)来做这个——你就可以开始了。

现在通过运行*处理*安装*视频导出*，并通过进入`Tools/Add Tool...`菜单打开*贡献管理器*。选择*库*选项卡，搜索*视频导出*。点击安装，你就完成了。还有更多关于 funprogramming.org 的信息。

要使用*视频导出*，你首先需要将它导入到你的草图中，所以将这些线添加到文件的顶部:

```
import com.hamoid.*;
VideoExport videoExport;
```

`hamoid`是开发者的 github 用户 id(查看他的 [github 账户](https://github.com/hamoid))，所以我猜他以此作为前缀命名所有的库。第二行定义了一个类型为`VideoExport`的变量`videoExport`。看起来很困惑，直到你习惯了。

接下来我们需要做的是告诉*视频导出*我们的草图，因此在`setup()`中添加以下内容:

```
videoExport = new VideoExport(this);
videoExport.setFrameRate(30);
videoExport.startMovie();
```

`new VideoExport(this);`创建一个`VideoExport`类型的实例，提供由`this`表示的当前草图，这样它就可以访问框架。

我们现在准备将单个帧保存到视频中，因此在`draw()`的结尾添加:

```
videoExport.saveFrame();
```

当动画结束时执行`videoExport.endMovie()`很重要，因为视频文件可能会损坏，如果视频不播放，则检查该命令是否已执行。

这将在你的草图文件夹中创建一个名为`processing-movie.mp4`的视频文件，你可以在你最喜欢的视频播放器中播放它。

第一次使用*视频导出*运行草图时，会出现一个窗口询问你在哪里安装了 *ffmpeg。*

查看文章末尾，查看基林动画的完整源代码，包括*视频输出*命令。

那么现在你如何创建一个 gif 呢？当然是通过使用 *ffmpeg* ！

您可以简单地打开 dos 提示符或终端并执行:

```
ffmpeg -i processing-movie.mp4 processing-movie.gif
```

这在大多数情况下是可行的，但是如果你创建一个超过 256 色的视频，你会发现质量会下降很多，这种情况下事情会变得有点棘手。在这种情况下，您需要执行两个 *ffmpeg* 命令。首先是找出视频中使用的所有颜色，然后使用这两个命令使用调色板创建 gif:

```
ffmpeg -i processing-movie.mp4 -vf "palettegen" -y palette.png
ffmpeg -i processing-movie.mp4 -i palette.png -lavfi "fps=50,paletteuse" -y out.gif
```

欲知全部血淋淋的细节，请阅读 pkh 的博客。我将上述内容转换成一个名为`makegif.bat`的批处理脚本文件:

```
ffmpeg -i %1 -vf "palettegen" -y palette.png
ffmpeg -i %1 -i palette.png -lavfi "fps=50,paletteuse" -y out.gif
```

简单地由`makegif processing-movie.mp4`执行，它创建一个名为`out.gif`的 gif。

现在我们有了一个`mp4`和一个`gif`文件！我们该拿它怎么办？我们需要把它放在某个地方，这样它就可以被分享。所以你有以下选择:

Twitter :简单地将你的`mp4`文件附加到一条推文上。如果超过一定长度，会出现一个修剪对话框，但是如果你将开始和停止指示器移动到视频的开头和结尾，twitter 会很乐意主持它。

**YouTube** :上传即走，可以从 YouTube 的免费音乐库添加音乐，很好听。

FaceBook: 只需将你的`mp4`贴在帖子上。

【imgur.com】****:**(显然发音为“成像仪”)是一个保存 gif 的完美地方，这样你就可以发布到像[【reddit.com】](http://reddit.com)这样的网站上，添加电子邮件的链接，或者在一个叫 medium.com 的网站上写故事。我发现与 imgur 社区分享也是非常值得的。当托管在那里时，你可以直接链接到 gif，或者简单地通过在 URL 的末尾添加一个`v`链接到一个加载速度更快的`gifv`。**

**下面是使用*视频导出*的基林动画的完整源代码:**

```
import com.hamoid.*;
VideoExport videoExport;import java.time.*;
import de.looksgood.ani.*;
Ani _ani;FloatDict _data = new FloatDict();
LocalDate _startDate = LocalDate.of(1958, 3, 29);void setup()
{
  loadData();
  size(500,500);
  background(0);
  stroke(255,255,0);
  textAlign(CENTER);

  Ani.init(this);
  Ani.setDefaultTimeMode(Ani.FRAMES);
  _ani = new Ani(this, 530, "_change", 6, Ani.EXPO_IN);

  videoExport = new VideoExport(this);
  videoExport.setFrameRate(30);
  videoExport.startMovie();
}float yScale = 20.0;boolean _coda = false;
int _codaCount = 0;float _change = 1.0;
final int MARGIN = 60;
final float CO2MIN = 313.04;void draw()
{
  if(_change >= 6.0) 
  {
    if(!_coda)
    {
      _coda = true;
      _codaCount = frameCount;
    }
    if(frameCount - _codaCount > 120) 
    {
      videoExport.endMovie();
      exit();
    }
    videoExport.saveFrame();
    return;
  }

  background(0);
  strokeWeight(1);

  line(MARGIN, 0, MARGIN, height);
  line(0, height - MARGIN, width, height - MARGIN);

  drawGraph(MARGIN, height - MARGIN, width - MARGIN, height - MARGIN);
  videoExport.saveFrame();
}void drawGraph(int xPos, int yPos, int graphWidth, int graphHeight)
{
  text("Atmospheric CO₂ concentration 1958 - 2017\nR. F. Keeling, S. J. Walker, S. C. Piper and A. F. Bollenbacher\nMauna Loa, Observatory, Hawaii", MARGIN + graphWidth / 2.0, MARGIN);
  int deltaX = int(frameCount * _change);

  float xScale = 1.0;

  int xAxisMaximum = graphWidth;

  if(deltaX > graphWidth)
  {
    xAxisMaximum = deltaX;
    xScale = float(graphWidth) / float(deltaX);
  }

  text("Year", MARGIN + graphWidth / 2, height - 20);
  drawXAxis(xAxisMaximum, xScale);

  strokeWeight(3);

  float co2Max = 0.0;

  for(int dataIndex = 1; dataIndex <= deltaX; dataIndex++)
  {

    int daysFromStart = (dataIndex - 1) * 7;
    LocalDate frameDate = _startDate.plusDays(daysFromStart);

    if(_data.hasKey(frameDate.toString()))
    {
      float co2 = _data.get(frameDate.toString());

      if(co2 > co2Max) co2Max = co2;

      float x = dataIndex;
      float y = (co2 - CO2MIN);

      if(y * yScale > graphHeight) 
      {
        yScale = float(graphHeight)/y;
      }

      point(xPos + x * xScale, yPos - (y * yScale));
    }
  }

  if((co2Max - CO2MIN) * yScale < graphHeight) 
    co2Max = graphHeight / yScale + CO2MIN;

  pushMatrix();
    translate(0, graphWidth / 2);
    rotate(-PI / 2.0);
    text("CO₂ concentration (PPM)", 0, 20);
  popMatrix();

  drawYAxis(co2Max, yScale);
}void drawYAxis(float co2Max, float yScale)
{
  float co2Tick = 320.0;
  strokeWeight(1);

  while(co2Tick <= co2Max)
  {
    float yAxisTickPos = height - MARGIN - (co2Tick - CO2MIN) * yScale;

    line(MARGIN, yAxisTickPos, MARGIN - 5.0, yAxisTickPos);
    text(int(co2Tick), MARGIN - 15, yAxisTickPos+3);

    co2Tick += 10.0;
  }
}void drawXAxis(int xAxisMaximum, float xScale)
{
  int yPos = height - MARGIN;
  float xAxisTick = 279.0 / 7.0;
  int axisYear = 1959;

  while(xAxisTick <= xAxisMaximum)
  {
    int tickLength = 5;
    int xPos = MARGIN + int(xAxisTick * xScale);

    if(axisYear % 5 == 0)
    {
      text(axisYear, xPos, yPos + 22);
      tickLength = 10;
    }

    line(xPos, yPos, xPos, yPos + tickLength);

    xAxisTick += 365.25 / 7.0;
    axisYear++;
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

**为了感谢你走了这么远，这是一只想要更多花生酱的狗([来源](https://www.reddit.com/r/aww/comments/663iob/more_peanut_butter/)):**

**![](img/1e1cc5d95b4200a2930ecae8e69f1d31.png)**

**Yes, this is blatant click-bait.**

**给 windows 用户的提示是打开 Windows 资源管理器到你想要的位置，按下`F4`，用`cmd`替换文本框的内容，然后按回车键。这将打开该目录的 dos 提示符。**

**reddit.com 最好的地方是[r/数据美丽](http://reddit.com/r/dataisbeautiful/)、[r/视觉化](https://www.reddit.com/r/visualization/)和[r/气候](https://www.reddit.com/r/climate)。**

**[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)****[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)****[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)**

> **[黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是这个家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。**
> 
> **如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！**

**![](img/be0ca55ba73a573dce11effb2ee80d56.png)**