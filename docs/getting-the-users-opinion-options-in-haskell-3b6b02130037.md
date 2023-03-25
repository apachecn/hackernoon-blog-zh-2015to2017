# 获取用户意见:Haskell 中的选项

> 原文：<https://medium.com/hackernoon/getting-the-users-opinion-options-in-haskell-3b6b02130037>

GUI 很难创建。幸运的是，我们经常可以通过命令行界面使代码可用。随着您开始编写更多的 Haskell 程序，您可能会在某个时候不得不这样做。

本文将介绍 CLI 的一些细节。特别是，我们将看看处理选项的基础。然后，我们将看到一些测试 CLI 行为的漂亮技术。

# 简单的样品

为了激发本文中的例子，让我们设计一个简单的程序，让用户输入一条**消息**。然后，我们将把消息打印到一个文件中一定的次数，并将用户的名字作为作者列在顶部。如果他们愿意，我们也允许他们将信息大写。因此，我们将从用户那里获得五条输入:

1.  他们想要的文件名
2.  把他们的名字放在最上面
3.  不管他们想不想大写
4.  信息
5.  重复次数

对于前三条信息，我们将使用**参数和选项**。然后我们会有一个**命令行提示**给另外两个。例如，我们坚持让用户将预期的文件名作为参数传递。然后，我们将选择用户希望放在顶部的名称。最后，我们将标记用户是否希望消息是大写的。这里有几个不同的程序调用。

```
>> run-cli “myfile.txt” -n “John Doe”
What message do you want in the file?
Sample Message
How many times should it be repeated?
5
```

这将把以下输出打印到`myfile.txt`:

```
From: John Doe
Sample Message
Sample Message
Sample Message
Sample Message
Sample Message
```

下面是另一次运行，这次输入有错误:

```
>> run-cli “myfile2.txt” -n “Jane Doe” -u
What message do you want in the file?
A new message
How many times should it be repeated?
asdf
Sorry, that isn't a valid number. Please enter a number.
3
```

该文件将类似于:

```
From: Jane Doe
A NEW MESSAGE
A NEW MESSAGE
A NEW MESSAGE
```

最后，如果我们没有得到正确的参数，我们应该得到一个用法错误:

```
>> run-cli
Missing: FILENAME -n USERNAMEUsage: CLIPractice-exe FILENAME -n USERNAME [-u]
  Comand Line Sample Program
```

# 获取输入

所以这个项目最重要的方面是**获取信息和重复**。我们暂时忽略这些选项。我们将打印几条消息，然后使用`getLine`函数获取它们的输入。他们没有办法给我们一个坏消息，所以这一节很简单。

```
getMessage :: IO String
getMessage = do
  putStrLn "What message do you want in the file?"
  getLine
```

但是他们可能会给我们一个我们无法解析的数字。因此，对于这个任务，我们将不得不建立一个循环，在这个循环中，我们不断地向用户询问一个数字，直到他们给我们一个好的值。在失败的情况下，这将是递归的。如果用户不输入有效的数字，他们将别无选择，只能通过其他方式终止程序。

```
getRepetitions :: IO Int
getRepetitions = do
  putStrLn "How many times should it be repeated?"
  getNumbergetNumber :: IO Int
getNumber = do
  rep <- getLine
  case readMaybe rep of
    Nothing -> do
      putStrLn "Sorry, that isn't a valid number. Please enter a number."
      getNumber
    Just i -> return i
```

一旦我们读取了输入，我们将输出到一个文件。在这种情况下，我们现在对所有选项进行硬编码。这是完整的节目单。

```
import Data.Char (toUpper)
import System.IO (writeFile)
import Text.Read (readMaybe)runCLI :: IO ()
runCLI = do
  let fileName = "myfile.txt"
  let userName = "John Doe"
  let isUppercase = False
  message <- getMessage
  reps <- getRepetitions
  writeFile fileName (fileContents userName message reps isUppercase)fileContents :: String -> String -> Int -> Bool -> String
fileContents userName message repetitions isUppercase = unlines $
  ("From: " ++ userName) :
  (replicate repetitions finalMessage)
  where
    finalMessage = if isUppercase 
     then map toUpper message 
    else message
```

# 解析选项

现在我们必须处理我们实际上如何**解析不同选项**的问题。我们可以用`getArgs`函数手工完成，但是这有点容易出错。一般来说，更好的选择是使用`Options.Applicative`库。我们将探索这个库允许的不同可能性。我们将使用三个不同的助手函数来处理我们需要的三部分输入。

我们要做的第一件事是构建一个数据结构来保存我们需要的不同选项。我们想知道要存储的文件名、顶部的名称以及大写状态。

```
data CommandOptions = CommandOptions
  { fileName :: FilePath
  , userName :: String
  , isUppercase :: Bool }
```

现在我们需要解析其中的每一个。我们将从大写值开始。我们拥有的最简单的解析器是`flag`函数。它告诉我们，如果一个特定的标志(我们称之为`-u`)存在，我们将大写消息，否则不。它用选项库这样编码:

```
uppercaseParser :: Parser Bool
uppercaseParser = flag False True (short 'u')
```

注意，我们在最后一个参数中使用了`short`来表示旗帜字符。我们也可以使用`switch`函数，因为这个标志只是一个布尔值，但是这个版本更通用。

现在我们将转移到文件名的参数**上。这使用了`argument`辅助函数。我们将使用一个字符串解析器(`str`)来确保获得实际的字符串。我们不会担心文件名有特殊的格式。注意，我们给这个参数添加了一些元数据。这告诉用户，如果他们没有使用正确的格式，他们错过了什么。**

```
fileNameParser :: Parser String
fileNameParser = argument str (metavar "FILENAME")
```

最后，我们将处理将什么名字放在顶部的选项。我们也可以把它作为一个论点，但是让我们看看这个选项是什么样的。自变量是**要求的位置参数**。另一方面，选项出现在特定标志之后的**。为了更好地显示错误消息，我们还在这里添加了元数据。我们的元数据的`short`部分确保它将使用我们想要的选项字符。**

```
userNameParser :: Parser FilePath
userNameParser = option str (short 'n' <> metavar "USERNAME")
```

现在我们必须组合这些不同的解析器，并添加一些关于我们程序的信息。

```
import Options.Applicative (execParser, info, helper, Parser, fullDesc, 
  progDesc, short, metavar, flag, argument, str, option)parseOptions :: IO CommandOptions
parseOptions = execParser $ info (helper <*> commandOptsParser) commandOptsInfo
  where
    commandOptsParser = CommandOptions <$> fileNameParser <*> userNameParser <*> uppercaseParser
    commandOptsInfo = fullDesc <> progDesc "Command Line Sample Program"-- Revamped to take options
runCLI :: CommandOptions -> IO ()
runCLI commandOptions = do
  let file = fileName commandOptions
  let user = userName commandOptions
  let uppercase = isUppercase commandOptions
  message <- getMessage
  reps <- getRepetitions
  writeFile file (fileContents user message reps uppercase)
```

现在我们完成了！我们使用这三种不同的解析器来构建命令对象。我们使用应用程序将操作链接在一起！然后我们把结果传递给我们的主程序。如果你不太熟悉[函子](https://mmhaskell.com/blog/2017/1/30/the-easiest-haskell-idiom)和[应用程序](https://mmhaskell.com/blog/2017/2/6/applicatives-one-step-further)，我们不久前在博客上讨论过这些。[刷新](https://mmhaskell.com/blog/2017/1/30/the-easiest-haskell-idiom)你的[内存](https://mmhaskell.com/blog/2017/2/6/applicatives-one-step-further)！

# IO 测试

现在我们的程序运行了，我们需要问自己如何测试它的行为。我们可以自己进行手工命令行测试，但是如果有一个自动化的解决方案就更好了。这里的关键是`Handle`抽象。

让我们先来看看一些基本的文件处理类型。

```
openFile :: FilePath -> IO Handle
hGetLine :: Handle -> IO String
hPutStrLn :: Handle -> IO ()
hClose :: Handle -> IO ()
```

通常当我们向文件写东西时，我们会为它打开一个句柄。我们对所有不同的操作使用句柄(而不是字符串名称)。完成后，我们关上把手。

好消息是`stdin`和`stdout`流实际上是完全相同的`Handle`类型！

```
stdin :: Handle
stdout :: Handle
```

这对我们的测试有什么帮助？第一步是抽象出我们正在使用的句柄。我们不想使用`print`和`getLine`，而是想使用`hGetLine`和`hPutStrLn`。然后我们把这些参数作为程序和函数的参数。让我们看看我们的阅读功能:

```
getMessage :: Handle -> Handle -> IO String
getMessage inHandle outHandle = do
  hPutStrLn outHandle "What message do you want in the file?"
  hGetLine inHandlegetRepetitions :: Handle -> Handle -> IO Int
getRepetitions inHandle outHandle = do
  hPutStrLn outHandle "How many times should it be repeated?"
  getNumber inHandle outHandlegetNumber :: Handle -> Handle -> IO Int
getNumber inHandle outHandle = do
  rep <- hGetLine inHandle
  case readMaybe rep of
    Nothing -> do
      hPutStrLn outHandle "Sorry, that isn't a valid number. Please enter a number."
      getNumber inHandle outHandle
    Just i -> return i
```

一旦我们完成了这些，我们就可以为我们的程序设置输入和输出句柄参数，如下所示。我们的包装器可执行文件将通过`stdin`和`stdout`:

```
-- Library File:
runCLI :: Handle -> Handle -> CommandOptions -> IO ()
runCLI inHandle outHandle commandOptions = do
  let file = fileName commandOptions
  let user = userName commandOptions
  let uppercase = isUppercase commandOptions
  message <- getMessage inHandle outHandle
  reps <- getRepetitions inHandle outHandle
  writeFile file (fileContents user message reps uppercase)-- Executable File
main :: IO ()
main = do
  options <- parseOptions
  runCLI stdin stdout options
```

现在我们的库 API 将句柄作为参数。这意味着在我们的测试代码中，我们可以传递我们想要的任何句柄给来测试代码。而且，正如您可能已经猜到的，我们将用文件来做这件事，而不是用`stdin`和`stdout`。我们将用预期的终端输出创建一个文件:

```
What message do you want in the file?
How many times should it be repeated?
```

我们将使用我们的输入创建另一个文件:

```
Sample Message
5
```

然后是我们期望创建的文件:

```
From: John Doe
Sample Message
Sample Message
Sample Message
Sample Message
Sample Message
```

现在我们可以编写一个调用库函数的测试。它将传递预期的参数对象以及正确的文件句柄。然后我们可以比较测试文件和输出文件的输出。

```
import Libimport System.IO
import Test.HUnitmain :: IO ()
main = do
  inputHandle <- openFile "input.txt" ReadMode
  outputHandle <- openFile "terminal_output.txt" WriteMode
  runCLI inputHandle outputHandle options
  hClose inputHandle
  hClose outputHandle
  expectedTerminal <- readFile "expected_terminal.txt"
  actualTerminal <- readFile "terminal_output.txt"
  expectedFile <- readFile "expected_output.txt"
  actualFile <- readFile "testOutput.txt"
  assertEqual "Terminal Output Should Match" expectedTerminal actualTerminal
  assertEqual "Output File Should Match" expectedFile actualFileoptions :: CommandOptions
options = CommandOptions "testOutput.txt" "John Doe" False
```

就是这样！我们还可以使用这个过程来添加关于错误情况的测试，比如当用户输入无效数字时。

# 摘要

编写命令行界面并不总是最容易的任务。如果用户没有给你想要的信息，获取用户的输入有时需要创建循环。那么处理争论会是一件很痛苦的事情。`Options.Applicative`库包含许多选项解析工具。它帮助您处理标志、选项和参数。当你准备好测试你的程序时，你会想要抽象出文件句柄。你可以在主可执行文件中使用`stdin`和`stdout`。但是当您测试时，您可以使用文件作为您的输入和输出句柄。

想试着写一个 CLI 但是还不了解 Haskell？没问题！下载我们的[入门清单](https://www.mmhaskell.com/checklist)，开始学习语言吧！

当你用可执行文件和测试套件做一个完整的项目时，你需要保持有条理！参加我们免费的 [Stack 迷你课程](http://academy.mondaymorninghaskell.com/p/your-first-haskell-project)来学习如何用 Stack 组织你的 Haskell。