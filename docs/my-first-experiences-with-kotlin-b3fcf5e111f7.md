# 我和科特林的第一次经历

> 原文：<https://medium.com/hackernoon/my-first-experiences-with-kotlin-b3fcf5e111f7>

## 一些可能对初学者有用的快速笔记

![](img/5fba2a916da3d9643eb9a496cb3d8d7a.png)

Kotlin 是一种新语言，至少大多数 Android 开发者听说过它，或者正在使用它来代替 T2 Java T3。

出于我的好奇心，我开始学习它，想知道它有什么吸引人的地方，让一些开发者为之疯狂！所以在我的学习过程中，我做了一些笔记，这些笔记可能看起来太小了，但包含了初学者和你以后搜索的关键词。

这是我喜欢在学习过程开始时做的笔记:

## 变量:

```
val immutableName: String = "Mohsen"var mutableName: String = "Mohsen"
var mutableName = "Mohsen"var nullableName: String? = nullval numbers = arrayListOf(1, 2, 3, 4, 5)
print("Even Numbers are ${numbers.filter { i -> i % 2 == 0 }}")val countries = listOf(Pair("Iran", "Tehran"), Pair("France", "Paris"), Pair("Germany", "Berlin"))for ((country, city) in countries) {
	print("Country: $country - City: $city")
}
```

## 功能:

```
// fun stands for function!fun addNumbers(first: Int, second: Int): Int {
	return first + second
}
addNumbers(10, 20)
addNumbers(first = 10, second = 20)
addNumbers(second = 1, first = 2) // !!!// you can have multiple params
fun addNumbers(vararg numbers: Int): Int {
	return numbers.sum()
}
addNumbers(1, 2, 3, 4, 5, 6) // default value
fun addTwoNumbers(first: Int, secound: Int = 0): Int {
	return first + secound
}
addTwoNumbers(2)
```

## **班级:**

```
// no fields in classes! only properties and functions
class Customer(val id: Int = 0, var name: String) { // secondary constructor
	constructor(id: Int) : this(id, "")

	// initialize method
	init() {
	}

	// public by default property
	val test = 0

	// visible inside Customer.kt
	private fun foo() {}

	// property is visible everywhere
	// setter is visible only in Customer.kt
	public var bar: Int = 5
		private set

	// visible inside the same module
	internal val baz = 6
}
```

## 数据类别:

```
// mutable data classes similar to AutoValue library which provide equals, toString, copy, etc.
data class Customer(val id: Int = 0, var name: String)
```

## 扩展功能:

```
fun String.convertSpacesToUnderscores(): String {
	return this.replace(" ", "_")
}print("This is my sample String.".convertSpacesToUnderscores())
```

## 空:

```
val x: String = null //error!
val x: String? = null// if x is not null range to 10
x?.rangeTo(10)
```

## 让:

```
var customer: Customer? = null
customer?.let {
	// it = user and not null and only read once!
	println(it.name)
}someMethod().let { result ->
	//method only run once!
}
```

## 任何:

```
fun getStringLen(obj: Any): Int? = if (obj is String) obj.length else null
```

## 同伴:

```
// the same as Static methods and you have to access the
// method like this: Customer.Companion.what()class Customer {
    companion object {
	fun what(customer: Customer): String {
		return customer.id.toString() + customer.name
	}
    }
}// If you want the function looks like java static methods
// like this: Customer.what() have to add JvmStatic
// It is useful for calling static methods from NDK libraries
class Customer {
    companion object {
        @JvmStatic
	fun what(customer: Customer): String {
		return customer.id.toString() + customer.name
	}
    }
}
```

## 包装功能:

```
// functions with no classes, just the same as java import static
package com.google.testfun packageFunction() {
	println("I am a package function!!")
}
```

## 列表，数组列表，映射:

```
val list = listOf("a", "b")
val mutableList = mutableListOf("a", "b")for (s in list) {
	// ...
}val map = mapOf("key" to "value")
val mutableMap = mutableMapOf("key" to "value")for ((key, value) in map) {
	// ...
}
```

## 接口:

```
interface TestInterface {
	fun run(t:Int): String

	fun test(){
		// default body!
	}
}
```

## 通用:

```
class Box<T>(t: T) {
	// ...
}
Val box: Box(1) // box: Box<Int>
```

## 枚举:

```
enum class Direction(val degree: Int) {
	NORTH(0), EAST(90), SOUTH(180), WEST(270)
}
```

## 铸造类型:

```
val y = x as Int
val y = x as? Int // nullable// smart cast
If (x is String)
	x.toUpperCase()
```

## 运算符重载:

```
operator fun plus(other: Point): Point = Point(x + other.x, y + other.y)
```

## 继承:

```
// all classes are final by default and for inheritance should be open
open class Shape {
}class Circle: Shape() {
}
```

## 中缀:

```
infix fun String.hello(name:String): String{
	return "Hello, $name. $this"
}"Test" hello "test" // == "Test".hello("test")
```

## 代表们:

```
interface Repository<T : Record> {
	fun getById(id: Int): T
	fun getAll(): List<T>
}class Controller(repository: Repository<Customer>) : Repository<Customer> by repository {
	fun customerList(): List<Customer> {
		return getAll()
	}

	fun customerById(id: Int): Customer {
		return getById(id)
	}
}var test: String by Delegates.observable("") {
    prop, old, new ->
    *println*("$old -> $new")
}
```

## 密封:

```
// sealed means there is no PageResult child more than ones that are here in this file
sealed class PageResultclass Success(val url: String) : PageResult()
class Error(val code: Int, val message: String) : PageResult()fun getPage(url: String): PageResult {
	val someOperationIsSuccessfull = false

	if (someOperationIsSuccessfull)
		return Success(url)
	else
		return Error(404, "Not found")
}fun callingPage() {
	val result = getPage("http://google.com")
	when (result) {
		is Success -> println(result.url)
		is Error -> println(result.message)
	}
}
```

## 懒惰:

```
class User(firstName: String, lastName: String) {
	val fullName: String by lazy { "$firstName $lastName" } fun printFullName() {
		println("User full name is: $fullName")
	}
}
```

## 本地回报:

```
**fun** myFunction() {
    **val** numbers = 1..100
    numbers.*forEach* **{
        if**(**it**%5==0)
            **return**@forEach
    **}** *println*(**"Hello!!"**) //Hello will be printed!
}
```

## Tailrec:

```
//tailrec convert a recursive function call to a goto loop **tailrec fun** factorial(number: Int, accumulator: Int = 1): Int {
    **when** (number) {
        0 -> **return** accumulator
        **else** -> **return** *factorial*(number - 1, accumulator * number)
    }
}
```

## Java 开发人员的一些不寻常的语法！！：

```
// multiline string literals
print("""1\. test
2\. unit test
3\. what a test""")print("""1\. test
		2\. unit test
		3\. what a test""".trimIndent())print("""|1\. test
		|2\. unit test
		|3\. what a test""".trimMargin())fun max(a: Int, b: Int) = if (a > b) a else b// no for (int i=0;i<10;I++) loop!!!
val myArray = *arrayOf*("A","B","C","D")
for (index in 0 *until* myArray.size){
    *println*(myArray.get(index))
}for ( i in 1..10 )
    *println*(myArray.get(index))
}for ( i in 100 downTo 1 step 5)
    *println*(myArray.get(index))
}var i = 0
while (i < 10)
	println (i++)// no switch case default
when (i) {
	0 -> println("zero")
	1..5 -> println("number")
	is String -> println("$i is string")
	!is String -> println("is not string")
	else -> println("unknown!!!")
}val isString = when ("Hello") {
	is String -> true
	else -> false
}val isInRange = i in 5..9
val isMember = "hello" in array// set alias while importing!
Import com.google.Test as MyTest// loop operators
while (true){
	if(false) break
	if(false) continue
	if(false) return
}// labels
outer@ for (l in list) {
	for (m in map) {
		if (l == m.key) break@outer
	}
}// Pair and Triple data types
val myPair = Pair(1, "Mohse")
val myTriple = Triple(1, "A", "Mohsen")
val (id, name) = myPair// measure runtime in ms!!
val took = measureTimeMillis {
	// ...
}// deprecate a method
@Deprecated ("Use another method!", level = WARNING/ERROR/HIDDEN, replaceWith = ReplaceWith("newPrintFullName()", imports = "com.google.test"))
fun printFullName() {
	// …
}// rename method for Java
@JvmName("methodNameInJava")
fun printFullName() {
	// …
}// inject using qualifier annotation
@field:[Inject ApplicationContext]
lateinit var context: Context// exclude field from serialization and gson
@Transient
var id: String? = null
```

## Kotlin Android 扩展:

```
// add plugin to build.gradle
apply plugin: 'kotlin-android-extensions'import kotlinx.android.synthetic.main.<layout>.*// access views without findViewById!!
myText.text = "Hello world!"
```

我期待你们的评论，Kotlin 的经验，以及任何我作为初学者可能会错过的不寻常的语法。如果你认为这篇文章有用，请点击分享💚按钮，关注我获取更多文章 [Mohsen Mirhoseini](https://medium.com/u/6a4ed0c4dd2c?source=post_page-----b3fcf5e111f7--------------------------------) 。

 [## 穆赫森·米尔霍西尼

### 高级 Android 开发人员

mohsenoid.com](http://mohsenoid.com) [![](img/50ef4044ecd4e250b5d50f368b775d38.png)](http://bit.ly/HackernoonFB)[![](img/979d9a46439d5aebbdcdca574e21dc81.png)](https://goo.gl/k7XYbx)[![](img/2930ba6bd2c12218fdbbf7e02c8746ff.png)](https://goo.gl/4ofytp)

> [黑客中午](http://bit.ly/Hackernoon)是黑客如何开始他们的下午。我们是阿妹家庭的一员。我们现在[接受投稿](http://bit.ly/hackernoonsubmission)并乐意[讨论广告&赞助](mailto:partners@amipublications.com)机会。
> 
> 如果你喜欢这个故事，我们推荐你阅读我们的[最新科技故事](http://bit.ly/hackernoonlatestt)和[趋势科技故事](https://hackernoon.com/trending)。直到下一次，不要把世界的现实想当然！

![](img/be0ca55ba73a573dce11effb2ee80d56.png)