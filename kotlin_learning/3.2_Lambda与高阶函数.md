# Kotlin Lambda 与高阶函数

> 📖 来源：飞书文档 / Kotlin 学习路线

---

## 1. Lambda 表达式基础 —— 语法与简写规则

```kotlin
// Lambda 语法：{ 参数列表 -> 函数体 }
// 最后一行表达式就是返回值，不需要 return

// 完整写法
val sum: (Int, Int) -> Int = { a: Int, b: Int -> a + b }
println("sum(3, 5) = ${sum(3, 5)}")

// 类型推断简写：声明了变量类型，Lambda 参数类型可省略
val multiply: (Int, Int) -> Int = { a, b -> a * b }
println("multiply(3, 5) = ${multiply(3, 5)}")

// 也可以反过来：Lambda 写类型，变量不写
val subtract = { a: Int, b: Int -> a - b }
println("subtract(10, 3) = ${subtract(10, 3)}")

// 无参数的 Lambda
val greet = { println("Hello Lambda!") }
greet()
```

---

## 2. it 关键字 —— 单参数 Lambda 的隐式名称

```kotlin
// 当 Lambda 只有一个参数时，可以省略参数声明，用 it 代替

val double: (Int) -> Int = { it * 2 }
println("double(7) = ${double(7)}")

val names = listOf("Alice", "Bob", "Charlie")
// 完整写法
names.forEach { name -> println("完整: $name") }
// 用 it 简写
names.forEach { println("简写: $it") }
```

---

## 3. 尾随 Lambda —— 最后一个参数是 Lambda 时移到括号外

```kotlin
// 当函数最后一个参数是 Lambda 时，可以把它移到括号外面

val numbers = listOf(1, 2, 3, 4, 5)

// 普通写法
val evens1 = numbers.filter({ it % 2 == 0 })

// 尾随 Lambda 写法（推荐）
val evens2 = numbers.filter { it % 2 == 0 }

println("偶数: $evens2")

// 如果 Lambda 是唯一参数，括号可以完全省略
numbers.forEach { print("$it ") }
println()
```

---

## 4. 高阶函数 —— 函数作为参数

```kotlin
// 4. 高阶函数：接受函数作为参数
fun calculate(a: Int, b: Int, operation: (Int, Int) -> Int): Int {
    return operation(a, b)
}

fun myFilter(list: List<Int>, predicate: (Int) -> Boolean): List<Int> {
    val result = mutableListOf<Int>()
    for (item in list) {
        if (predicate(item)) result.add(item)
    }
    return result
}

println("加法: ${calculate(10, 5) { a, b -> a + b }}")
println("减法: ${calculate(10, 5) { a, b -> a - b }}")
println("乘法: ${calculate(10, 5) { a, b -> a * b }}")

// 实际用途：自定义列表过滤
val scores = listOf(85, 92, 56, 78, 95, 43, 88)
val passed = myFilter(scores) { it >= 60 }
val excellent = myFilter(scores) { it >= 90 }
println("及格: $passed")
println("优秀: $excellent")
```

---

## 5. 函数作为返回值

```kotlin
// 5. 函数作为返回值
fun makeAdder(n: Int): (Int) -> Int {
    return { x -> x + n }
}

fun getValidator(type: String): (String) -> Boolean {
    return when (type) {
        "email" -> { input -> input.contains("@") && input.contains(".") }
        "phone" -> { input -> input.length == 11 && input.all { it.isDigit() } }
        else -> { _ -> true }
    }
}

val addFive = makeAdder(5)
val addTen = makeAdder(10)
println("addFive(3) = ${addFive(3)}")   // 8
println("addTen(3) = ${addTen(3)}")     // 13

val validator = getValidator("email")
println("验证邮箱: ${validator("test@example.com")}")  // true
println("验证邮箱: ${validator("invalid")}")           // false
```

---

## 6. 函数引用 —— :: 语法

```kotlin
// 用 :: 把已有函数转成 Lambda

// 引用顶层函数
val nums = listOf(1, -2, 3, -4, 5)
val positives = nums.filter(::isPositive)  // 等价于 { isPositive(it) }
println("正数: $positives")

// 引用类的方法
val words = listOf("hello", "world", "kotlin")
val uppercased = words.map(String::uppercase)  // 等价于 { it.uppercase() }
println("大写: $uppercased")

// 引用构造函数
data class Person(val name: String)
val nameList = listOf("Alice", "Bob")
val people = nameList.map(::Person)  // 等价于 { Person(it) }
println("人员: $people")

// 6. 函数引用用到的辅助函数
fun isPositive(n: Int): Boolean = n > 0
```

---

## 7. 常用集合高阶函数 —— map/filter/forEach/flatMap/reduce 等

```kotlin
val items = listOf(1, 2, 3, 4, 5, 6, 7, 8, 9, 10)

// map：转换每个元素
val doubled = items.map { it * 2 }
println("map 翻倍: $doubled")  // [2, 4, 6, 8, 10, 12, 14, 16, 18, 20]

// filter：过滤
val bigOnes = items.filter { it > 5 }
println("filter >5: $bigOnes")  // [6, 7, 8, 9, 10]

// map + filter 链式调用
val result = items
    .filter { it % 2 == 0 }   // 先取偶数 [2,4,6,8,10]
    .map { it * it }           // 再平方 [4,16,36,64,100]
println("偶数的平方: $result")

// forEach：遍历（无返回值）
print("forEach: ")
items.take(5).forEach { print("$it ") }
println()

// find / firstOrNull：找第一个满足条件的
val firstEven = items.find { it % 2 == 0 }  // 等价于 firstOrNull
println("第一个偶数: $firstEven")

// any / all / none：判断
println("有大于5的? ${items.any { it > 5 }}")    // true
println("全部大于0? ${items.all { it > 0 }}")    // true
println("没有负数? ${items.none { it < 0 }}")    // true

// groupBy：分组，返回类型是 Map<K, List<T>>
val grouped = items.groupBy { if (it % 2 == 0) "偶数" else "奇数" }
println("分组: $grouped")  // {奇数=[1, 3, 5, 7, 9], 偶数=[2, 4, 6, 8, 10]}

// flatMap：展平
val nested = listOf(listOf(1, 2), listOf(3, 4), listOf(5))
val flat = nested.flatMap { it }
println("flatMap: $flat")  // [1, 2, 3, 4, 5]

// reduce：累积计算
val sumAll = items.reduce { acc, i -> acc + i }
println("reduce 求和: $sumAll")  // 55

// fold：带初始值的累积
val sumPlus100 = items.fold(100) { acc, i -> acc + i }
println("fold 求和+100: $sumPlus100")  // 155

// sortedBy：排序
data class Student(val name: String, val score: Int)
val students = listOf(
    Student("Alice", 85),
    Student("Bob", 92),
    Student("Charlie", 78)
)
val sorted = students.sortedBy { it.score }
println("按分数排序: ${sorted.map { "${it.name}(${it.score})" }}")
```

---

## 8. Lambda 与闭包 —— 捕获外部变量

```kotlin
// Lambda 可以捕获并修改外部变量（Java 的匿名类只能捕获 final 变量）

var counter = 0
val increment = { counter++ }

increment()
increment()
increment()
println("闭包计数器: $counter")  // 3

// 实际场景：统计满足条件的个数
var evenCount = 0
items.forEach { if (it % 2 == 0) evenCount++ }
println("偶数个数: $evenCount")  // 5
```

---

## 9. 内联函数 inline —— 消除 Lambda 性能开销

```kotlin
// 普通高阶函数每次调用 Lambda 都会创建一个对象
// inline 让编译器把 Lambda 代码直接嵌入调用处，避免对象创建

inline fun measureTime(block: () -> Unit) {
    val start = System.currentTimeMillis()
    block()
    val end = System.currentTimeMillis()
    println("耗时: ${end - start}ms")
}

measureTime {
    var sum = 0
    for (i in 1..1000000) sum += i
    println("计算结果: $sum")
}
```

---

## 10. 实战：模拟 Android 中常见的 Lambda 用法

```kotlin
// 模拟点击监听器（Android 中最常见的 Lambda 场景）
class FakeButton(val name: String) {
    private var listener: ((FakeButton) -> Unit)? = null

    fun setOnClickListener(l: (FakeButton) -> Unit) {
        listener = l
    }

    fun click() {
        listener?.invoke(this)
    }
}

fun fetchData(onSuccess: (String) -> Unit, onError: (String) -> Unit) {
    // 模拟请求成功
    onSuccess("{ \"name\": \"Kotlin\" }")
}

class FakeAdapter(private val items: List<String>) {
    var onItemClick: ((Int, String) -> Unit)? = null

    fun simulateClick(position: Int) {
        onItemClick?.invoke(position, items[position])
    }
}

// 模拟点击监听器
val button = FakeButton("登录按钮")
button.setOnClickListener {
    println("按钮被点击了: ${it.name}")
}
button.click()  // 触发点击

// 模拟网络请求回调
fetchData(
    onSuccess = { data -> println("请求成功: $data") },
    onError = { error -> println("请求失败: $error") }
)

// 模拟 RecyclerView Adapter 的 item 点击
val adapter = FakeAdapter(listOf("项目A", "项目B", "项目C"))
adapter.onItemClick = { position, item ->
    println("点击了第 $position 项: $item")
}
adapter.simulateClick(1)
```
