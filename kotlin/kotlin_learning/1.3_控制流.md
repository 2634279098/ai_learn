# Kotlin 控制流

> 📖 来源：飞书文档 / Kotlin 学习路线

---

## 1. if 表达式 —— 可以有返回值，替代三元运算符

```kotlin
val score = 85

// 基本用法（和 Java 一样）
if (score >= 60) {
    println("及格了")
} else {
    println("没及格")
}

// Kotlin 的 if 是表达式，可以有返回值（替代 Java 的三元运算符）
// Java: String level = score >= 90 ? "优秀" : "一般";
val level = if (score >= 90) "优秀" else "一般"
println("等级: $level")

// 多分支也可以作为表达式
val grade = if (score >= 90) {
    "A"
} else if (score >= 80) {
    "B"
} else if (score >= 60) {
    "C"
} else {
    "D"
}
println("成绩等级: $grade")  // B
```

---

## 2. when 表达式 —— 替代 switch，功能更强大

```kotlin
// 基本用法：替代 switch
val day = 3
when (day) {
    1 -> println("星期一")
    2 -> println("星期二")
    3 -> println("星期三")
    4 -> println("星期四")
    5 -> println("星期五")
    6, 7 -> println("周末")  // 多个值合并
    else -> println("无效")
}

// when 也是表达式，可以有返回值
val dayName = when (day) {
    1 -> "Monday"
    2 -> "Tuesday"
    3 -> "Wednesday"
    else -> "Other"
}
println("dayName = $dayName")

// 用 in 判断范围
val age = 18
val ageGroup = when (age) {
    in 0..12 -> "儿童"
    in 13..17 -> "青少年"
    in 18..64 -> "成年人"
    else -> "老年人"
}
println("$age 岁属于: $ageGroup")

// 用 is 判断类型（Android 中很常见）
val obj: Any = "Hello"
when (obj) {
    is String -> println("是字符串，长度 = ${obj.length}")  // 智能转换
    is Int -> println("是整数")
    is Boolean -> println("是布尔值")
    else -> println("其他类型")
}

// 不带参数的 when（替代 if-else if 链）
val temperature = 35
when {
    temperature >= 40 -> println("极端高温")
    temperature >= 35 -> println("高温预警")
    temperature >= 25 -> println("天气不错")
    else -> println("有点冷")
}
```

---

## 3. for 循环 —— 区间、step、downTo、indices、解构

```kotlin
// 基本区间：1 到 5（包含 5）
print("1..5: ")
for (i in 1..5) {
    print("$i ")
}
println()

// until：不包含右端点（常用于索引）
print("0 until 5: ")
for (i in 0 until 5) {
    print("$i ")  // 0 1 2 3 4
}
println()

// step：指定步长
print("0..10 step 2: ")
for (i in 0..10 step 2) {
    print("$i ")  // 0 2 4 6 8 10
}
println()

// downTo：倒序
print("5 downTo 1: ")
for (i in 5 downTo 1) {
    print("$i ")  // 5 4 3 2 1
}
println()

// 遍历集合
val fruits = listOf("苹果", "香蕉", "橘子")
for (fruit in fruits) {
    println("水果: $fruit")
}

// 带索引遍历（Android 中经常用到）
for ((index, fruit) in fruits.withIndex()) {
    println("  [$index] $fruit")
}

// 遍历 Map
val scores = mapOf("Alice" to 90, "Bob" to 85, "Charlie" to 78)
for ((name, s) in scores) {
    println("  $name: $s 分")
}
```

---

## 4. while / do-while 循环

```kotlin
// while：先判断再执行
var count = 3
while (count > 0) {
    println("while 倒计时: $count")
    count--
}

// do-while：先执行再判断（至少执行一次）
var num = 0
do {
    println("do-while: num = $num")
    num++
} while (num < 3)
```

---

## 5. break 与 continue

```kotlin
// break：跳出循环
println("break 示例:")
for (i in 1..10) {
    if (i == 5) break
    print("$i ")  // 1 2 3 4
}
println()

// continue：跳过本次，继续下一次
println("continue 示例（跳过偶数）:")
for (i in 1..10) {
    if (i % 2 == 0) continue
    print("$i ")  // 1 3 5 7 9
}
println()
```

---

## 6. 标签（label）跳转 —— 控制多层循环的跳出

```kotlin
// 在多层循环中，用标签控制跳出哪一层

println("标签 break 示例:")
outer@ for (i in 1..3) {
    for (j in 1..3) {
        if (i == 2 && j == 2) break@outer  // 直接跳出外层循环
        println("  i=$i, j=$j")
    }
}

// 标签在 forEach 中也很有用
println("标签 return 示例:")
listOf(1, 2, 3, 4, 5).forEach loop@{
    if (it == 3) return@loop  // 相当于 continue，跳过 3
    print("$it ")  // 1 2 4 5
}
println()
```
