# Kotlin 学习路线

> **目标：熟悉 Kotlin 基础语法，能看懂一般的 Kotlin Android 项目代码**

---

## 第一阶段：语言基础（1-2 周）

### 1. 变量与数据类型

- `val`（不可变）与 `var`（可变）的区别
- 基本类型：Int, Long, Float, Double, Boolean, Char, String
- 类型推断：为什么很多时候不需要写类型
- 字符串模板：`"Hello, $name"` 和 `"结果是 ${a + b}"`

📄 [详细内容 → 变量与数据类型](变量与数据类型.md)

### 2. 空安全（Null Safety）

- 可空类型：`String?` vs `String`
- 安全调用：`?.`
- Elvis 操作符：`?:`
- 非空断言：`!!`（尽量少用）
- `let`、`also` 配合空安全使用

📄 [详细内容 → kotlin_空安全](kotlin_空安全.md)

### 3. 控制流

- `if` 作为表达式（可以有返回值）
- `when` 表达式（替代 switch，功能更强）
- `for` 循环与区间：`for (i in 1..10)`、`until`、`step`、`downTo`
- `while` / `do-while`

📄 [详细内容 → kotlin_控制流](kotlin_控制流.md)

### 4. 函数

- 基本函数定义：`fun add(a: Int, b: Int): Int`
- 单表达式函数：`fun double(x: Int) = x * 2`
- 默认参数与命名参数
- 可变参数：`vararg`

📄 [详细内容 → kotlin_函数](kotlin_函数.md)

---

## 第二阶段：面向对象（1-2 周）

### 5. 类与对象

- 主构造函数与 `init` 块
- 属性（property）与幕后字段
- `data class`：自动生成 equals/hashCode/toString/copy
- `sealed class`：限定子类，常用于 UI 状态建模
- `object`：单例模式
- `companion object`：类似 Java 的 static

📄 [详细内容 → 类与对象](类与对象.md)

### 6. 继承与接口

- `open` 关键字（Kotlin 类默认 final）
- 接口可以有默认实现
- `override` 关键字
- 抽象类 vs 接口的选择

📄 [详细内容 → 继承与接口](继承与接口.md)

### 7. 可见性修饰符

- `public`（默认）、`private`、`protected`、`internal`
- 与 Java 的区别

📄 [详细内容 → 可见性修饰符](可见性修饰符.md)

---

## 第三阶段：Kotlin 特色功能（1-2 周）

### 8. 扩展函数与属性

- 给已有类添加方法：`fun String.addExclamation() = this + "!"`
- Android 中大量使用，如 `Context.toast()`

📄 [详细内容 → kotlin_扩展函数与属性](kotlin_扩展函数与属性.md)

### 9. Lambda 与高阶函数

- Lambda 表达式：`{ x: Int -> x * 2 }`
- 高阶函数：函数作为参数或返回值
- 尾随 Lambda：`list.filter { it > 3 }`
- `it` 关键字：单参数 Lambda 的隐式名称
- 常用高阶函数：`map`、`filter`、`forEach`、`flatMap`、`reduce`

📄 [详细内容 → kotlin_Lambda 与高阶函数](kotlin_Lambda_与高阶函数.md)

### 10. 作用域函数

- `let`：常配合 `?.let` 做空安全处理
- `apply`：配置对象属性，返回对象本身
- `run`：执行代码块，返回结果
- `with`：对同一个对象执行多次操作
- `also`：执行附加操作，返回对象本身

📄 [详细内容 → kotlin_作用域函数](kotlin_作用域函数.md)

### 11. 集合操作

- List、Set、Map 的可变与不可变版本
- 集合变换：`map`、`filter`、`groupBy`、`sortedBy`
- 集合创建：`listOf()`、`mutableListOf()`、`mapOf()`

📄 [详细内容 → kotlin_集合操作](kotlin_集合操作.md)

---

## 第四阶段：协程与 Android 实战（2-3 周）

### 12. 协程全面教程 🔥

**目标：全面、深入地掌握 Kotlin 协程，能够熟练在 Android/后端项目中正确使用协程**

从零开始学习协程，包括：协程概念、挂起函数、协程作用域、调度器、并发、取消与超时、异常处理、Flow 异步流、实际应用场景、最佳实践。

📄 [详细内容 → 协程全面教程](5.1_协程全面教程.md)

**学习重点：**
- `suspend` 挂起函数的原理和使用
- `launch` / `async` / `runBlocking` 的区别和使用场景
- 调度器（Dispatchers）的选择：Default / IO / Main
- 结构化并发（Structured Concurrency）的理解
- 异常处理和协程取消
- Flow 异步流的使用

### 13. Jetpack Compose 基础语法

- `@Composable` 注解
- `remember` 与 `mutableStateOf`
- `Column`、`Row`、`Box` 布局
- `Modifier` 链式调用
- `LaunchedEffect`、`SideEffect`

### 14. Android 项目中的常见 Kotlin 用法

- `ViewModel` 中的 `LiveData` / `StateFlow`
- `sealed class` 表示 UI 状态（Loading / Success / Error）
- `data class` 作为数据模型
- 扩展函数简化 Android API 调用
- DSL 风格的代码（如 Gradle Kotlin DSL、Compose）

---

## 学习建议

1. 每学一个知识点，在 `basic/` 目录下写个小文件练习
2. 遇到不理解的 Android 代码，先搞清楚用了哪个 Kotlin 特性
3. 重点掌握：空安全、Lambda、作用域函数、data class、sealed class，这些在 Android 项目中出现频率最高
4. 推荐资源：
   - [Kotlin 官方文档（中文）](https://book.kotlincn.net/)
   - [Kotlin Koans（在线练习）](https://play.kotlinlang.org/koans)
   - [Android 官方 Kotlin 指南](https://developer.android.com/kotlin)

---

> 按这个路线走完，阅读大部分 Kotlin Android 项目代码不会有太大障碍。
