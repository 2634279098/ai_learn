# Kotlin 作用域函数

> 📖 来源：飞书文档 / Kotlin 学习路线

---

## 1. let —— 配合 ?.let 做空安全处理，引用对象用 it

```kotlin
// 特点：用 it 引用对象，返回 lambda 的结果
// 最常见用法：配合 ?. 做空安全

// 用法一：空安全处理
val name: String? = "Kotlin"
val nullName: String? = null

name?.let {
    println("let - 名字不为空: $it, 长度: ${it.length}")
}

nullName?.let {
    println("这行不会执行")
}

// 用法二：变换对象，返回新的结果
val nameLength = name?.let {
    println("let - 正在处理: $it")
    it.length  // lambda 最后一行是返回值
}
println("let - 名字长度: $nameLength")

// 用法三：限定变量作用域，避免污染外部
// let { } 的大括号形成一个 lambda 代码块，里面定义的变量只在大括号内有效
// 好处：临时变量不会泄漏到外部，保持外部作用域干净
val result = "Hello".let {
    val greeting = "$it World"   // greeting 只在 let 块内存在
    greeting.uppercase()  // 返回值
}
println("let - result: $result")  // HELLO WORLD
```

---

## 2. apply —— 配置对象属性，返回对象本身，引用对象用 this

```kotlin
// 特点：用 this 引用对象（可省略），返回对象本身
// 最常见用法：初始化/配置对象

// 用于演示的数据类
data class ScopePerson(
    var name: String = "",
    var age: Int = 0,
    var email: String = ""
)

data class ScopeConfig(
    var host: String = "",
    var port: Int = 0,
    var debug: Boolean = false
)

// 用法一：创建并配置对象（最经典的用法）
val person = ScopePerson().apply {
    this.name = "Alice"
    this.age = 25
    this.email = "alice@example.com"
}
println("apply - $person")

// 用法二：链式配置
// apply 返回对象本身，所以可以连续 .apply / .also 串起来
// 每一步操作完都返回同一个对象，像链条一样一环接一环
val config = ScopeConfig()
    .apply { this.host = "192.168.1.1" }       // 第1步：设置 host，返回 config
    .apply { this.port = 8080 }                 // 第2步：设置 port，返回 config
    .also { println("配置中: $it") }             // 第3步：打印日志，返回 config
    .apply { this.debug = true }                // 第4步：设置 debug，返回 config
println("apply - $config")

// 对比不用 apply 的写法（更啰嗦）：
// val person = ScopePerson()
// person.name = "Alice"
// person.age = 25
// person.email = "alice@example.com"
```

---

## 3. run —— 执行代码块并返回结果，引用对象用 this

```kotlin
// 特点：用 this 引用对象，返回 lambda 的结果
// 适合：需要用对象做一些计算并返回结果

// 用法一：对象上执行操作并返回结果
val greeting = person.run {
    "你好，我是 $name，今年 ${age} 岁"  // this.name, this.age
}
println("run - $greeting")

// 用法二：非扩展的 run（当作代码块用）
val serverUrl = run {
    val protocol = "https"
    val host = "api.example.com"
    val port = 443
    "$protocol://$host:$port"  // 返回拼接结果
}
println("run - serverUrl: $serverUrl")

// run vs let 的区别：
// run 用 this（可以省略），适合调用对象自身的方法/属性
// let 用 it，适合把对象作为参数传递
```

---

## 4. with —— 对同一个对象执行多次操作，引用对象用 this（非扩展函数）

```kotlin
// 特点：不是扩展函数，对象作为参数传入，用 this 引用，返回 lambda 结果
// 适合：对同一个对象连续操作

val personInfo = with(person) {
    // 这里可以直接用 name、age，不需要 person.name
    println("with - 姓名: $name")
    println("with - 年龄: $age")
    println("with - 邮箱: $email")
    "信息打印完毕"  // 返回值
}
println("with - 返回: $personInfo")

// with vs run：功能几乎一样
// with(obj) { ... }  — 对象作为参数
// obj.run { ... }     — 对象作为接收者（支持空安全 obj?.run）
// 推荐：需要空安全时用 run，其他时候看个人习惯
```

---

## 5. also —— 执行附加操作（日志/调试），返回对象本身，引用对象用 it

```kotlin
// 特点：用 it 引用对象，返回对象本身
// 适合：做附加操作（打日志、验证），不影响链式调用

// 用法一：调试/日志
val numbers = mutableListOf(1, 2, 3)
    .also { println("also - 初始列表: $it") }
    .also { it.add(4) }
    .also { println("also - 添加后: $it") }
println("also - 最终: $numbers")

// 用法二：创建对象时顺便做点额外的事
val newPerson = ScopePerson().apply {
    this.name = "Bob"
    this.age = 30
}.also {
    println("also - 创建了用户: ${it.name}")
    // 比如这里可以发送统计事件、写日志等
}

// also vs apply：
// apply 用 this，适合配置对象属性
// also 用 it，适合做"顺便"的事情（日志、验证、通知）
```

---

## 6. 五个作用域函数的对比总结

```
┌──────────┬───────────┬───────────────┬──────────────────────┐
│ 函数      │ 引用对象   │ 返回值         │ 典型场景              │
├──────────┼───────────┼───────────────┼──────────────────────┤
│ let      │ it        │ lambda 结果    │ 空安全、变换对象        │
│ apply    │ this      │ 对象本身       │ 初始化/配置对象         │
│ run      │ this      │ lambda 结果    │ 对象上计算并返回结果     │
│ with     │ this      │ lambda 结果    │ 对同一对象多次操作       │
│ also     │ it        │ 对象本身       │ 附加操作（日志/调试）    │
└──────────┴───────────┴───────────────┴──────────────────────┘

记忆技巧：
- 返回对象本身的：apply、also（名字里都有 a）
- 用 it 的：let、also（不需要访问对象内部属性时）
- 用 this 的：apply、run、with（需要直接访问属性/方法时）
```

---

## 7. 实际 Android 场景中的用法示例

```kotlin
// 场景1：apply 配置 Intent（最常见）
// val intent = Intent(this, DetailActivity::class.java).apply {
//     putExtra("id", 123)
//     putExtra("title", "详情页")
//     addFlags(Intent.FLAG_ACTIVITY_NEW_TASK)
// }
// startActivity(intent)

// 场景2：let 处理可空数据
// val data: UserData? = getUserData()
// data?.let { adapter.submitList(it.items) }

// 场景3：run 获取 SharedPreferences 的值
// val token = sharedPrefs.run {
//     getString("token", null) ?: "default_token"
// }

// 场景4：also 打日志不影响链式调用
// repository.fetchUsers()
//     .also { Log.d("TAG", "获取到 ${it.size} 个用户") }
//     .filter { it.isActive }
//     .also { Log.d("TAG", "活跃用户 ${it.size} 个") }

// 场景5：apply 构建 AlertDialog
// AlertDialog.Builder(context).apply {
//     setTitle("提示")
//     setMessage("确定删除吗？")
//     setPositiveButton("确定") { _, _ -> delete() }
//     setNegativeButton("取消", null)
// }.show()
```
