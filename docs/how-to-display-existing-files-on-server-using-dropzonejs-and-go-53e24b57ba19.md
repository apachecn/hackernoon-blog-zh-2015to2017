# 如何使用 DropzoneJS 和 Go 显示服务器上的现有文件

> 原文：<https://medium.com/hackernoon/how-to-display-existing-files-on-server-using-dropzonejs-and-go-53e24b57ba19>

![](img/763203385ea25cbd2d0a3dfe0e4d3cd4.png)

这是 DropzoneJS + Go 系列的第 2 部分。

*   [第 1 部分:如何构建文件上传表单](/@kataras/how-to-build-a-file-upload-form-using-dropzonejs-and-go-8fb9f258a991)
*   [第二部分:如何显示服务器上的现有文件](/@kataras/how-to-display-existing-files-on-server-using-dropzonejs-and-go-53e24b57ba19)

# DropzoneJS + Go:如何显示服务器上的现有文件

在本教程中，我们将向您展示如何在使用 DropzoneJS 和 Go 时显示服务器上的现有文件。本教程基于[如何使用 DropzoneJS 和 Go](/@kataras/how-to-build-a-file-upload-form-using-dropzonejs-and-go-8fb9f258a991) 构建文件上传表单。在继续学习本教程的内容之前，请确保您已经阅读了它。

# 目录

*   准备
*   修改服务器端
*   修改客户端
*   参考
*   结束了

# 准备

用`go get github.com/nfnt/resize`安装 go 包“github.com/nfnt/resize”，我们需要它来创建缩略图。

在之前的[教程](https://dev.to/kataras/README.md)中。我们已经设置了一个适当的工作 DropzoneJs 上传表单。本教程不需要额外的文件。我们需要做的是对下面的文件做一些修改:

1.  main.go
2.  view/upload . html

让我们开始吧！

# 修改服务器端

在之前的教程中。所有“/upload”所做的就是将上传的文件存储到服务器目录中。/public/uploads”。因此，我们需要添加一段代码来检索存储文件的信息(名称和大小)，并以 JSON 格式返回。

将下面的内容复制到“main.go”。详情请看评论。

# 修改客户端

将下面的内容复制到”。/views/upload.html "。我们将逐个进行修改。

1.  我们在页面中添加了 Jquery 库。这其实不是直接针对 DropzoneJs 的。我们使用的是 Jquery 的 ajax 函数 **$。仅获取**。您将在下面看到
2.  我们在表单中添加了一个 ID 元素(my-dropzone)。这是必需的，因为我们需要将配置值传递给 Dropzone。要做到这一点，我们必须有它的 ID 引用。这样我们就可以通过给 Dropzone.options.myDropzone 赋值来配置它了，很多人在配置 Dropzone 的时候都会面临困惑。简单地说。不要把 Dropzone 当成 Jquery 插件，它有自己的语法，你需要遵循它。
3.  这开始了修改的主要部分。我们在这里做的是传递一个函数来监听 Dropzone 的 init 事件。初始化 Dropzone 时调用此事件。
4.  通过 ajax 从新的“/uploads”中检索文件细节。
5.  使用服务器中的值创建模拟文件。mockFile 只是具有名称和大小属性的 JavaScript 对象。然后我们显式调用 Dropzone 的 **addedfile** 和 **thumbnail** 函数，将现有文件放入 Dropzone 上传区并生成其缩略图。

# 运行服务器

在当前项目的目录下打开终端并执行:

```
$ go run main.go
Now listening on: http://localhost:8080
Application started. Press CTRL+C to shut down.
```

如果你成功地做到了。现在去上传一些图片，并重新加载上传页面。已经上传的文件应该自动显示在拖放区。

![](img/8fa4db0053c48c2757cc9ebcb62e0979.png)

# 参考

*   【http://www.dropzonejs.com/#server-side-implementation 
*   [https://www . startutorial . com/articles/view/how-to-build-a-file-upload-form-using-drop zone js-and-PHP](https://www.startutorial.com/articles/view/how-to-build-a-file-upload-form-using-dropzonejs-and-php)
*   [https://docs.iris-go.com](https://docs.iris-go.com)
*   [https://github . com/kataras/iris/tree/master/_ examples/tutorial/drop zone js](https://github.com/kataras/iris/tree/master/_examples/tutorial/dropzonejs)

# 结束了

希望这个简单的教程对你的开发有所帮助。
如果你喜欢我的帖子，请在 [Twitter](https://twitter.com/makismaropoulos) 上关注我，帮助传播消息。我需要你的支持才能继续。

[](https://twitter.com/makismaropoulos) [## Gera simos Maropoulos(@ MakisMaropoulos)|推特

### Gerasimos Maropoulos 的最新推文(@MakisMaropoulos)。那个叫#golang 的家伙。自学 10 多年的开发人员…

twitter.com](https://twitter.com/makismaropoulos) 

我喜欢我不止一次点击鼓掌按钮时的视觉效果，你呢？很简单:只要点击拍手按钮。如果感觉强烈，再点一下**(或者直接按住** **)。**

![](img/e7bd15bcb40212893d2e7daf3f20813b.png)