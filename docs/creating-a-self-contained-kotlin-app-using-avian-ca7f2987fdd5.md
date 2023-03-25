# 使用 Avian 创建自包含的 Kotlin 程序

> 原文：<https://medium.com/hackernoon/creating-a-self-contained-kotlin-app-using-avian-ca7f2987fdd5>

Avian 是一个轻量级的 JVM，它允许你创建独立的应用程序。这意味着你可以在不强迫用户安装任何 JRE 的情况下发布你的应用。在这里，我们将遵循简单的步骤，允许我们使用 Kotlin 创建一个独立的 hello-world 应用程序。

# 为什么是科特林？

Kotlin 是一种很有潜力的伟大语言。我不打算介绍它的所有优点，因为在我之前已经有很多人做过了。所以我们将把它留在那里，这是一门很有潜力的伟大语言。如果你想知道为什么，网上有很多信息。

# 为什么是禽类？

Java 面临的最大挑战之一是它需要一个 Java 运行时环境。这意味着，如果我编写一个 Java 程序，我的每个用户都需要下载、安装，甚至配置 JRE，然后才能启动我的程序。在某些情况下，如果他们还没有 JRE(或者如果他们有 JRE 的不兼容版本),人们会选择不安装某些软件来避免麻烦。)

# 结果呢

我们要完成的是一个用于 macOS 的 1.9MB 二进制文件或用于 Linux 的 2.2MB 二进制文件(做了一些小的修改，它应该可以在其他支持的平台上工作，见[这里](https://github.com/ReadyTalk/avian/blob/master/README.md)，一旦执行，它将问候用户并打印它收到的所有参数。像这样:

```
$ ./hello Miguel Castiblanco
Hello from Kotlin and Avian
args:
-Miguel
-Castiblanco
```

# 1.开始之前

*   $JAVA_HOME 必须指向正确的位置
*   必须安装 Kotlin，kotlinc (Kotlin 编译器)必须在$PATH 中
*   下面的命令是为在 macOS 上工作而编写的。除非另有说明，否则通过将 macosx 更改为 linux，以及架构(如果需要的话),该命令应该可以在 Linux 中工作。)例如 *macosx-x86_64* 会变成 *linux-i386* 或 *linux-x86_64*
*   本教程基于 Avian 的[自述](https://github.com/ReadyTalk/avian/blob/master/README.md)。我的大部分工作是让它与 Kotlin 而不是 Java 一起工作，并把它放在对 Avian 新手有意义的指令组中，同时解释我们在做什么。

# 2.获取和构建 Avian

让我们从获取最新版本的 Avian 开始:

```
$ git clone [https://github.com/ReadyTalk/avian.git](https://github.com/ReadyTalk/avian.git)
```

然后用默认配置构建它，并测试它是否工作

```
$ cd avian
$ make
$ build/macosx-x86_64/avian -cp build/macosx-x86_64/test Hello
```

最后一个命令应该显示“hello，world！”如果一切正确的话。

# 3.编写我们简单的 Kotlin 程序，并打包

让我们创建一个文件夹，将我们的小 Kotlin 脚本放在那里，并将其打包到一个 jar 中。

```
$ cd ../
$ mkdir helloKotlin && cd helloKotlin
$ cat >Hello.kt <<EOF
fun main(args: Array<String>) {
   println("Hello from Kotlin and Avian")
   println("args: ")
   args.forEach {
      println("-\$it")
   }
}
EOF
$ kotlinc Hello.kt -include-runtime -d boot.jar
```

注意，我们正在编译 Kotlin 的运行时，这意味着如果你运行 un *zip -l boot.jar* 你会看到所有 Kotlin 的类。这很重要，因为我们需要一个独立的应用程序。

# 4.准备将 Avian 的运行时与 Kotlin 的合并

我们将获得 Avian 的运行时并提取它，还将获得用 Avian 创建二进制文件所需的文件。

```
$ ar x ../avian/build/macosx-x86_64/libavian.a
$ mkdir avian-cp
$ cp ../avian/build/macosx-x86_64/classpath.jar avian-cp/avian-cp.jar
$ cd avian-cp/
$ unzip avian-cp.jar && rm -rf avian-cp.jar
```

# 5.将 Avian、Kotlin 和我们的应用程序合并在一个 jar 中

这里我们创建了一个 jar，它包含了 Avian 的运行时、Kotlin 的运行时和我们的小应用程序。我们将把之前从 *avian-cp.jar* 中提取的所有内容合并到 *boot.jar* (其中已经有了 Kotlin 和我们的代码。)

```
$ mv ../boot.jar .
$ zip -r boot.jar META-INF avian dalvik java libcore sun
$ mv boot.jar ../ && cd ../
```

如果你现在运行 un *zip -l boot.jar* ，你会看到所有的类现在都在一起很开心。

# 6.创建二进制文件

现在我们有了一个独立的罐子。剩下的只是使用 Avian 创建一个二进制文件。首先，我们从 jar 中创建一个对象。

```
$ ../avian/build/macosx-x86_64/binaryToObject/binaryToObject boot.jar \boot-jar.o _binary_boot_jar_start _binary_boot_jar_end macosx x86_64
```

下面的命令将为我们的二进制文件创建 c++主类。请注意， *FindClass* 正在寻找 **HelloKt** ，因为用 *kotlinc* 编译的类会有一个 *Kt* 后缀( *javac* 将*Hello.java*编译成 *Hello.class* ，而 *kotlinc* 将 *Hello.kt* 编译成 *HelloKt.class*

```
$ cat >embedded-jar-main.cpp <<EOF
#include "stdint.h"
#include "jni.h"
#include "stdlib.h"#if (defined __MINGW32__) || (defined _MSC_VER)
#  define EXPORT __declspec(dllexport)
#else
#  define EXPORT __attribute__ ((visibility("default"))) \
  __attribute__ ((used))
#endif#if (! defined __x86_64__) && ((defined __MINGW32__) || (defined _MSC_VER))
#  define SYMBOL(x) binary_boot_jar_##x
#else
#  define SYMBOL(x) _binary_boot_jar_##x
#endifextern "C" {extern const uint8_t SYMBOL(start)[];
  extern const uint8_t SYMBOL(end)[];EXPORT const uint8_t*
  bootJar(size_t* size)
  {
    *size = SYMBOL(end) - SYMBOL(start);
    return SYMBOL(start);
  }} // extern "C"extern "C" void __cxa_pure_virtual(void) { abort(); }int
main(int ac, const char** av)
{
  JavaVMInitArgs vmArgs;
  vmArgs.version = JNI_VERSION_1_2;
  vmArgs.nOptions = 1;
  vmArgs.ignoreUnrecognized = JNI_TRUE;JavaVMOption options[vmArgs.nOptions];
  vmArgs.options = options;options[0].optionString = const_cast<char*>("-Xbootclasspath:[bootJar]");JavaVM* vm;
  void* env;
  JNI_CreateJavaVM(&vm, &env, &vmArgs);
  JNIEnv* e = static_cast<JNIEnv*>(env);jclass c = e->FindClass("HelloKt");
  if (not e->ExceptionCheck()) {
    jmethodID m = e->GetStaticMethodID(c, "main", "([Ljava/lang/String;)V");
    if (not e->ExceptionCheck()) {
      jclass stringClass = e->FindClass("java/lang/String");
      if (not e->ExceptionCheck()) {
        jobjectArray a = e->NewObjectArray(ac-1, stringClass, 0);
        if (not e->ExceptionCheck()) {
          for (int i = 1; i < ac; ++i) {
            e->SetObjectArrayElement(a, i-1, e->NewStringUTF(av[i]));
          }e->CallStaticVoidMethod(c, m, a);
        }
      }
    }
  }int exitCode = 0;
  if (e->ExceptionCheck()) {
    exitCode = -1;
    e->ExceptionDescribe();
  }vm->DestroyJavaVM();return exitCode;
}
EOF
```

现在我们将把 C++类编译成一个对象，并链接所有的对象(注意，在第 4 步中，我们将一堆 Avian 的对象复制到当前文件夹中)以最终创建我们的二进制文件，它将被称为 *hello* 。

**对于 macOS:**

```
$ g++ -I$JAVA_HOME/include -I$JAVA_HOME/include/darwin \
     -D_JNI_IMPLEMENTATION_ -c embedded-jar-main.cpp -o main.o
$ g++ -rdynamic *.o -ldl -lpthread -lz -o hello -framework CoreFoundation
$ strip -S -x hello
```

**对于 Linux:**

```
$ g++ -I$JAVA_HOME/include -I$JAVA_HOME/include/linux \
     -D_JNI_IMPLEMENTATION_ -c embedded-jar-main.cpp -o main.o
$ g++ -rdynamic *.o -ldl -lpthread -lz -o hello
$ strip --strip-all hello
```

# 7.运行它

现在我们终于可以运行它，看看它是如何工作的

```
$ ./hello Kotlin Avian
Hello from Kotlin and Avian
args:
-Kotlin
-Avian
$ ls -lha hello
-rwxr-xr-x 1 starcarr starcarr 2.0M Feb  8 17:03 hello
```

# 结论

*   这个过程是漫长的，当然，它有可能被转换成一个脚本，为我们做所有的工作。也就是说，至少花时间这样做一次是很重要的，这样才能理解 Avian 是如何工作的，这样我们就可以在出现问题时进行故障排除
*   Avian 正在向独立的 Kotlin 和 Java 应用程序敞开大门，这将允许我们创建脚本或实用程序，这些程序可以很容易地在无 JRE 的机器上分发
*   应用程序的总大小是 1.9MB(在 Linux 中是 2.2MB)，这表明 Avian 是轻量级的

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是 [@AMI](http://bit.ly/atAMIatAMI) 家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 要了解更多信息，[请阅读我们的“关于”页面](https://goo.gl/4ofytp) , [喜欢/在脸书给我们发消息](http://bit.ly/HackernoonFB)，或者简单地，[发推文/DM @HackerNoon。](https://goo.gl/k7XYbx)
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！