# 编写您的第一个 JavaScript 库

> 原文：<https://medium.com/hackernoon/writing-your-first-javascript-library-475b776f5e73>

![](img/e07dff3d22c68246c2197486618fb2ff.png)

让我们从介绍什么是 JavaScript 中的库开始。例如，您编写的用于在 WebApp 中执行操作的任何程序或函数都被捆绑到一个模块中，可以在任何地方轻松重用，而不是直接将代码添加到脚本中，仅供您的应用程序使用。

# 目标

1.  无依赖性(完全独立的代码和功能)
2.  高效(最小的尺寸和处理负载)
3.  可重复使用，易于添加到任何项目中

# 模板和功能

JavaScript 本身就是一种强大的语言。凭借其 DOM(文档对象模型)操作功能，您只需使用[](https://plainjs.com/javascript/)*函数就可以完成您的库需要完成的几乎所有任务。*

*以下是使用 JavaScript 可以对 HTML 和 CSS 做的一些事情:*

```
*// Add HTML code to your page                   
this.template = '<div id="id">' + ' <div class="class"></div>';
document.body.innerHTML += this.template;// Change element styling
document.getElementById("id").style.display = "none";// Modify element attributes
document.getElementById("id").src = "";// Add listeners to elements
document.getElementById("id").addEventListener("click", this.function);*
```

# *基本结构*

*所以一般图书馆都是*。js* 文件连同*。css* 文件(如果需要添加很多自定义样式)。库中使用的所有函数，连同变量，甚至要添加的 HTML 模板，都被打包成*中的一个类。js* 文件。我之前写过一篇关于 JavaScript 类的文章[可能会对你有所帮助。](/@ajay.ns08/moving-from-class-ical-languages-to-javascript-1dfa978ddcae)*

```
*// Previewer.js
// LIBRARY CLASS 
function Previewer() {
  // Add all your code here
}*
```

*然后初始化模块，将`var previewer = new Previewer;`添加到 WebApp 的脚本中，一次*。js* 和*。css* 文件已经包含在你的项目中。*

# *结论*

*现在，您已经了解了纯 JavaScript 的结构、功能以及如何将它打包成一个库。让我们看看在我们刚刚构建的上述假想库中，基本目标是如何实现的。*

1.  *使用 vanillaJS(基本上是纯 Javascript)消除了对任何依赖的需要，使代码和功能相互独立*
2.  *当你有最少或者没有依赖时，效率的优化是自动完成的，并且代码尽可能的紧凑*
3.  *使用类和函数包装代码是基本的模块化*

# *预览器:编写我的第一个 JavaScript 库*

*一个用于网络的图像预览器，它使用 Picasa 风格的用户界面，并且是超级轻量级的。*

# *摆脱依赖*

*最初，我主要依靠 jQuery 选择器和函数来编写代码，因为我发现它们极大地简化了我的代码，也使我的工作变得容易。但是 Reddit 中的程序员让我意识到这根本不是一个好的实践，因为它有依赖性，这使得它效率更低；所以我不得不关闭 jQuery。*

*当处理选中的单个元素并修改属性或样式时，这并不是很多工作。问题是当你在 vanillaJS 中为多个元素使用选择器时。*

```
*// jQuery function that selects all elements of this class and assigns them the click listener
$(".preview-image").click(function() { 
    // function code here
});// Pure JavaScript equivalent of above code
var images = document.querySelectorAll(".preview-image");                                                       for(i = 0; i < images.length; i++) {                           
  (function(i){
    images[i].addEventListener('click', function () { 
        // function code here               
    });                        
  })(i) 
}*
```

*这是因为 JavaScript 在查询元素时会返回一个对象数组，所以必须为每个对象单独添加 click 事件侦听器。*

# *未来的发展*

*所有的库都有改进和增加功能的空间。作为开源软件的积极贡献者，首先要做的是让代码尽可能容易被其他开发者理解和贡献。整个代码中的描述性注释和一个好的 README.md 将会很有帮助。*

*另一件事是让社区来评估和建议。仅仅通过社区的想法，我已经添加了许多特性，例如基于键盘的库控制(下面将详细介绍),也接受了想要帮助的开发人员的请求。*

```
*document.onkeydown = keyCtrl; // Maps all keyboard actions
function keyCtrl(e) { 
    if (e.keyCode == '27') {
        // Add function code here// 27 here means Esc key, similarly other conditions can be used // for other functions and different keys*
```

# *结论(最后一个我发誓)*

*这足以让你开始构建库或者框架。因为编码社区每天都有东西，很难保持跟踪，所以，为了进一步阅读，我建议你检查一下 [*npm*](https://www.npmjs.com/) *包*。现在，您只需将导入 JS 模块的任务交给 npm，而不是手动添加文件。*

*检查我提到的项目，预览器:*

*[](https://github.com/ajayns/previewer/) [## ajayns/预览器

### 一个超轻量的 JavaScript 图像预览器

github.com](https://github.com/ajayns/previewer/) 

我希望你喜欢这篇开发者教程。如果有，请一定推荐、关注并分享这篇文章。*