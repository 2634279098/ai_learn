# Kotlin 函数

> 📖 来源：飞书文档 / Kotlin 学习路线

---

## 1. 基本函数定义 —— fun 关键字、参数、返回值

```kotlin
// 1. 基本函数：参数类型必须显式声明，返回类型可以推断但建议写上
fun add(a: Int, b: Int): Int {
    return a + b
}

fun max(a: Int, b: Int): Int {
    return if (a > b) a else b
}

println("3 + 5 = ${add(3, 5)}")
println("max(10, 20) = ${max(10, 20)}")
```

---

## 2. Unit 返回类型 —— 相当于 Java 的 void

```kotlin
// 2. Unit 返回类型（可以省略不写）
fun sayHello(name: String): Unit {
    println("Hello, $name!")
    // 不需要 return
}

// 省略 Unit 的写法（更常见）
fun sayHello(name: String) { println("Hello, $name!") }

sayHello("Kotlin")  // 没有返回值的函数
```

---

## 3. 单表达式函数 —— 用 = 简写函数体

```kotlin
// 3. 单表达式函数：函数体只有一个表达式时，用 = 简写
fun double(x: Int) = x * 2
fun isEven(n: Int) = n % 2 == 0

println("double(7) = ${double(7)}")
println("isEven(4) = ${isEven(4)}")
println("isEven(5) = ${isEven(5)}")
```

---

## 4. 默认参数 —— 参数可以有默认值

```kotlin
// 4. 默认参数
fun greet(greeting: String = "Hello", name: String = "World") {
    println("$greeting, $name!")
}

// 全部使用默认值
greet()                        // Hello, World!

// 只传第一个参数
greet("Hi")                    // Hi, World!

// 传两个参数
greet("你好", "Kotlin")         // 你好, Kotlin!
```

---

## 5. 命名参数 —— 调用时指定参数名

```kotlin
// 5. 命名参数
fun createUser(name: String, age: Int, email: String) {
    println("创建用户: name=$name, age=$age, email=$email")
}

// 不用命名参数，得记住顺序
createUser("Alice", 25, "alice@example.com")

// 用命名参数，代码更清晰（Android 中很常见）
createUser(
    name = "Bob",
    email = "bob@example.com",  // 顺序可以和定义不同
    age = 30
)

// 只对部分参数命名
createUser("Charlie", age = 28, email = "charlie@example.com")
```

---

## 6. 可变参数 vararg

```kotlin
// 6. 可变参数
fun sum(vararg numbers: Int): Int {
    var total = 0
    for (n in numbers) {
        total += n
    }
    return total
}

fun printAll(vararg items: String) {
    println("所有项目: ${items.joinToString(", ")}")
}

println("sum(1,2,3) = ${sum(1, 2, 3)}")
println("sum(10,20,30,40) = ${sum(10, 20, 30, 40)}")

// 传入数组时用 * 展开
val numbers = intArrayOf(1, 2, 3, 4, 5)
println("sum(数组) = ${sum(*numbers)}")

printAll("苹果", "香蕉", "橘子")
```

---

## 7. 局部函数 —— 函数里面定义函数

```kotlin
// 7. 局部函数：在函数内部定义函数，可以访问外部函数的变量
fun validateAndSave(name: String, email: String) {
    // 局部函数：只在这个函数内部使用
    fun validate(value: String, fieldName: String) {
        if (value.isBlank()) {
            throw IllegalArgumentException("$fieldName 不能为空")
        }
    }

    validate(name, "姓名")
    validate(email, "邮箱")
    println("验证通过，保存用户: $name ($email)")
}

validateAndSave("Alice", "alice@example.com")
// validateAndSave("", "test@example.com")  // 会抛异常
```

---

## 8. 泛型函数基础 —— 简单的类型参数

```kotlin
// 8. 泛型函数
fun <T> firstOrDefault(list: List<T>, default: T): T {
    return if (list.isNotEmpty()) list[0] else default
}

val intList = listOf(1, 2, 3)
val strList = listOf("a", "b", "c")
println("Int 列表第一个: ${firstOrDefault(intList, 0)}")
println("String 列表第一个: ${firstOrDefault(strList, "空")}")
println("空列表默认值: ${firstOrDefault(emptyList(), "默认")}")
```

---

## 9. 中缀函数 infix —— 让调用更像自然语言

```kotlin
// 9. 中缀函数：必须是成员函数或扩展函数，只有一个参数
infix fun Int.isDivisibleBy(divisor: Int): Boolean = this % divisor == 0

// 普通调用
println("10.isDivisibleBy(3) = ${10.isDivisibleBy(3)}")

// 中缀调用（更像自然语言）
println("10 isDivisibleBy 5 = ${10 isDivisibleBy 5}")

// Kotlin 内置的中缀函数：to（创建 Pair）
val pair = "name" to "Kotlin"
println("Pair: $pair")
```

---

## 10. 尾递归 tailrec —— 编译器优化递归

```kotlin
// 10. 尾递归：tailrec 让编译器把递归优化成循环，避免栈溢出
tailrec fun factorial(n: Int, accumulator: Long = 1): Long {
    return if (n <= 1) accumulator else factorial(n - 1, n * accumulator)
}

// 普通递归写法（对比用，没有 tailrec 优化）
fun fibonacci(n: Int): Int {
    return if (n <= 1) n else fibonacci(n - 1) + fibonacci(n - 2)
}

println("factorial(5) = ${factorial(5, 1)}")  // 120
println("fibonacci(10) = ${fibonacci(10)}")    // 55
```
