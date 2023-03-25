# 如何构建微型 Httpd 容器

> 原文：<https://medium.com/hackernoon/how-to-build-a-tiny-httpd-container-ae622c37db39>

![](img/8c21dd687d098819e30baee3d3371c57.png)

**TL；DR:** [Smith](https://github.com/oracle/smith) 可以将一个 69MB [的 apache httpd](https://httpd.apache.org/) 容器缩小到 2MB 以下，从而节省 97%的空间，同时使其更加安全和易于操作。然而，构建[微容器](https://blogs.oracle.com/developers/the-microcontainer-manifesto)可能需要一些复杂的修改，所以使用该工具进行一些练习是有用的。

Smith 是一个[微容器](https://blogs.oracle.com/developers/the-microcontainer-manifesto)构建器，过去几个月我一直在为它工作。[自述文件](https://github.com/oracle/smith/blob/master/README.md)展示了如何构建一个超简单的微容器，但是有些软件可能更难“微型化”。Apache httpd 是棘手应用程序的一个很好的例子，因为它包括配置文件，有一些复杂的权限要求，并加载动态库。所有这些都需要一些基本步骤之外的额外步骤。下面我将详细介绍构建一个工作的 httpd 微容器的过程。

# 第一步

Smith 可以使用 mock 直接从 yum 存储库和 rpm 构建，但是它也可以“微型化”现有的 docker 容器。出于这个过程的目的，我们将使用 Docker Hub 上的官方 [httpd 容器作为我们的基础。最新的标签是由 debian jessie 构建的，压缩后重 69MB。还有一个阿尔卑斯山的图像，在一个更瘦的 28MB。我将使用基于 debian 的最新图像，但是也可以使用 alpine 图像执行类似的过程。](https://hub.docker.com/_/httpd/)

第一步是下载并安装 smith:

```
# before starting ensure you have golang 1.6 or later installed
git clone github.com/oracle/smith
cd smith
make
sudo make install
```

现在我们为我们的构建创建一个新目录:

```
mkdir smith-httpd
cd smith-httpd
```

接下来，我们下载 httpd 映像，这样我们就可以用它作为基础。(请注意，您还可以在 smith.yaml 中将包指定为完整的下载路径，但是图像不会被缓存，因此每次我们进行构建时都会重新下载。因为我们要构建很多次，所以最好手动下载图像。)

```
smith download -r [https://registry-1.docker.io/library/httpd](https://registry-1.docker.io/library/httpd) \
-i httpd.tar.gz
```

下一步是创建一个基本 yaml 文件。我不知道 httpd 二进制文件安装在映像中的什么位置，所以我们只能猜测它的位置。`-DFOREGROUND`的 arg 告诉 httpd 不要后台化，以便 docker 可以以通常的方式获取日志:

```
cat << EOF > smith.yaml
type: oci
package: httpd.tar.gz
paths:
- /usr/bin/httpd
cmd:
- /usr/bin/httpd
- -DFOREGROUND
EOF
```

现在我们可以尝试构建:

```
smith -i smith-httpd.tar.gz
# …
# WARN[0002] chmod failed: chmod: cannot access ‘/usr/bin/httpd’: No such file or directory
# WARN[0002] Could not make paths readable: Process chmod -R go+rX /usr/bin/httpd exited with status 1
# …
```

看起来/usr/bin/httpd 不是正确的位置。幸运的是，smith 将源图像中的图层解压缩到一个目录中，这样我们就可以四处查看了。目录是`/tmp/smith-unpack-$(id -u)`。让我们找到正确的二进制:

```
find /tmp/smith-unpack-$(id -u) -name httpd
# /tmp/smith-unpack-1000/usr/local/apache2/bin/httpd
```

看起来二进制文件已经安装到了`/usr/local/apache2/bin/httpd`中。事实上，如果我们在解压缩后的图像中稍微看一下，看起来所有的东西都已经安装在`/usr/local/apache2`下了。让我们走一点捷径，只获取整个目录:

```
cat << EOF > smith.yaml
type: oci
package: httpd.tar.gz
paths:
- /usr/local/apache2/
cmd:
- /usr/local/apache2/bin/httpd
- -DFOREGROUND
EOF
```

现在我们可以重建了。因为我们没有改变我们的源映像，所以当我们构建:

`smith -f -i smith-httpd.tar.gz`

# 测试我们的形象

Docker [还不支持](https://github.com/moby/moby/pull/33355)通过 docker load 直接导入 oci 映像，所以我们必须将映像推送到存储库来使用它。Docker Hub 工作正常。创建帐户并创建新存储库后，您可以按如下方式推送您的映像:

```
smith upload -r \
https://<user>:<pass>[@registry](http://twitter.com/registry)-1.docker.io/<user>/<repo> \
-i smith-httpd.tar.gz
```

如果您想将图像标记为除了`latest`之外的东西，您可以选择将`:<tag>`附加到存储库中。上传完成后，您可以通过`docker run -it --rm <user>/<repo>`运行图像，或者使用我构建并上传的图像继续操作:

```
docker run -it --rm vishvananda/smith-httpd:first
# …
# /usr/local/apache2/bin/httpd: error while loading shared libraries: libuuid.so.1: cannot open shared object file: No such file or directory
```

嗯，看起来不太管用。

# 修复我们的形象

Smith 足够聪明，可以下载为映像选择的二进制文件所需的任何库，只要它们在运行时没有通过 dlopen 打开。不幸的是，apache 确实 dlopen 了一些库，包括上面错误消息中看到的`libuuid.so.1`。有时，您可以通过在二进制文件上运行`strings`找到可能打开的库，但是我发现的最好的方法是尝试运行二进制文件，看看什么会失败。通常错误信息会告诉你到底需要什么文件。

在这种情况下，我们需要添加`/lib/x86_64-linux-gnu/libuuid.so.1`到我们的图像中。这很容易做到，只需将文件名附加到 yaml 文件的路径中。重建并重新推送映像后，记得使用新标签或使用`docker pull <user>/<repo>`强制其在`docker run`之前获得更新版本。如果您运行新的映像，您将会看到一个类似的抱怨`librt.so.1`的错误消息。为了节省您的时间，这里有一个新的 yaml，其中包含您需要的动态加载库的完整列表:

```
cat << EOF > smith.yaml
type: oci
package: httpd.tar.gz
paths:
- /usr/local/apache2/
- /lib/x86_64-linux-gnu/libuuid.so.1
- /lib/x86_64-linux-gnu/librt.so.1
- /lib/x86_64-linux-gnu/libdl.so.2
- /lib/x86_64-linux-gnu/libexpat.so.1
- /lib/x86_64-linux-gnu/libgcc_s.so.1
- /lib/x86_64-linux-gnu/libcrypt.so.1
cmd:
- /usr/local/apache2/bin/httpd
- -DFOREGROUND
EOF
```

在另一次构建/上传/拉取之后，我们可以再次运行它:

```
docker run -it --rm vishvananda/smith-httpd:second
# …
# AH00543: httpd: bad user name daemon
```

为了理解这里出了什么问题，我们需要稍微跑题一下。在大多数安装中，apache httpd 将以 root 用户身份运行。它通常需要以 root 用户身份运行，以便能够绑定 1024 以下的端口。当然，让您的 web 服务器以 root 用户身份运行会有安全风险，因此它会放弃配置文件中指定的用户/组的权限。

在理想情况下，我们应该从 httpd 配置文件中删除 User 和 Group 命令，并以非特权用户的身份运行二进制文件。这是可以做到的，但它要求我们以下列方式之一解决缺少绑定低端口的问题:

1.  更改配置，使 httpd 侦听非特权端口
2.  将`--sysctl net.ipv4.ip_unprivileged_port_start=0`传递给`docker run`(需要内核> = 4.11)
3.  将`--cap-add NET_BIND_SERVICE`传递给`docker run`，并在 httpd 二进制文件上设置适当的能力位(docker 中有对环境能力的短暂支持，这将消除设置能力位的需要，但它被还原，尚未返回到 docker 中)。

这些选项并不适合所有人，所以 httpd 映像的创建者使用了可靠的方法，即以 root 用户身份开始，然后转到另一个用户。一般来说，我们不希望微容器以 root 用户身份运行，但是 smith 支持这种情况。为了启用这个特性，我们需要在 yaml 中设置两个布尔值:`root`和`nss`。`root`设置镜像以 root 用户身份运行，而`nss`安装所需的 linux 位以使用户名和组查找在容器中工作。让我们用这些设置创建一个新的 yaml:

```
cat << EOF > smith.yaml
type: oci
package: httpd.tar.gz
nss: true
root: true
paths:
- /usr/local/apache2/
- /lib/x86_64-linux-gnu/libuuid.so.1
- /lib/x86_64-linux-gnu/librt.so.1
- /lib/x86_64-linux-gnu/libdl.so.2
- /lib/x86_64-linux-gnu/libexpat.so.1
- /lib/x86_64-linux-gnu/libgcc_s.so.1
- /lib/x86_64-linux-gnu/libcrypt.so.1
cmd:
- /usr/local/apache2/bin/httpd
- -DFOREGROUND
EOF
```

为了避免任何权限问题，我们还希望 apache 将映像中的文件归属于拥有这些文件的用户。用 smith 构建的微容器中所有文件的默认`user:group`是`smith:smith`。我们必须修改 httpd 配置来使用这些名称。此外，按照惯例，我们将微容器进程的配置文件放在`/read`中，所以让我们创建一个覆盖图来完成这项工作:

```
# make an overlay read directory
mkdir -p rootfs/read
# copy the config file out of the image
cp /tmp/smith-unpack-$(id -u)/usr/local/apache2/conf/httpd.conf \
rootfs/read/
# replace the file in our image with symlink to our file
mkdir -p rootfs/usr/local/apache2/conf/
ln -s /read/httpd.conf rootfs/usr/local/apache2/conf/httpd.conf
```

现在，我们可以用所需的设置替换用户和组:

```
sed -i -e ‘s/User daemon/User smith/’ \
-e ‘s/Group daemon/Group smith/’ rootfs/read/httpd.conf
```

在构建/上传/拉取之后，我们可以运行容器并在主机上公开一个端口:

```
docker run -it -p 80:80/tcp --rm vishvananda/smith-httpd:third
```

来自另一个 shell 的快速 curl 验证 httpd 正在工作:

```
curl localhost
# <html><body><h1>It works!</h1></body></html>
```

# 变得真正“微”

现在容器可以工作了，我们可以进一步最小化它。apache2 目录中实际上有相当多的无关内容。我们使用的唯一 bin 文件是 httpd 本身。我们确实需要这些模块，但是我们可以只挑选出那些在`httpd.conf`中启用的模块。这是我们的最终构建，所以我们也可以切换到直接从 Docker Hub 获取图像:

```
cat << EOF > smith.yaml
package: [https://registry-1.docker.io/library/httpd](https://registry-1.docker.io/library/httpd)
nss: true
root: true
paths:
- /usr/local/apache2/bin/httpd
# dynamic dependencies
- /lib/x86_64-linux-gnu/libuuid.so.1
- /lib/x86_64-linux-gnu/librt.so.1
- /lib/x86_64-linux-gnu/libdl.so.2
- /lib/x86_64-linux-gnu/libexpat.so.1
- /lib/x86_64-linux-gnu/libgcc_s.so.1
- /lib/x86_64-linux-gnu/libcrypt.so.1
# modules
- /usr/local/apache2/modules/mod_authn_file.so
- /usr/local/apache2/modules/mod_authn_core.so
- /usr/local/apache2/modules/mod_authz_host.so
- /usr/local/apache2/modules/mod_authz_groupfile.so
- /usr/local/apache2/modules/mod_authz_user.so
- /usr/local/apache2/modules/mod_authz_core.so
- /usr/local/apache2/modules/mod_access_compat.so
- /usr/local/apache2/modules/mod_auth_basic.so
- /usr/local/apache2/modules/mod_reqtimeout.so
- /usr/local/apache2/modules/mod_filter.so
- /usr/local/apache2/modules/mod_mime.so
- /usr/local/apache2/modules/mod_log_config.so
- /usr/local/apache2/modules/mod_env.so
- /usr/local/apache2/modules/mod_headers.so
- /usr/local/apache2/modules/mod_setenvif.so
- /usr/local/apache2/modules/mod_version.so
- /usr/local/apache2/modules/mod_unixd.so
- /usr/local/apache2/modules/mod_status.so
- /usr/local/apache2/modules/mod_autoindex.so
- /usr/local/apache2/modules/mod_dir.so
- /usr/local/apache2/modules/mod_alias.so
cmd:
- /usr/local/apache2/bin/httpd
- -DFOREGROUND
EOF
```

为了遵循微容器约定，我们还想对 conf 和 rootfs 做一些调整:

```
# move the mime.types config file into the read directory
cp /tmp/smith-unpack-$(id -u)/usr/local/apache2/conf/mime.types \
rootfs/read/
ln -s /read/mime.types rootfs/usr/local/apache2/conf/mime.types
# create a directory for serving files
mkdir -p rootfs/read/www
# copy the index file into our new directory
cp /tmp/smith-unpack-$(id -u)/usr/local/apache2/htdocs/index.html \ rootfs/read/www/
# change the serve directory in the config
# (note we could also have accomplished this with a symlink)
sed -i ‘s;/usr/local/apache2/htdocs;/read/www;’ \ rootfs/read/httpd.conf
# store the pidfile in /run
sed -i ‘/Group smith/a PidFile /run/httpd.pid’ rootfs/read/httpd.conf
```

在构建/上传/拉取之后，我们尽可能安全地运行我们的微容器:

```
docker run -it -p 80:80/tcp --read-only --tmpfs /run \
-v httpd-read:/read:ro --rm vishvananda/smith-httpd
```

我们可以访问主机上的服务目录，因此我们可以进行更改:

```
sudo sed -i ‘s/It works/Hello world/’ \
/var/lib/docker/volumes/httpd-read/_data/www/index.html
curl localhost
# <html><body><h1>Hello world!</h1></body></html>
```

# 从 Yum 存储库构建

使用 mock 的基于 yum 的构建惊人地相似。在[设置好模拟](https://fedoraproject.org/wiki/Using_Mock_to_test_package_builds#How_do_I_set_up_Mock.3F)后，你可以简单的用`package: httpd`替换 yaml 中的包行。当然，这些文件将位于与 docker 映像不同的位置，在这种情况下，没有要研究的解压缩映像。

要找到您想要取出的文件，在第一次构建后使用`/usr/bin/mock --shell`在模拟 chroot 中查看。您还可以使用`/usr/bin/mock --copyout`将文件从 chroot 复制到您的本地目录。构建/上传/运行过程是相同的，我鼓励您尝试在没有明确说明的情况下生成微容器。

# 结论

我们的微容器的第一个工作版本只有 6MB，最终版本不到 2MB。这还不到原容器大小的 3%!此外，我们已经删除了所有无关的二进制文件，并将容器的文件系统设置为只读，从而改善了容器本身的操作特性和安全性。我希望这能启发你和 smith 一起开发一些其他的微容器。

[![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)