# Kotlin 空安全（Null Safety）

> 📖 来源：飞书文档 / Kotlin 学习路线

---

## 1. 可空类型与非空类型 —— String vs String?

```kotlin
// 普通类型不能赋值为 null
val name: String = "Kotlin"
// val name2: String = null  // 编译错误！

// 类型后面加 ? 表示可以为 null
val nickname: String? = null
val email: String? = "test@example.com"

println("name = $name")
println("nickname = $nickname")  // 输出: null
println("email = $email")
```

---

## 2. 安全调用操作符 —— ?.

```kotlin
// 如果对象为 null，不会报错，直接返回 null

val text: String? = "Hello Kotlin"
val nullText: String? = null

println("text 的长度: ${text?.length}")       // 输出: 12
println("nullText 的长度: ${nullText?.length}") // 输出: null（不会崩溃）

// 对比 Java：如果不判空直接调用 nullText.length() 就会 NPE
```

---

## 3. 安全调用链 —— 连续 ?. 调用

```kotlin
// 多层调用时，任何一环为 null 都会短路返回 null

data class Address(val city: String?)
data class User(val name: String, val address: Address?)

val user1 = User("Alice", Address("Beijing"))
val user2 = User("Bob", null)
val user3 = User("Charlie", Address(null))

// 安全地获取城市名的长度
println("user1 城市名长度: ${user1.address?.city?.length}")  // 7
println("user2 城市名长度: ${user2.address?.city?.length}")  // null
println("user3 城市名长度: ${user3.address?.city?.length}")  // null
```

---

## 4. Elvis 操作符 —— ?:（提供默认值）

```kotlin
// 当左边为 null 时，使用右边的默认值

val input: String? = null
val result = input ?: "默认值"
println("result = $result")  // 输出: 默认值

// 实际场景：获取用户昵称，没有就用真名
val userNickname: String? = null
val userName = "张三"
val displayName = userNickname ?: userName
println("显示名称: $displayName")  // 输出: 张三

// 配合 ?. 一起用
val city = user2.address?.city ?: "未知城市"
println("user2 的城市: $city")  // 输出: 未知城市

// Elvis 还可以配合 return / throw
// val len = nullText?.length ?: throw IllegalArgumentException("不能为空")
```

---

## 5. 非空断言 —— !!（慎用）

```kotlin
// 强制告诉编译器"我确定这不是 null"
// 如果实际是 null，会抛出 NullPointerException

val value: String? = "我确定有值"
val length = value!!.length  // OK
println("非空断言获取长度: $length")

// 危险用法（千万别这样）：
// val crash: String? = null
// val len = crash!!.length  // 运行时 NPE！

// 建议：尽量用 ?. 和 ?: 代替 !!
```

---

## 6. let 配合空安全 —— ?.let { }

```kotlin
// ?.let { } 只在非空时执行代码块，it 就是非空的值

val phone: String? = "13800138000"
val noPhone: String? = null

phone?.let {
    // 这里 it 的类型是 String（非空）
    println("手机号: $it, 长度: ${it.length}")
}

noPhone?.let {
    println("这行不会执行")
}

// 常见 Android 用法：
// activity?.let { startActivity(intent) }
// data?.let { adapter.submitList(it) }
```

---

## 7. also 配合空安全 —— ?.also { }

```kotlin
// ?.also { } 也是非空时执行，但返回对象本身（适合做日志、调试）

val config: String? = "debug_mode"
config?.also {
    println("配置项: $it")
}?.also {
    println("配置项长度: ${it.length}")
}

// let vs also:
// let  -> 返回 lambda 的结果
// also -> 返回对象本身（方便链式调用）
```

---

## 8. 可空类型的智能转换（Smart Cast）

```kotlin
// 在 if 判空之后，编译器自动将类型转为非空

val message: String? = "Hello"

if (message != null) {
    // 这里 message 自动变成 String 类型（非空），可以直接调用
    println("消息长度: ${message.length}")  // 不需要 ?. 了
}

// when 中也可以智能转换
val data: Any? = "这是一个字符串"
when (data) {
    is String -> println("字符串长度: ${data.length}")  // 自动转为 String
    is Int -> println("整数值: ${data + 1}")            // 自动转为 Int
    null -> println("是 null")
    else -> println("其他类型")
}
```

---

## 9. 安全类型转换 —— as?

```kotlin
// 普通转换 as 失败会抛异常，as? 失败返回 null

val obj: Any = "Hello"
val str: String? = obj as? String    // 成功，str = "Hello"
val num: Int? = obj as? Int          // 失败，num = null（不会崩溃）

println("安全转换为 String: $str")
println("安全转换为 Int: $num")
```
