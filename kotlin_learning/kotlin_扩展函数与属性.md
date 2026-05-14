# Kotlin 扩展函数与属性

> 📖 来源：飞书文档 / Kotlin 学习路线

---

## 1. 扩展函数基础 —— 给已有类添加新方法

```kotlin
// 语法：fun 类名.函数名(参数): 返回值 { }
// 不需要修改原始类的代码，就能给它加方法

// 1. 基础扩展函数
fun String.capitalizeFirst(): String {
    if (isEmpty()) return this
    return this[0].uppercase() + substring(1)
}

fun String.removeSpaces(): String = replace(" ", "")

fun Int.isOdd(): Boolean = this % 2 != 0

println("hello kotlin".capitalizeFirst())  // Hello kotlin
println("  spaces  ".removeSpaces())       // spaces
println(3.isOdd())   // true
println(4.isOdd())   // false
```

---

## 2. 扩展函数中的 this —— 指向接收者对象

```kotlin
// this 指向调用该扩展函数的对象（接收者）

// 2. this 的使用
fun String.repeatWithSeparator(times: Int, separator: String): String {
    // this 就是调用这个方法的字符串
    return List(times) { this }.joinToString(separator)
}

println("kotlin".repeatWithSeparator(3, "-"))  // kotlin-kotlin-kotlin
```

---

## 3. 扩展属性 —— 给已有类添加新属性

```kotlin
// 语法：val 类名.属性名: 类型 get() = ...
// 注意：扩展属性没有幕后字段（backing field），不能用 field，只能定义 getter

// 3. 扩展属性
val <T> List<T>.lastElement: T
    get() = this[size - 1]

val <T> List<T>.isSingle: Boolean
    get() = size == 1

val String.firstChar: Char
    get() = this[0]

val list = listOf(1, 2, 3, 4, 5)
println("列表最后一个元素: ${list.lastElement}")  // 5
println("列表是否只有一个元素: ${list.isSingle}")  // false
println(listOf("唯一").isSingle)                  // true

println("hello".firstChar)  // h
```

---

## 4. 可空类型的扩展 —— 对 null 也能安全调用

```kotlin
// 可以给 String? 这样的可空类型定义扩展，函数内部 this 可能为 null

// 4. 可空类型扩展
fun String?.orDefault(default: String): String {
    return this ?: default
}

fun String?.safeLength(): Int {
    return this?.length ?: 0
}

val name: String? = null
val realName: String? = "Kotlin"

println(name.orDefault("匿名"))      // 匿名
println(realName.orDefault("匿名"))  // Kotlin

println(name.safeLength())      // 0
println(realName.safeLength())  // 6
```

---

## 5. 扩展函数的实质 —— 静态方法，不是真正修改类

```kotlin
// 扩展函数本质上是静态方法，编译后大致等价于：
// public static String capitalizeFirst(String str) { ... }
//
// 重要结论：
// - 扩展函数不能访问类的 private / protected 成员
// - 扩展函数是静态解析的，不支持多态

open class Animal
class Dog : Animal()

fun Animal.name() = "动物"
fun Dog.name() = "狗"

val animal: Animal = Dog()
println("扩展函数静态解析: ${animal.name()}")  // 输出"动物"，不是"狗"
// 因为变量声明类型是 Animal，扩展函数看声明类型，不看实际类型
```

---

## 6. 泛型扩展函数 —— 配合泛型更灵活

```kotlin
// 6. 泛型扩展
fun <A, B> Pair<A, B>.swap(): Pair<B, A> = Pair(second, first)

fun <T> T.printAndReturn(): T {
    println("[DEBUG] $this")
    return this
}

// 交换 Pair 的两个元素
val pair = "key" to 42
println("交换前: $pair")
println("交换后: ${pair.swap()}")  // (42, key)

// 打印任意对象并返回自身（调试利器）
val result = "hello".printAndReturn()  // 输出: [DEBUG] hello
println("返回值: $result")
```

---

## 7. 常见标准库扩展函数 —— 你一直在用的其实都是扩展

```kotlin
// 你一直在用的很多方法其实都是扩展函数

// String 的扩展
println("123".toInt())                    // 字符串转数字
println("hello world".substringAfter(" ")) // 返回指定分隔符之后的部分 -> world
println("  trim me  ".trim())             // trim me
println("abc".reversed())                 // cba
println("hello".uppercase())              // HELLO

// Collection 的扩展
val numbers = listOf(3, 1, 4, 1, 5, 9, 2, 6)
println("排序: ${numbers.sorted()}")
println("去重: ${numbers.distinct()}")
println("前3个: ${numbers.take(3)}")
println("求和: ${numbers.sum()}")
println("最大值: ${numbers.max()}")

// Any 的扩展
val obj = "test"
println(obj.toString())
println(obj.hashCode())
```

---

## 8. 实战：模拟 Android 中常见的扩展用法

```kotlin
// Android 中经常这样用扩展函数来简化代码：

// dp 转 px 扩展属性（Android 中的经典写法）
val Int.dp: Int
    get() = (this * 2.75).toInt()  // 模拟屏幕密度

// 模拟 View 类
class FakeView(val name: String) {
    var visible = true
}

fun FakeView.show() {
    visible = true
    println("$name: 显示")
}

fun FakeView.hide() {
    visible = false
    println("$name: 隐藏")
}

fun FakeView.toggleVisibility() {
    if (visible) hide() else show()
}

// 字符串校验扩展
fun String.isValidEmail(): Boolean {
    return contains("@") && contains(".")
}

fun String.isPhoneNumber(): Boolean {
    return length == 11 && all { it.isDigit() }
}

// 模拟 dp 转 px（Android 中非常常见）
println("16dp = ${16.dp}px")   // 假设密度为 2.75
println("8dp = ${8.dp}px")

// 模拟 View 的显示/隐藏扩展
val fakeView = FakeView("按钮")
fakeView.show()
fakeView.hide()
fakeView.toggleVisibility()

// 模拟 String 校验扩展
println("test@email.com".isValidEmail())  // true
println("not-email".isValidEmail())       // false
println("13800138000".isPhoneNumber())    // true
println("1234".isPhoneNumber())           // false
```
