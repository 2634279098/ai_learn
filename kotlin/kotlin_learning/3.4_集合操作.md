# Kotlin 集合操作

> 📖 来源：飞书文档 / Kotlin 学习路线

---

## 1. List —— 不可变列表与可变列表

```kotlin
// 不可变列表：创建后不能增删改
val fruits = listOf("苹果", "香蕉", "橘子", "苹果")  // 允许重复
println("List: $fruits")
println("第一个: ${fruits[0]}, 大小: ${fruits.size}")
// fruits.add("西瓜")  // 编译错误！不可变列表没有 add 方法

// 可变列表：可以增删改
val mutableFruits = mutableListOf("苹果", "香蕉")
mutableFruits.add("橘子")
mutableFruits.remove("香蕉")
mutableFruits[0] = "红苹果"
println("MutableList: $mutableFruits")

// 空列表
val emptyList = emptyList<String>()
println("空列表: $emptyList, isEmpty: ${emptyList.isEmpty()}")
```

---

## 2. Set —— 不可变集合与可变集合（元素不重复）

```kotlin
// 不可变 Set：元素不重复，自动去重
val numbers = setOf(1, 2, 3, 2, 1)
println("Set: $numbers")  // [1, 2, 3]，重复的被去掉了

// 可变 Set
val mutableSet = mutableSetOf("A", "B")
mutableSet.add("C")
mutableSet.add("A")  // 已存在，不会重复添加
println("MutableSet: $mutableSet")  // [A, B, C]

// 常见用法：去重
val listWithDuplicates = listOf(1, 2, 2, 3, 3, 3)
val unique = listWithDuplicates.toSet().toList()
// 更简洁的写法：listWithDuplicates.distinct()
println("去重: $unique")
```

---

## 3. Map —— 不可变映射与可变映射（键值对）

```kotlin
// 不可变 Map：键值对
val scores = mapOf("Alice" to 90, "Bob" to 85, "Charlie" to 78)
println("Map: $scores")
println("Alice 的分数: ${scores["Alice"]}")       // 90
println("不存在的键: ${scores["David"]}")          // null
println("带默认值: ${scores.getOrDefault("David", 0)}")  // 0

// 可变 Map
val mutableScores = mutableMapOf("Alice" to 90)
mutableScores["Bob"] = 85          // 添加
mutableScores["Alice"] = 95        // 修改
mutableScores.remove("Bob")        // 删除
println("MutableMap: $mutableScores")

// 遍历 Map
for ((name, score) in scores) {
    println("  $name: $score")
}
```

---

## 4. 集合变换 —— map、filter、flatMap

```kotlin
val nums = listOf(1, 2, 3, 4, 5)

// map：对每个元素做变换，返回新列表
val doubled = nums.map { it * 2 }
println("map 翻倍: $doubled")  // [2, 4, 6, 8, 10]

// filter：过滤，只保留满足条件的元素
val evens = nums.filter { it % 2 == 0 }
println("filter 偶数: $evens")  // [2, 4]

// map + filter 链式调用（非常常见）
val result = nums
    .filter { it > 2 }       // 先过滤：[3, 4, 5]
    .map { it * 10 }         // 再变换：[30, 40, 50]
println("filter + map: $result")

// flatMap：每个元素变成一个列表，然后展平成一个列表
val nested = listOf(listOf(1, 2), listOf(3, 4), listOf(5))
val flat = nested.flatMap { it }
println("flatMap 展平: $flat")  // [1, 2, 3, 4, 5]

// flatMap 实际用法：每个人有多个标签
val tags = listOf("Android,Kotlin", "Java,Spring", "Kotlin,Compose")
val allTags = tags.flatMap { it.split(",") }
println("所有标签: $allTags")  // [Android, Kotlin, Java, Spring, Kotlin, Compose]

// mapNotNull：变换的同时过滤掉 null
val strings = listOf("1", "2", "abc", "4")
val parsed = strings.mapNotNull { it.toIntOrNull() }
println("mapNotNull: $parsed")  // [1, 2, 4]，"abc" 转换失败被过滤
```

---

## 5. 集合聚合 —— reduce、fold、sum、count

```kotlin
val values = listOf(1, 2, 3, 4, 5)

// sum：求和
println("sum: ${values.sum()}")  // 15

// count：计数（可带条件）
println("count: ${values.count()}")                    // 5
println("count 偶数: ${values.count { it % 2 == 0 }}")  // 2

// reduce：从第一个元素开始，依次和下一个元素做运算
val sum = values.reduce { acc, num -> acc + num }
// 过程：1 -> 1+2=3 -> 3+3=6 -> 6+4=10 -> 10+5=15
println("reduce 求和: $sum")

// fold：和 reduce 类似，但可以指定初始值
val sumFrom100 = values.fold(100) { acc, num -> acc + num }
// 过程：100 -> 100+1=101 -> 101+2=103 -> ... -> 115
println("fold 从100开始求和: $sumFrom100")

// 其他聚合
println("max: ${values.max()}")
println("min: ${values.min()}")
println("average: ${values.average()}")
```

---

## 6. 集合排序 —— sortedBy、sortedDescending

```kotlin
val unsorted = listOf(3, 1, 4, 1, 5, 9, 2, 6)

println("sorted 升序: ${unsorted.sorted()}")
println("sortedDescending 降序: ${unsorted.sortedDescending()}")

// sortedBy：按某个属性排序
val people = listOf(
    Employee("Alice", "开发", 15000),
    Employee("Bob", "测试", 12000),
    Employee("Charlie", "开发", 18000),
    Employee("David", "测试", 13000)
)

val byName = people.sortedBy { it.name }
println("按名字排序: ${byName.map { it.name }}")

val bySalaryDesc = people.sortedByDescending { it.salary }
println("按薪资降序: ${bySalaryDesc.map { "${it.name}(${it.salary})" }}")

// 用于排序的数据类
data class Employee(val name: String, val department: String, val salary: Int)
```

---

## 7. 集合分组 —— groupBy、partition

```kotlin
// groupBy：按条件分组，返回 Map<Key, List<Value>>
val byDept = people.groupBy { it.department }
println("按部门分组:")
byDept.forEach { (dept, members) ->
    println("  $dept: ${members.map { it.name }}")
}

// partition：分成两组（满足条件的 和 不满足条件的）
val (highSalary, lowSalary) = people.partition { it.salary >= 15000 }
println("高薪: ${highSalary.map { it.name }}")   // [Alice, Charlie]
println("低薪: ${lowSalary.map { it.name }}")    // [Bob, David]
```

---

## 8. 集合查找 —— find、first、last、any、all、none

```kotlin
// find / firstOrNull：找到第一个满足条件的元素
val firstDev = people.find { it.department == "开发" }
println("第一个开发: ${firstDev?.name}")  // Alice

// first / last：第一个/最后一个（找不到会抛异常）
val first = people.first()
val last = people.last { it.department == "测试" }
println("第一个: ${first.name}, 最后一个测试: ${last.name}")

// any：是否有任意一个满足条件
val hasHighSalary = people.any { it.salary > 15000 }
println("有人薪资>15000: $hasHighSalary")  // true

// all：是否全部满足条件
val allAbove10k = people.all { it.salary > 10000 }
println("所有人薪资>10000: $allAbove10k")  // true

// none：是否没有任何一个满足条件
val noneBelow5k = people.none { it.salary < 5000 }
println("没有人薪资<5000: $noneBelow5k")  // true
```

---

## 9. 集合转换 —— toList、toSet、toMap、associate

```kotlin
// List -> Set（去重）
val listToSet = listOf(1, 2, 2, 3).toSet()
println("toSet: $listToSet")

// Set -> List
val setToList = setOf(3, 1, 2).toList()
println("toList: $setToList")

// List -> Map（用 associate）
val nameToSalary = people.associate { it.name to it.salary }
println("associate: $nameToSalary")
// {Alice=15000, Bob=12000, Charlie=18000, David=13000}

// associateBy：指定 key，value 是元素本身
val nameMap = people.associateBy { it.name }
println("associateBy: ${nameMap["Alice"]}")

// joinToString：拼成字符串
val nameStr = people.joinToString(separator = " | ") { it.name }
println("joinToString: $nameStr")  // Alice | Bob | Charlie | David
```

---

## 10. 实际 Android 场景中的集合用法

```kotlin
// 场景1：API 返回的列表做过滤和变换
val activeUsers = response.users
    .filter { it.isActive }
    .map { UserVO(it.name, it.avatar) }
adapter.submitList(activeUsers)

// 场景2：RecyclerView 数据分组显示
val grouped = messages.groupBy { it.date }
// grouped: { "2024-01-01" -> [msg1, msg2], "2024-01-02" -> [msg3] }

// 场景3：去重 + 排序
val uniqueTags = allItems
    .flatMap { it.tags }
    .distinct()
    .sorted()

// 场景4：检查权限列表
val hasCamera = permissions.any { it == Manifest.permission.CAMERA }

// 场景5：把列表转成 Map 方便查找
val userMap = userList.associateBy { it.id }
val target = userMap[targetId]  // O(1) 查找，比 find 快
```
