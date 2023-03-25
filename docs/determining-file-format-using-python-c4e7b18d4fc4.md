# 使用 Python 确定文件格式

> 原文：<https://medium.com/hackernoon/determining-file-format-using-python-c4e7b18d4fc4>

# 史前史

大家好！最近，我遇到了一个问题:由于无法解释的原因，存储卡开始将所有文件移动到 LOST。没有任何扩展名的目录文件夹。长期以来，这里积累了 500 多种不同类型的文件:图片、视频、音频、文档。不可能独立理解[文件](https://hackernoon.com/tagged/file)的格式，所以我开始寻找一种以编程方式解决这个问题的方法。

# 寻找决策

我不想使用 web 服务或程序形式的现成解决方案，所以有一个想法是编写一个控制台实用程序，它将检查所有文件并自动安装扩展。 [Python](https://hackernoon.com/tagged/python) 被选中来编写这个实用程序。对合适的模块和库的搜索没有带来结果，原因有几个:

*   缺乏开发商的支持
*   功能过多
*   缺乏对新版本 Python 的支持
*   过度的代码复杂性

在众多的库中，python-magic 非常受欢迎(GitHub 上几乎有 1000 颗星)。这是 libmagic 库的包装器。但是如果没有 Unix 库的 DLL，就不可能在 Windows 下使用 python-magic。所以这个选项不够好。

# 问题的解决方案

从上面开始，我决定不使用第三方库和模块，不用它们来解决问题。在对如何实现这一任务的信息进行了简短的搜索之后，唯一正确的方法是通过文件的签名(也称为“幻数”)来确定格式。

文件签名是提供文件格式定义的一组字节。该签名具有以下十六进制表示形式:

```
50 4D 4F 43 43 4D 4F 43
```

幸运的是，互联网上有两个不错的网站，有很多不同格式的签名。最常见的格式成为目标。
事实证明，有些签名适用于不同的文件格式，比如微软 Office 文件的签名。基于此，在某些情况下，有必要返回合适的文件扩展名列表。

```
print(get("D:\\some_ms_office_document")) # prints ['doc', 'ppt', 'xls']
```

此外，签名通常与文件开头有一个偏移量，如 3GP 多媒体容器。

# 1.汇编数据列表

作为数据列表，我决定使用一个 JSON 文件，带有“data”对象，它的值将是以下形式的对象数组:

```
{"format": "jpg", "offset": 0, "signature": ["FF D8 FF E0", "FF D8 FF E1", "FF D8 FF E2", "FF D8 FF E8"]}
```

其中:

*   格式—文件格式；
*   offset —签名距文件开头的偏移量；
*   签名—适用于指定文件格式的签名数组。

# 2.编写实用程序

导入必要的模块:

```
import os
import json
```

读取数据列表:

```
abspath = os.path.abspath(os.path.dirname(__file__))
data = json.loads(open(os.path.join(abspath, "data.json"), "r", encoding="utf-8").read())["data"]
```

太好了，数据列表加载完毕。现在我们将文件作为字节数组来读取。我们将只读取前 32 个字节，因为确定通用格式并不需要更多，完整读取一个大文件将需要很长时间。

```
file = open("path_to_the_file", "rb").read(32)
```

如果您打印' file '变量，您将会看到类似如下的内容:

```
\x90\x00\x03\x00\x00\x00\x04
```

现在字节必须转换成十六进制系统:

```
hex_bytes = " ".join(['{:02X}'.format(byte) for byte in file])
```

接下来，我们创建一个列表，其中将添加适当的格式:

```
out = []
```

现在，我们创建一个循环确定文件格式的结构:

```
for element in data:
        for signature in element["signature"]:
            offset = element["offset"]*2+element["offset"]
            if signature == hex_bytes[offset:len(signature)+offset].upper():
                out.append(element["format"])
```

关于这个字符串:

```
offset = element["offset"]*2+element["offset"]
```

由于我们的字节表示为一个字符串，两个符号表示一个字节，我们将偏移量乘以 2，并在“字节”之间添加空格数。

我们唯一要做的就是输出一个合适格式的列表，用‘out’变量表示。

```
print(out) # prints something like ['extension_1', 'extension_2']
```

# 结论

事实证明，各种项目都面临着识别文件格式的需求，所以我决定以开源方式发布我的解决方案，作为 Python 的一个模块，名为 fleep[link to the GitHub page](https://github.com/floyernick/fleep)。您可以使用标准 python 实用程序“pip”安装该模块:

```
pip install fleep
```

GitHub 项目页面上还有使用示例和支持的文件格式的完整列表。
我每天都在改进 fleep，增加新的功能和格式。您可以在您的项目中使用它:)

# 感谢关注！

附注:我很高兴听到你对我的模块的意见。
P.P.S .英语不是我的母语，因此，请原谅我的任何错误:)