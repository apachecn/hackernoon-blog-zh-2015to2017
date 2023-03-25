# 我是如何通过学习 Reflection 的工作在 Golang 创建一个最简单有用的库的

> 原文：<https://medium.com/hackernoon/how-i-made-a-simplest-and-useful-library-in-golang-by-learning-reflections-work-6192aa7ef8cf>

## 从做中学:在戈朗的反思

![](img/7697e56411f4b04e9769fafe842eae77.png)

Random text , searched from google images

几个月前，我在 golang 中创建了一个简单的库，以帮助 golang 工程师测试他们的功能。这只是一个简单的库，会根据它们的结构产生虚假和随机的数据。这个想法来自于我在每个函数中创建单元测试，创建不同的结构，这是浪费时间。

这里可以看到图书馆

[](https://github.com/bxcodec/faker) [## bxcodec/faker

### faker - Go (Golang)结构的假数据生成器

github.com](https://github.com/bxcodec/faker) 

如何使用它:

```
$ go get github.com/bxcodec/faker
```

然后导入到您的测试文件中。

这个简单的库是我利用 golang 中的反射特性制作的。这项工作很简单，你只需定义你的结构，我的库将根据你的结构为你生成一个虚拟数据。

示例:

当你运行的时候，你的对象将会充满随机数据:

```
$ go run main.go
$ {Int:37 String:LOftACEPfclEWQDBqAaaqyVis Bool:false SString:[yWgdk
  ZQtbTmi] Struct:{Number:36 Height:0.021622694665801242
  AnotherStruct:{Image:wUvHuSlyJYknWbqUbWtYGrMrZ}}}
```

即使你有一个嵌套的 struct，只要它们不指向 struct，它们就会安全地生成。

很好，你是怎么做到的？

## **倒影**

在 golang 中，有一个内置的包可以用于反射数据类型，它叫做`reflect`。你可以在官方文件中看到`reflect`能做什么【https://golang.org/pkg/reflect/

基本上，我只是**得到数据类型**，然后**根据它的类型**得到随机数据。

```
var a int64 

ref :=reflect.ValueOf(&a) //*will return reflect.Value objects ;* 
                          //*Pass with refferences for data* 
                          //*manipulation*

ref=reflect.Indirect(ref) //*accessing the value only that pointed by* 
                          //*ref*

kind:= ref.Kind() // *will return the kind of the reflected value*

if kind == reflect.Int64{ val:= int64(100)  //*Data will assigned. For fake data, this* 
                   //*should randomized* ref.Set(reflect.ValueOf(**val**)) //*Set the value*
}

fmt.Println("Value of a ", a) //*Will print 100*
```

活生生的例子你可以在这里运行

 [## 围棋运动场

### 操场服务不仅仅被官方 Go 项目使用(Go by Example 是另一个例子),我们…

play.golang.org](https://play.golang.org/p/wY1kBUFEFu) 

就这么简单。现在，我可以为我的结构生成一个虚拟数据，并在用随机数据进行自动化测试时帮助我。

但是，这个库仍然需要更多的改进，因为它仍然不能随机化结构数组的数据。我试过很多方法，还是不好，可能是做不到，也可能是还需要学习更多。

快乐编码…:)

*如果你有问题，或者需要更多的解释，或者有什么东西，我在这里不好解释，你可以从我的*[*LinkedIn*](https://www.linkedin.com/in/imantumorang/)*或者*[*email*](mailto:iman.tumorang@gmail.com)*问我。谢谢*

[](https://hackernoon.com/golang-clean-archithecture-efd6d7c43047) [## 在 Golang 尝试干净的建筑

### 独立的、可测试的和干净的

hackernoon.com](https://hackernoon.com/golang-clean-archithecture-efd6d7c43047) [](https://toolbox.kurio.co.id/implementing-grpc-service-in-golang-afb9e05c0064) [## 在戈兰实施 gRPC 服务

### 指南，使 gRPC 服务使用清洁的建筑在果兰。

toolbox.kurio.co.id](https://toolbox.kurio.co.id/implementing-grpc-service-in-golang-afb9e05c0064) [](https://toolbox.kurio.co.id/im-learning-to-use-flatbuffer-in-go-golang-here-what-i-learn-f7f8bf42f015) [## 我正在学习使用 Go (Golang)中的 Flatbuffer，下面是我所学到的

### 用于序列化数据的 Flatbuffer。

toolbox.kurio.co.id](https://toolbox.kurio.co.id/im-learning-to-use-flatbuffer-in-go-golang-here-what-i-learn-f7f8bf42f015)