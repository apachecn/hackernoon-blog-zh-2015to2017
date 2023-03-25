# 一种在 E2E 测试中验证用户身份的简单方法

> 原文：<https://medium.com/hackernoon/an-easier-way-to-authenticate-users-in-e2e-tests-230d86071f48>

在许多情况下，您可能需要您的测试来代表一个经过身份验证的用户。例如，如果您的 Web 应用程序为授权用户提供特殊功能，您将需要您的测试来进行身份验证。

Web 应用程序中有两种流行的身份验证类型

*   由用户在网页上输入凭据执行的基于表单的身份验证
*   协议级 HTTP 身份验证，包括 HTTP Basic 和 Windows NTLM 身份验证

然而，现有的测试工具不能为这两种认证类型提供完全的支持。

大多数测试框架没有专门处理基于表单的身份验证——它是通过一系列常规的测试操作来执行的。假设一些框架在测试运行之间共享 cookies，而另一些不共享，那么您可能需要在每个测试中手工登录或注销。

最近的 [E2E](https://hackernoon.com/tagged/e2e) 测试[工具](https://hackernoon.com/tagged/tools)也对 HTTP 基本或 NTLM 认证有有限或没有内置支持。例如，Selenium 只能在第三方浏览器自动化工具的帮助下处理 HTTP 认证。

幸运的是，事情发生了变化，新的解决方案出现在 E2E 测试领域。我们在 DevExpress 开发了自己的测试框架 [TestCafe](https://github.com/DevExpress/testcafe/) ，为 Web 认证提供了广泛的支持。

TestCafe 支持现成的 HTTP 基本和 NTLM 认证。为了处理基于表单的认证，TestCafe 引入了一个新概念— *用户角色*。用户角色代表其帐户参与测试的个人用户。用户可以登录多个网站，所有帐户都将与用户角色相关联。您可以通过一次函数调用在角色之间切换来更改活动用户。

在本文中，我将描述如何在 TestCafe 测试中执行认证。我还将展示一个在 GitHub 上进行身份验证的真实例子。

## 用户角色

假设您需要测试一个在表格中显示数据的应用程序页面。普通用户可以查看数据，而管理员也可以删除单个行。

让我们编写一个测试来检查这一点

*   只有管理员可以看到“删除行”按钮；
*   该按钮实际上删除了表格行。

在这个测试中，我们必须登录三次，并为一个普通用户提供两次相同的凭证。

为了简化认证操作，TestCafe 引入了用户角色。用户角色由登录用户的测试操作初始化。

我们创建了两个用户角色:一个用于普通用户帐户，另一个用于管理帐户。现在让我们重写测试，以便它使用角色而不是身份验证操作。

我们隔离了身份验证操作，测试现在集中在测试场景上。我们还通过在角色之间切换而不是再次认证节省了几行代码。

## 示例:在 GitHub 上验证

在这个例子中，我们将编写一个测试，登录到 GitHub 并检查认证是否成功。

首先，让我们创建一个页面对象。

`LoginPage`类描述了 GitHub 登录页面，有两个凭证输入和`Sign in`按钮。

描述了登录页面后，我们可以创建用户角色。

这些用户角色通过输入用户凭证并点击`Sign In`按钮的测试动作来初始化。

现在让我们为 GitHub 主页添加一个 page 对象。我们将在测试中使用两个元素:头像和下拉菜单标题。

最后，我们可以编写一个通过用户角色进行身份验证的测试。然后，它检查通过单击头像调用的下拉菜单是否显示正确的用户名。

## HTTP 基本/NTLM 身份验证

要登录受 HTTP Basic 或 NTLM 认证保护的网站，请向`httpAuth`方法提供您的凭据。您可以为整个设备或单个测试指定这些凭证。

NTLM 身份验证需要额外的参数:域名和工作站名称。你可以将它们传递给`httpAuth`方法。如果您省略它们，TestCafe 将自动使用当前机器的域名和工作站名。

## 结论

在本文中，我展示了 TestCafe 如何对被测试的网页进行认证。TestCafe 支持现成的 HTTP 基本/NTLM 身份验证，并提供用户角色来处理基于表单的身份验证。

参考 TestCafe 文档，了解更多关于[用户角色](https://devexpress.github.io/testcafe/documentation/test-api/authentication/user-roles.html)和 [HTTP 认证](https://devexpress.github.io/testcafe/documentation/test-api/authentication/http-authentication.html)的信息。如果你有关于 TestCafe 的问题，请访问我们的论坛。要报告错误或留下建议，请访问我们的 [GitHub 页面](https://github.com/DevExpress/testcafe/)。

如果你已经尝试过 TestCafe 认证，请在评论中分享你的经验。