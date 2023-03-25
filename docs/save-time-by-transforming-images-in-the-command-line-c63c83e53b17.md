# 通过在命令行中转换图像来节省时间

> 原文：<https://medium.com/hackernoon/save-time-by-transforming-images-in-the-command-line-c63c83e53b17>

经过几个月的编程，这个月我退一步做常规的 HTML/CSS 编码。起初，这真的很令人愉快，但很快出现了第一个问题:导出的图像通常不会以正确的形式出现。有时它应该是 JPG 的 PNG，有时图像有不必要的空白或者颜色不对。一种解决方案是启动一个图像 GUI 编辑器，更改图像并保存它。但是这也有一些缺点。显然你必须花些时间四处点击。也许通过一些练习，你会学到一些快速做事的关键技巧。但是打开图像并保存它的简单过程还是花费了太多的时间。并且一次对多个图像进行改变是有问题的。如果你能用几句话解释这个任务，那么在命令行中也很容易做到。

# 两个最基本的命令

有几个命令行命令可以帮助您完成大多数基本操作。这些当然是针对 BASH，而不是 Windows CLI。(这通常意味着你需要在 Windows 上安装 MacOS、Linux 或 Ubuntu)。实现图像处理的库主要是 [ImageMagick](https://www.imagemagick.org/) 。您很可能已经安装了它，但是在某些 Linux 发行版上，您可能需要自己安装它:

```
sudo apt-get install imagemagick
```

有时候也需要安装在 MacOS 上。使用 Brew 很容易做到这一点:

```
brew update && brew install imagemagick
```

ImageMagick 为您提供了两个强大的命令:[转换](https://www.imagemagick.org/script/convert.php)和 [mogrify](http://www.imagemagick.org/script/mogrify.php) 。Mogrify 通常采用与 convert 相同的参数，但是允许您处理多个图像，并使用**在适当的位置替换它们**。因此，提前备份这些映像是一个好习惯。例如，这会将当前文件夹中所有图像的大小调整为 256 * 256。

```
mogrify -resize 256x256 *.jpg
```

使用 convert，您可以指定单个源和输出文件名。这将转换图像从巴布亚新几内亚到 JPG。

```
convert myimage.png myimage.jpg
```

对应的货币单位是:

```
mogrify -format jpg *.png
```

# 调整大小和纵横比

对于很多人来说，调整大小是最常见的图像变换。

convert 和 mogrify 都有可用的 *-resize (-r)* 参数。它可以接受几种格式的值:

```
mogrify -resize 50% *.jpg # resize to 50%
mogrify -resize 1024x768 *.jpg # resize, keep original aspect ratio
mogrify -r 1024x768! *.jpg # resize, enforce exact dimensions
mogrify -r 1024 # resize to width of 1024
mogrify -r x768 *.jpg # resize to height of 1024
convert source.jpg -r 1024x768 output.jpg
```

调整大小的另一个解决方案可以是仅在 MacOS 上可用的 [*sips*](https://developer.apple.com/legacy/library/documentation/Darwin/Reference/ManPages/man1/sips.1.html) 命令。与 mogrify 相同，它就地替换图像。

```
sips --resampleWidth 1024 *.jpg
sips --resampleHeight 768 *.jpg
sips -z 768 1024 *.jpg # -z takes height as a first argument
```

sip 也可以旋转和翻转，但通常不如 ImageMagick 灵活。它的主要优点是默认安装在 MacOS 上。

# 借助重力进行裁剪

*用 mogrify 和 convert 裁剪*与 resize 非常相似。默认用法可能不完全直观。

```
convert source.jpg -crop 100x100 output.jpg
```

如果源图像是 1000x1000，就不会输出一张图像，而是十张。这可能是有用的，但在现实世界中，你经常需要的只是一个从中心裁剪下来的图像。这就是——*引力*发挥作用的地方:

```
convert source.jpg -crop 100x100+0+0 -gravity center output.jpg
```

+0+0 意味着 y=0，x=0，因此我们将从中心(图像的实际中心)获得最近的图块。

如果你只是需要从图像中去除一些边框，使用 *-shave* 会更有用。

```
convert source.jpg -shave 10x10 output.jpg #shave 10px from sides
```

# 修剪空白

编码时，带有空白的图像几乎没有用。这样的图像更难定位和响应。然而，当使用 GUI 编辑器时，很容易保存带有额外的未被注意到的空白的图像。

删除空白非常简单

```
mogrify -trim *.{jpg,png}
```

Trim 使用起来很安全，但是在使用之前做一个备份是一个很好的习惯。有些图像可能无法正确裁剪，因为可能会有极少量的“像素残留”。可以添加 *-fuzz* 使修剪更加严格。

```
mogrify -trim -fuzz 40% *.{jpg,png}
```

# 替换颜色和透明度

你需要不同颜色的标志吗？还是需要透明背景？这两种改变都很容易做到:

```
convert white.png -fill '#000' -opaque '#fff' black.png
```

-此处可以添加模糊，以增加颜色替换的容差

如果你有一个白色背景的 jpg，并需要使它透明，你可以简单地做:

```
convert source.jpg -transparent white output.png
```

# 创建收藏夹图标

如果源图像至少为 256x256，您可以快速转换为包含所有必要标准图像版本的 ico:

```
convert source.png -define icon:auto-resize favicon.ico
```

# 使用自定义 shell 脚本完成更多工作

准备一些 shell 脚本来自动化常见的图像转换可能会有所帮助。这些可以是更高级的操作或在多个图像上进行的操作序列。

我所说的 shell 脚本主要是放在~/中的函数。bashrc 文件(或~/。Mac 上的 bash_profile ),因此它们总是可用的。

我经常用它们来收集图片，主要是图库。当我创建一个画廊时，当我收到一个图片集时，我大部分时间都是这样做的

```
mv_seqnum(){
  a=1
  for i in $@; do
    new=$(printf "%04d.jpg" "$a")
    mv -- "$i" "$new"
    let a=a+1
  done
}# This can be used as$ cd some-image-folder
$ mv_seqnum
```

这将把图像重命名为序列号。这使得在各种模板语言和脚本中使用它们变得很容易。下一步是为缩略图和 srcset 创建多种图像尺寸。

```
img_size_folder(){
  mkdir $1
  cp *.jpg $1
  cd $1
  mogrify -r $2 *.jpg
  cd ..
}# This can be used as$ cd some-image-folder
$ img_size_folder thumbnail 150
```

这将创建一个缩略图文件夹，将图像复制到其中，并将它们的宽度调整为 150 像素。

现在，我们可以创建一个脚本，该脚本将获取一组图像，并为它们创建多种大小的版本:

```
create_image_sizes(){
  mv_seqnum
  img_size_folder big 1620
  img_size_folder full 1920
  img_size_folder medium 1024
  img_size_folder small 450
}$ cd some-image-folder
$ create_image_sizes
```

这将创建大、全、中、小文件夹，并在其中添加 1620 像素、1920 像素、1024 像素和 450 像素宽的图像。

# 压缩

Imagemagick 还提供了几种优化图像以缩小尺寸的方法。您可以使用选项*-质量*(范围从 0 到 100)。你也可以通过*-滤镜*选项应用许多滤镜。您可以选择渐进式 JPEG 渲染、额外锐化和其他功能。

在这篇优秀的[粉碎杂志文章](https://www.smashingmagazine.com/2015/06/efficient-image-resizing-with-imagemagick/)中很好地描述了先进的压缩技术，尤其是 [smartresize 函数](https://www.smashingmagazine.com/2015/06/efficient-image-resizing-with-imagemagick/#bash-shell)可以非常方便。

在 MacOS 上，还可以 [ImageOptim-CLI](https://github.com/JamieMason/ImageOptim-CLI) ，它可以[提供高达 80%的尺寸缩减](http://jamiemason.github.io/ImageOptim-CLI/)。

# 结论

到目前为止，这些命令对我很有用，我不需要打开 GUI。和往常一样，通过使用 CLI，您不仅节省了时间，而且还学到了一些方法，以后您可以使用这些方法在定制构建脚本和集成中实现自动化。学习曲线是值得的。

— — — — —

*我是一名自由职业者，目前正在寻找未来几个月的工作。我专门用 Ember.js 和性能优化构建单页面应用程序。随时联系我，电话:*[*malindacz@gmail.com*](mailto:malindacz@gmail.com)*或*[【twitter.com/@martinmalinda.cz】T21](https://twitter.com/martinmalindacz)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿美族家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp)、[在脸书上给我们点赞/发消息](http://bit.ly/HackernoonFB)，或者简单地说， [tweet/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！