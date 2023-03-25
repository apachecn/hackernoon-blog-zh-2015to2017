# 如何在 Django 框架中添加用户认证

> 原文：<https://medium.com/hackernoon/how-to-add-user-authentication-in-django-framework-c2ea59fc6a3d>

Django 有一个用户认证系统，可以处理从用户账户和基于 cookie 的用户会话到组和权限的各种功能。身份验证是一种将传入请求与标识凭据(包括请求来自何处以及使用哪个令牌签名)联系起来的机制。然后，权限策略可用于允许或拒绝请求。

![](img/47add62eebc576fcfce779523e0a77a1.png)

Django 认证系统处理认证和授权。认证确认用户是否确实是他们所声称的那个人，而授权决定已经通过认证的用户被允许做什么。在大多数情况下，术语身份验证指的是这两项任务。*Django 认证系统由以下部分组成:*

*   一组用户
*   一组权限，包括二进制是/否标志，用于确定是否允许用户执行某项任务
*   为多个用户分组标签和权限
*   密码散列系统
*   查看用于登录用户或限制对特定内容的访问的工具和表单
*   后端系统

与大多数 web 认证系统不同，Django 认证是通用的，功能相对较少。该系统使用第三方软件包为常见问题提供解决方案，如检查密码强度、限制登录尝试以及第三方身份验证(如 OAuth)。

# 身份验证的安装

身份验证支持是作为 contrib 提供的。模块(django.contrib.auth)。默认情况下，所需的配置包含在 settings.py 中，该文件由 django-admin startproject 部分生成。在**已安装应用**设置中你会发现这两项:

*   *django.contrib.auth* ，它拥有认证框架核心和默认模型
*   *django . contrib . content types*，这是系统的内容类型，允许与创建的模型相关的权限。

在**中间件**设置中，你会发现这两项:

*   *会话中间件*处理请求会话
*   *认证中间件*连接用户和会话请求

当您已经有了这些设置后，您只需要运行 manage.py migrate 命令来创建您需要的数据库表，以便在您已安装的应用程序中执行与授权相关的任务和权限。

# 如何使用 Django 认证系统

随着时间的推移，Django 身份验证系统一直在变化，它不断发展，以服务于大量的任务，处理项目需求，并实现安全的密码和权限。

Django 还支持广泛的扩展和定制，以处理具有不同认证需求的不同类型的项目。Django 身份验证中的身份验证和授权特性在某种程度上是结合在一起的。

# Django 中的用户对象

Django 认证系统严重依赖用户对象。这些基本上是与您的网站互动的人或用户。用户对象处理诸如限制对特定内容的访问、将内容与其创建者连接以及注册新的用户配置文件等问题。

[Django 认证框架](https://docs.djangoproject.com/en/1.11/topics/auth/)只有一个用户类。“超级用户”和管理人员用户不是不同的用户类，而是具有特定属性集的用户对象。默认用户有以下主要属性:

*   用户名
*   西方人名的第一个字
*   姓
*   密码
*   电子邮件地址

如需更详细的参考资料，您可查阅完整的 API 文档[https://docs . djangopolympic . com/en/1.11/ref/contrib/auth/# django . contrib . auth . models](https://docs.djangoproject.com/en/1.11/ref/contrib/auth/#django.contrib.auth.models)。

# 如何创建用户

您可以通过包含的 create_user()函数直接创建用户，方法是转到 django . contrib . auth . models import user。例如 User = User . objects . create _ User(' Jane '，' jane@example.com '，' janepassword ')。

Jane 现在是一个已经创建并保存在数据库中的用户对象。然后，您可以继续对属性或其他字段进行任何必要的更改，例如“user.lastname ”,然后保存在“user.save()。

或者，如果您已经安装了 Django 管理器，您可以通过交互方式创建用户。

# 如何创建超级用户

您可以直接从 createsuperuser 命令创建超级用户。为此，请访问“python manage . py create super user-username = Jane-email = Jane @ example . com”。

您需要提供密码。用户将在您输入密码后创建。如果您将值留空，还会提示您输入值——用户名或——电子邮件。

# 如何更改密码

系统的用户模型不存储明文或原始密码，而是存储哈希。有关密码管理的更多详细信息，请查看此链接:[https://docs . djangoproject . com/en/1.11/topics/auth/password/。](https://docs.djangoproject.com/en/1.11/topics/auth/passwords/)

避免直接操作用户密码属性，而是在创建用户时参考助手函数。但是，您可以通过几种方式更改用户密码。

*   第一种方法是通过命令行管理程序。py changepassword *username*。如果命令提示您更改用户密码，您需要输入两次密码。如果两个密码匹配，新密码将立即生效。如果您不提供用户，系统将尝试更改与当前使用系统的用户匹配的用户名密码。
*   也可以通过 set_password()函数更改密码。您可以简单地从 django.contrib.auth.models 导入用户中完成此操作。u = user . objects . get(username = ' Jane ')，然后 u.set_password ('new password ')，并从 u.save()保存新密码。
*   如果您已经从认证系统的管理页面安装了 Django admin，您可以参考以下链接更改用户密码:[https://docs . Django project . com/en/1.11/topics/auth/default/# auth-admin](https://docs.djangoproject.com/en/1.11/topics/auth/default/#auth-admin)
*   该系统还提供了允许所有用户随时更改密码的表单和视图。

请记住，当您更改密码时，系统会自动将您从所有活动会话中注销。

# 如何认证用户

要验证用户，您可以使用' authenticate()'函数来验证用户凭证。该函数使用凭据作为关键字参数，使用默认情况下的用户名和密码值来验证每种情况，如果给定的凭据对后端有效，则返回用户对象。当提供的凭据对后端无效时，或者如果指定的后端以 PermissionDenied 响应，系统将返回 None 响应。

当一个未经验证的请求被拒绝时，您将得到一个错误代码，比如 HTTP 401 未授权或 HTTP 403 权限被拒绝。因此，确保为添加到系统中的每个用户提供正确的用户名和密码值非常重要。