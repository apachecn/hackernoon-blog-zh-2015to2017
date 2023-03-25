# Angular 如何保护我们免受 XSS 的攻击？

> 原文：<https://medium.com/hackernoon/how-angular-protects-us-from-xss-attacks-3cb7a7d49d95>

## 有角度的跨站点脚本攻击和杀毒过程

![](img/7f10c71fb0ab1f7c055cf4189c82cbd6.png)

***本*条** ***原本出现在***[***dormoshe . io***](https://dormoshe.io/articles/how-angular-protects-us-from-xss-attacks-19)

XSS 是可能影响您网站的攻击之一。为了应对这种攻击，Angular 实现了一些概念，防止开发人员犯错误，并为安全漏洞打开了一扇窗。

在本文中，我们将了解什么是 XSS 攻击，如何在 Angular 应用中进行这种攻击，Angular 如何保护我们的安全，以及如何禁用这种保护。

# 什么是 XSS 攻击？

跨站点脚本(XSS)攻击是一种注入类型，在这种攻击中，恶意脚本被注入到良性和可信的网站中。当攻击者使用 web 应用程序向不同的最终用户发送恶意代码(通常以浏览器端脚本的形式)时，就会发生 XSS 攻击。允许这些攻击成功的缺陷非常普遍，并且出现在 web 应用程序在其生成的输出中使用来自用户的输入而没有对其进行验证或编码的任何地方。

# 如何在角度应用中实现 XSS？

在角度应用中有一些攻击方法:

*   **HTML** —将值解释为 HTML 时
*   **样式** —绑定 CSS 时
*   **URL** —使用 URL 属性时

XSS attacks examples

# 角度如何防止这种攻击？

为了阻止 XSS 攻击，您必须防止恶意代码进入 DOM。当一个值通过属性、特性、样式、类绑定或插值从模板插入到 DOM 中时，Angular 清理并转义不可信的值。

> 净化是对不可信值的检查，将它转换成可以安全插入 DOM 的值

下面是`BrowserModule`中`sanitization providers`的声明

Browser sanitization providers

## DOM 清理服务

DomSanitizer 的目标是清除值中不可信的部分。这个类的框架看起来像:

The DOM sanitizer skeleton

如您所见，有两种方法模式。第一个是`bypassSecurityTrustX`方法，根据值的用法得到不可信的值，返回一个可信的对象(后面会讲到)。第二个是`sanitize`方法，它获取安全上下文和不可信值，并返回一个可信值。安全上下文是值的使用。

如果一个值对于上下文是可信的，这个`sanitize`方法将打开包含的安全值并直接使用它。否则，该值将根据安全上下文被清理为安全的。下面是功能代码:

The sanitize method

该方法的核心部分是`switch-case`块。根据安全上下文检查的值。`SafeXImpl`对象只是拥有`getTypeName`方法的对象，能够使用`instanceof`功能。
有三个主要的辅助函数用于净化这些值。`[sanitizeHtml](https://github.com/angular/angular/blob/5293794316cc1b0f57d5d88b3fefdf6ae29d0d97/packages/platform-browser/src/security/html_sanitizer.ts)`函数通过解析值并检查其标记来净化不受信任的 HTML 值。`[sanitizeStyle](https://github.com/angular/angular/blob/5293794316cc1b0f57d5d88b3fefdf6ae29d0d97/packages/platform-browser/src/security/style_sanitizer.ts)` [](https://github.com/angular/angular/blob/5293794316cc1b0f57d5d88b3fefdf6ae29d0d97/packages/platform-browser/src/security/style_sanitizer.ts)和`[sanitizeUrl](https://github.com/angular/angular/blob/5293794316cc1b0f57d5d88b3fefdf6ae29d0d97/packages/platform-browser/src/security/url_sanitizer.ts)`函数通过正则表达式净化不可信的样式/URL 值。

# 我们如何禁用清理逻辑？

在特定情况下，可能有必要禁用清理，例如，如果应用程序确实需要生成一个带有动态值的``javascript:``样式的链接。用户可以通过用`bypassSecurityTrustX`方法之一构造一个值，然后从模板绑定到那个值，从而绕过安全性。

> 用不受信任的用户数据调用`bypassSecurityTrustX`方法会使您的应用程序面临 XSS 安全风险

这种方法的一个例子是 HTML 值的`bypassSecurityTrustHtml`。

The bypassSecurityTrustHtml method

我们可以看到该方法使用的接口。该方法只是获取一个值，并在包装的对象中原样返回该值。

# 结论

XSS 攻击在 web 浏览器中很常见。在这些攻击中，受害者是用户而不是应用程序。恶意值大多来自 HTML、CSS 或 URL。有了 Angular，你就自动处于一个安全的地方。这是由清除不可信值的 DOM 杀毒程序完成的。您可以禁用此角度保护。当你决定做这件事时，要注意其中的危险，谨慎而明智地去做。

![](img/522b2e4ace3cfcecd43bba30fcf0a317.png)

***您可以关注我的***[***dormo she . io***](https://www.dormoshe.io)***或***[***Twitter***](https://twitter.com/DorMoshe)***了解更多关于 Angular、JavaScript 和 web 开发的内容。***