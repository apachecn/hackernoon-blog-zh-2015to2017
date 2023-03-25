# 将蟒蛇与仙丹混合

> 原文：<https://medium.com/hackernoon/mixing-python-with-elixir-7a2b7ac6696>

![](img/cf8ccd41ed54852f3639f3b18fda7ee1.png)

*使用 Elixir app 中的 Python 模块生成和解码二维码*

运行 Elixir 的 Erlang VM 对于特定领域的应用程序来说是高效的，但是当涉及到其他操作，如数据处理和计算密集型的东西时就显得不够了。另一方面，Python 有一套丰富的包来处理数据处理和科学计算。

幸运的是，Erlang VM 允许使用 [Erlang 端口协议](http://erlang.org/doc/reference_manual/ports.html)与其他语言进行互操作。

ErlPort 是一个 Elixir 库，它使得使用 Erlang 端口协议将 Erlang 连接到许多其他编程语言变得更加容易。目前 Erlport 支持 [Python](http://erlport.org/docs/python.html) 和 [Ruby](http://erlport.org/docs/ruby.html) 。

在这篇文章中，我们将看到如何调用 Python 模块从我们的 Elixir 应用程序中生成二维码。

**Erlport Python 入门** 要使用 Erlport，只需添加到您的依赖项中

```
#mix.exs
{:erlport, "~> 0.9"}
```

一旦 Erlport 被添加到您的项目中，您就可以访问您的 elixir 代码中的`:*python*`模块。这个 Erlport python 模块允许您启动一个 Elixir 进程来执行 python 代码。

```
#creates and Elixir process for calling python functions
{:ok, pid} = :python.start()#get the current python version
:python.call(pid, :sys, :'version.__str__', [])
```

更好的是，Erlport 允许您配置 python 路径，这样您就可以
从特定目录加载定制的 python 模块！

```
#Automatically load all modules in directory with path /custom/modules/directory{:ok, pid} = :python.start([{:python_path, '/custom/modules/directory'}])
```

通过调用`start/1`返回的进程，您可以使用熟悉的 **MFA** - *模块*、*函数*、*参数*格式调用 python 模块中的函数

```
{:ok, pid} = :python.start([{:python_path, 'custom_modules_directory'}])
**module** = :test #python module to call 
**function** = :hello # function in module
**arguments** = ["World"] # list of arguments pass to function the function
result = :python.call(p, module, function, arguments)
```

如果 python 函数返回数据，它将被绑定到上面代码中的`result`变量。否则`:python.call/4`为不返回任何数据的 python 函数返回`:undefined`。

**让我们创建 Elixir 项目**在这个项目中，我们将创建一个简单的应用程序，将字符串编码为二维码图像。然后解码二维码图像文件，把字符串拿回来。我们将从我们的应用程序中调用 Python 模块( [**qrtools**](https://github.com/primetang/qrtools) )来进行编码和解码，而不是使用原生的 Elixir/Erlang lib。检查此处的[以设置 qrtools。

**1。** *创建灵药 OTP app*](https://github.com/primetang/qrtools)

```
$ mix new elixir_python_qrcode 
```

**2。** *添加依赖关系*

添加`erlport`到你的依赖关系`mix.exs`

```
defp deps do
    [
      # {:dep_from_hexpm, "~> 0.3.0"},
      # {:dep_from_git, git: "https://github.com/elixir-lang/my_dep.git", tag: "0.1.0"},
      **{:erlport, "~> 0.9"}**,
    ]
  end
```

然后安装项目依赖项。

```
$ mix deps.get
```

**3。** *让我们为 Erlport*

Create`lib/helper.ex`添加包装药剂模块，添加名为`ElixirPythonQrcode.Helper`的药剂模块并添加这些辅助函数。

```
defmodule ElixirPythonQrcode.Helper do
  @doc """
  ## Parameters
    - path: directory to include in python path (charlist)
  """
  def python_instance(path) when is_list(path) do
    {:ok, pid} = :python.start([{:python_path, to_charlist(path)}])
    pid
  end

  def python_instance(_) do
    {:ok, pid} = :python.start()
    pid
  end

  @doc """
  Call python function using MFA format
  """
  def call_python(pid, module, function, arguments \\ []) do
    pid
    |>:python.call(module, function, arguments)

  end
end
```

**4。** *为 main 添加模块代码。*

我们将有两个函数`encode/2`——它接受一些字符串和文件路径。它对数据进行编码，并将二维码图像写入给定的文件路径，以及`decode/1`——它接受二维码图像的文件路径，并解码得到原始数据。

我们来编辑一下`lib/elixir_python_qrcode.ex`

```
#lib/elixir_python_qrcode.exdefmodule ElixirPythonQrcode do
  @moduledoc """
  Documentation for ElixirPythonQrcode.
  """
  alias ElixirPythonQrcode.Helper

  def encode(data, file_path) do
    call_python(:qrcode, :encode, [data, file_path])
  end

  def decode(file_path) do
    call_python(:qrcode, :decode, [file_path])
  end defp default_instance() do
    #Load all modules in our priv/python directory
    path = [:code.priv_dir(:elixir_python_qrcode), "python"] 
          |> Path.join()
    Helper.python_instance(to_charlist(path))
  end

  # wrapper function to call python functions using
  # default python instance
  defp call_python(module, function, args \\ []) do
    default_instance()
    |> Helper.call_python(module, function, args)
  endend
```

**5。** *现在让我们创建我们的 python 模块*

创建包含我们的 python 函数的`priv/python/qrcode.py`模块。这些函数处理实际的 QR 码生成和解码。

```
#priv/python/qrcode.pydef decode(file_path):
    import qrtools
    qr = qrtools.QR()
    qr.decode(file_path)
    return qr.data

def encode(data, file_path):
    import qrtools
    qr = qrtools.QR(data.encode("utf-8"))
    return qr.encode(filename=file_path) 
```

**6。让我们测试一下我们的代码。**

```
$ iex -S mix
iex(1)> ElixirPythonQrcode.encode("Some test to encode", "qrcode.png")
0
iex(2)> ElixirPythonQrcode.decode("qrcode.png")
'Some test to encode'
iex(3)> ElixirPythonQrcode.decode("qrcode.png") |> to_string()
"Some test to encode"
```

就是这样。查看[github repo](https://github.com/badubizzle/elixir_python_qrcode)。

编码快乐！

查看[第二部分](/@badu_bizzle/mixing-python-with-elixir-ii-async-e8586f9b2d53)了解 Elixir 和 Python 之间的异步通信