# 创建与 DOM 交互的弹出 Chrome 扩展

> 原文：<https://medium.com/hackernoon/creating-popup-chrome-extensions-that-interact-with-the-dom-27b943978daf>

Chrome 扩展对你的项目非常有用，它可以让你的用户与其他网站互动，改善他们的体验。有一些非常酷的应用，你可能很熟悉，它们为用户提供了从广告拦截到保存 Evernote 网站内容的所有功能。

创建 Chrome 扩展并不是很复杂，不幸的是文档对一些细节不是很清楚，有很多选项和可能性，通读它们会让人不知所措；在这篇文章中，我将解释一个弹出扩展的结构，它用给定的字符串改变页面上每个链接的文本。

从 github 下载示例扩展，作为 [zip](https://github.com/dvidsilva/chrome-ext-sample/archive/1.0.0.zip) [clone](https://github.com/dvidsilva/chrome-ext-sample) 。

首先，要加载你在 chrome 网络商店之外创建或下载的扩展，进入 chrome://extensions/并确保`developer mode`被激活。这将启用新选项，选择`Load unpacked extension...`并选择扩展所在的文件夹。

新的扩展名将出现在扩展名列表中，当你做了更改并想让浏览器识别它们时，重新加载该页面，或者点击扩展名下的`reload`选项。

# [文件](https://developer.chrome.com/extensions/manifest)

扩展中的主文件叫做`manifest.json`，它告诉 chrome 它需要加载什么文件，它们做什么，加上一些关于扩展的信息。

描述性属性是非常自我描述的，所以我将解释其他的。

```
"browser_action": {
        "default_icon": "favicon.png",
        "default_popup": "popup.html",
        "default_title": "ChromiePop"
    }
```

当浏览器动作出现时，扩展会在浏览器中添加一个按钮，靠近 omnibar。`default_icon`是将用于显示在栏中的图标。`default_popup`是按钮按下时将加载的 HTML 文件。`default_title`是图标将具有的标题元素，当鼠标悬停在按钮上时将显示帮助文本。

```
"content_scripts": [{
        "matches": ["<all_urls>"],
        "all_frames": true,
        "js":      ["scripts/content.js"]
    }]
```

[内容脚本](https://developer.chrome.com/extensions/content_scripts)是可以与活动页面交互的 javascript 文件。是一个字符串数组，内容脚本可以在哪些 URL 上运行，它们是匹配模式。`all_frames`意味着内容脚本也可以在页面内部的 iframes 中运行。`js`引用脚本，另外一个属性`css`也将注入样式表。

```
"permissions": [
    "activeTab"
]
```

[Permissions](https://developer.chrome.com/extensions/permissions) ，是一个字符串数组，包含扩展可以访问的权限。

# 信息

扩展中的 HTML 和脚本应该是不言自明的，有一个输入，一个按钮和事件监听器来使用这些信息，在文件中有两个 chrome 特有的东西，我将在这里讨论。

在`extension.js`:

```
chrome.tabs.query({active: true, currentWindow: true}, function(tabs) {
        chrome.tabs.sendMessage(tabs[0].id, {data: text}, function(response) {
            $('#status').html('changed data in page');
            console.log('success');
        });
    });
});
```

`[chrome.tabs.query](https://developer.chrome.com/extensions/tabs#method-query)`允许你在浏览器中找到一个标签，就像每一个 chrome 扩展 API 方法一样，它异步工作，它接收一个函数，这个函数将被调用并检索标签列表。使用清单中请求的`activeTab`权限，您将只能访问用户所在的当前活动选项卡。

Extension.js 将在用户每次点击按钮时执行，所以每次你想与标签交互时，你必须请求标签 id。

`[chrome.tabs.sendMessage](https://developer.chrome.com/extensions/tabs#method-sendMessage)`将允许您将一些数据从弹出脚本传递到内容脚本。

它采用以下参数`integer tabId, any message, object options, function responseCallback`。消息可以是任何值，在本例中，我们传递一个带有数据属性的对象，该属性包含链接将被更改的字符串。

```
chrome.runtime.onMessage.addListener( function(request, sender, sendResponse) {
    console.log("something happening from the extension");
    var data = request.data || {};
    var linksList = document.querySelectorAll('a');
    [].forEach.call(linksList, function(header) {
        header.innerHTML = request.data;
    });
    sendResponse({data: data, success: true});
});
```

`[onMessage](https://developer.chrome.com/extensions/runtime#event-onMessage)`允许您添加一个事件监听器，以对弹出窗口发送的消息做出反应。

请求是弹出窗口发送的消息数据，发送方有一些关于消息的扩展和来源的信息，发送响应是传递的回调。

# 结论

创建一个 chrome popup 扩展来与 DOM 交互是非常容易的。一旦你掌握了它，记住在 manifest.json 中正确地声明一切，并在传递消息以从 popup 向内容脚本传递信息时进行小的增量更改。