# `List` 接口——你需要掌握的核心内容

---

## 一、List 是什么

```
List = 有序的、可重复的元素集合
```

| 特性 | 说明 | 类比 |
|------|------|------|
| **有序** | 放进去什么顺序，取出来就是什么顺序 | 排队——先来的人排前面 |
| **可重复** | 同一个元素可以放多次 | 队列里可以有两个穿同样衣服的人 |
| **有索引** | 每个元素有编号（从 0 开始） | 排队每个人有号码牌：0号、1号、2号... |

> 对比 Map：Map 是"通讯录"（通过名字找电话），List 是"排队"（通过编号找人）。

---

## 二、必须掌握（日常开发天天用）

### 1. 常用实现类

| 实现类 | 特点 | 使用频率 |
|--------|------|---------|
| `ArrayList` | 最常用，查询快，增删慢 | ⭐⭐⭐ |
| `LinkedList` | 增删快，查询慢 | ⭐ |

> 90% 的场景用 `ArrayList` 就够了。

### 2. 核心方法（记住这 10 个）

| 方法                    | 作用         | 示例                             |
| --------------------- | ---------- | ------------------------------ |
| `add(E e)`            | 末尾添加一个元素   | `list.add("张三")`               |
| `add(int index, E e)` | 指定位置插入     | `list.add(0, "张三")`（插到最前面）     |
| `get(int index)`      | 根据索引取值     | `list.get(0)` → `"张三"`         |
| `set(int index, E e)` | 修改指定位置的值   | `list.set(0, "李四")`            |
| `remove(int index)`   | 删除指定位置的元素  | `list.remove(0)`               |
| `remove(Object o)`    | 删除第一个匹配的元素 | `list.remove("张三")`            |
| `size()`              | 有多少个元素     | `list.size()` → `3`            |
| `isEmpty()`           | 是否为空       | `list.isEmpty()` → `false`     |
| `contains(Object o)`  | 是否包含某个元素   | `list.contains("张三")` → `true` |
| `clear()`             | 清空所有元素     | `list.clear()`                 |

### 3. 多态写法（标准写法）

```java
// ✅ 推荐：左边用接口，右边用实现类
List<String> list = new ArrayList<>();
```

---

## 三、应该掌握（经常遇到）

### 4. 遍历 List 的三种方式

```java
List<String> list = new ArrayList<>();
list.add("张三");
list.add("李四");
list.add("王五");

// 方式1：for-each（最常用，推荐）
for (String item : list) {
    System.out.println(item);
}

// 方式2：普通 for 循环（需要索引时用）
for (int i = 0; i < list.size(); i++) {
    System.out.println(i + " → " + list.get(i));
}

// 方式3：forEach + Lambda（最简洁，Java 8+）
list.forEach(item -> System.out.println(item));
```

### 5. 转数组

```java
List<String> list = new ArrayList<>();
list.add("张三");
list.add("李四");

// 转成数组
String[] arr = list.toArray(new String[0]);
// arr = ["张三", "李四"]
```

### 6. 排序

```java
List<Integer> numbers = new ArrayList<>();
numbers.add(30);
numbers.add(10);
numbers.add(20);

// 升序（从小到大）
Collections.sort(numbers);
// [10, 20, 30]

// 降序（从大到小）
numbers.sort(Comparator.reverseOrder());
// [30, 20, 10]
```

Collections 是 JDK 提供的一个工具类（Utility Class），里面全是 static 静态方法，专门用来操作集合（List、Set、Map 等）。
### 7. 子列表

```java
List<String> list = new ArrayList<>();
list.add("张三");
list.add("李四");
list.add("王五");
list.add("赵六");

List<String> sub = list.subList(1, 3);  // 索引 1 到 3（不含 3）
// sub = ["李四", "王五"]
```

---

## 四、了解即可（遇到时能看懂）

### 8. `ListIterator` —— 双向迭代器

```java
ListIterator<String> it = list.listIterator();
it.next();      // 往后移
it.previous();  // 往前移（List 特有，Set 没有）
```

### 9. `indexOf()` / `lastIndexOf()`

```java
list.indexOf("张三")       // 第一次出现的位置
list.lastIndexOf("张三")   // 最后一次出现的位置
```

---

## 五、List vs Set vs Map 对比

| 对比项 | List | Set | Map |
|--------|------|-----|-----|
| 有序？ | ✅ 有序 |  无序（HashSet） | ❌ 无序（HashMap） |
| 可重复？ | ✅ 可重复 | ❌ 不可重复 | key 不可重复，value 可重复 |
| 通过什么访问？ | 索引（0, 1, 2...） | 迭代器 | key |
| 典型实现 | `ArrayList` | `HashSet` | `HashMap` |
| 类比 | 排队 | 袋子（扔进去就乱了） | 通讯录 |

---

## 六、一张速查表

```
List 核心知识
│
── 是什么？    → 有序的、可重复的元素集合（排队）
├── 用什么？    → ArrayList（最常用）
├── 怎么存？    → add(element)
├── 怎么取？    → get(index)
├── 怎么改？    → set(index, element)
├── 怎么删？    → remove(index) 或 remove(object)
├── 怎么遍历？  → for-each / forEach()
├── 怎么写？    → List<E> list = new ArrayList<>()（多态）
└── 注意什么？  → 索引从 0 开始，越界会报 IndexOutOfBoundsException
```

> 一句话：**掌握 `add`、`get`、`remove`、`for-each` 遍历、多态写法**这 5 个点，就能应对若依项目中 95% 的 List 使用场景。