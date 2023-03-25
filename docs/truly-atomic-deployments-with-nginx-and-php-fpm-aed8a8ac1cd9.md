# NGINX 和 PHP-FPM 真正的原子部署

> 原文：<https://medium.com/hackernoon/truly-atomic-deployments-with-nginx-and-php-fpm-aed8a8ac1cd9>

**注意:**这个解决方案只有在 NGINX 和 PHP-FPM 都驻留在同一个服务器上时才有效。

在实时服务器上进行“git pull”部署并不理想，因为磁盘上的所有文件不会在同一毫秒内发生变化。从一个版本的代码开始的请求可能会访问其他文件，这些文件可能会在请求过程中被更新。为了获得真正的原子部署，像 Capistrano 这样的 PHP 部署应用程序有一个指向当前构建的符号链接，一旦新的构建文件夹准备好了，就简单地将符号链接更新到新的构建。因为 Linux 本身没有任何磁盘缓存，所以将符号链接改为指向新的构建是原子性的。在同一毫秒，符号链接文件夹中的所有文件现在都指向它们的较新版本。

这个构建过程有一些问题。

# **设置:**

1)NGINX(web 服务器)和 PHP-FPM(PHP FastCGI 进程管理器)都驻留在同一个服务器上。

2) NGINX 从文档根目录(/var/www/app.com)提供服务，该根目录是指向当前版本的符号链接。

3)访问者请求【https://app.com/hello.php.】T4 NGINX 代理 PHP-FPM，并请求它执行/var/www/app . com/hello . PHP。PHP-FPM 将上述脚本的输出返回给 Nginx，Nginx 将它返回给访问者。

4) PHP Opcache 是一个缓存，它维护了(脚本路径->机器代码翻译)的映射。这种缓存可以防止一次又一次地解释 PHP 代码，并对性能产生很大影响。

5) PHP 的 Realpath 缓存是一个包含路径映射的缓存，用于 PHP 脚本中的相关文件。如果脚本中使用了大量的“require/require_once”和“include/include_once”语句，性能也会有很大的不同。

# **问题**

构建过程的最后一步是更改符号链接。在改变符号链接时，FPM 仍然在旧的构建文件夹中执行 PHP 脚本。

然后意识到。如果 NGINX 告诉 PHP-FPM 执行/var/www/app.com/hello.php，Opcache 将简单地获取旧的脚本机器翻译(因为 OpCache 仍然获得相同的文件路径)，除非它的缓存被刷新。

Opcache 本身向 Realpath 缓存查询一些路径映射。

## **如果我们可以刷新 Opcache 和 Realpath 缓存会怎么样**

即使每次构建时都刷新这两种缓存，在刷新缓存和符号链接更改之间也会有几毫秒的时间。在此期间，由于服务器上的传入请求，缓存将再次开始构建，刷新缓存将是无用的。

如果在进行符号链接更改后刷新缓存，那么旧代码将在刷新缓存和符号链接更改之间的时间内执行。

## **PHP-FPM 重装了怎么办**

这本身有两个问题:

a)在重新加载过程中，一些请求可能会丢弃/未完成/失败。

b)同样，在创建符号链接和开始重新加载 FPM 之间可能会有几毫秒的时间。在这段时间内，旧代码可能会再次执行，因为符号链接在此之前可能已经更改过了。

因此，这显然不是一个解决方案。

## **如果 NGINX 从新的构建文件夹(不是符号链接的那个)提供服务并重新加载会怎样？**

NGINX 可以从新的构建文件夹而不是符号链接文件夹提供服务，因此，消除了所有缓存问题。

每次构建时，可以在 NGINX conf 文件中动态更改文档根，然后可以重新加载 NGINX。

这是一个潜在的值得研究的解决方案。想避免重新加载 NGINX。

# **解决方案**

经过一番研究，我发现了一个易于实施的解决方案:

fastcgi _ param SCRIPT _ FILENAME $ document _ root $ fastcgi _ SCRIPT _ name；

=>

fastcgi _ param SCRIPT _ FILENAME $ real path _ root $ fastcgi _ SCRIPT _ name；

NGINX 配置中从$document_root 到$realpath_root 的简单更改将使 NGINX 传递实际的脚本路径(在跟随符号链接之后解析)。由于 PHP-FPM 现在获得了脚本的实际路径(每次构建时总是新的)，所有的缓存问题都会消失，因为所有文件的路径现在都是新的。

## **解决方案仍然无效**

解决方案没有成功。在启用了 PHP FPM 的访问日志和 NGINX 的调试日志以找出 NGINX 在对 FPM 的请求中传递的确切路径之后，结果是传递的路径类似于/var/www/app.com/hello.php

这是带有符号链接的路径，而不是绝对真实的路径。那么，为什么这不起作用呢？我当前版本的 NGINX 支持 Realpath 功能吗？经过一些研究，我发现在 NGINX 0.8 版中启用了 Realpath 功能。我的环境中有 1.4 版，因此应该可以工作。

我在 NGINX 中注释了 fastcgi_pass 这一行，只是为了确定这一行还在被拾取。NGINX 重新加载，应用程序仍然工作。我不知道 FPM 是如何知道该执行哪个文件的。然后，我意识到这一行可能会在某处被覆盖。事实证明这是正确的。

在下面两行，NGINX 配置显示如下:include/etc/NGINX/fastcgi _ params

这个文件有一个传递给 FPM 的常用 fastcgi 参数列表。我从这个文件中删除了 SCRIPT_FILENAME 参数，并重新加载了 NGINX，现在 NGINX 正在将真正的解析路径传递给 FPM。唷。

如果我将 fastcgi_pass 语句放在 include 语句下面，这种情况就可以避免，因为这样我的变量值就会覆盖该文件中的值。因为我的语句在它上面，所以文件中的变量覆盖了我定义的变量。

# **新的构建过程**

1)现在构建是完全原子化的，因为一个符号链接的改变足以告诉 NGINX 从新的文档根开始服务。

2)无需在每次构建后重新加载 NGINX 或 FPM。因此，每一个要求都可以得到满足。

3) NGINX 负责将每个 PHP 文件的绝对解析路径传递给 FPM，因此，新构建后的缓存问题不再存在。因此，在新的构建之后，不需要刷新 Opcache 或 Realpath 缓存。

4)如果在新版本上有任何问题，指向先前版本的符号链接更改就足够了:)

交叉发布于[https://kanishkdudeja . in/true-atomic-deployments-with-nginx-and-PHP-fpm/](https://kanishkdudeja.in/truly-atomic-deployments-with-nginx-and-php-fpm/)