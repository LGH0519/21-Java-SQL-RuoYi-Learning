# `Map` 接口——你需要掌握的核心内容

根据你在若依项目中的实际开发需求，按**重要程度**排列：

---

## 必须掌握（日常开发天天用）

### 1. Map 是什么

```
Map = 键值对的集合（key → value）
```

> 类比：通讯录——左边是名字（key），右边是电话（value）。通过名字找电话，不能重名（key 唯一）。

### 2. 常用实现类

| 实现类 | 特点 | 使用频率 |
|--------|------|---------|
| `HashMap` | 最常用，无序，速度快 | ⭐⭐ |
| `LinkedHashMap` | 保持插入顺序 | ⭐⭐ |
| `TreeMap` | 按 key 自动排序 | ⭐ |

### 3. 核心方法（记住这 8 个就够了）

| 方法               | 作用             | 示例                                   |
| ---------------- | -------------- | ------------------------------------ |
| `put(K, V)`      | 放一个键值对         | `map.put("张三", 25)`                  |
| `get(K)`         | 根据 key 取 value | `map.get("张三")` → `25`               |
| `remove(K)`      | 删除一个键值对        | `map.remove("张三")`                   |
| `containsKey(K)` | 判断 key 是否存在    | `map.containsKey("张三")` → `true`     |
| `size()`         | 有多少个键值对        | `map.size()` → `3`                   |
| `isEmpty()`      | 是否为空           | `map.isEmpty()` → `false`            |
| `keySet()`       | 获取所有 key 的集合   | `for (String key : map.keySet())`    |
| `entrySet()`     | 获取所有键值对的集合     | `for (Map.Entry e : map.entrySet())` |

### 4. 多态写法（标准写法）

```java
// ✅ 推荐：左边用接口，右边用实现类
Map<String, Integer> map = new HashMap<>();
```

---

## 应该掌握（经常遇到）

### 5. 遍历 Map 的三种方式

```java
Map<String, Integer> map = new HashMap<>();
map.put("张三", 25);
map.put("李四", 30);

// 方式1：entrySet（最常用，同时拿 key 和 value）
for (Map.Entry<String, Integer> entry : map.entrySet()) {
    System.out.println(entry.getKey() + " → " + entry.getValue());
}

// 方式2：keySet（只需要 key，或需要 key 去 get value）
for (String key : map.keySet()) {
    System.out.println(key + " → " + map.get(key));
}

// 方式3：forEach（最简洁，Java 8+）
map.forEach((key, value) -> {
    System.out.println(key + " → " + value);
});
```

### 6. `put` 的返回值

```java
Integer old = map.put("张三", 26);  // 返回旧值 25
// 如果 key 不存在，返回 null
Integer old2 = map.put("王五", 28); // 返回 null（之前没有"王五"）
```

### 7. key 不能重复

```java
map.put("张三", 25);
map.put("张三", 30);  // 不会新增，而是覆盖！
// map 里只有 1 个 "张三"，value 变成了 30
```

---

## 了解即可（遇到时能看懂）

### 8. 泛型通配符

```java
void putAll(Map<? extends K, ? extends V> m);
```

知道 `? extends K` 表示"K 或 K 的子类"就行，不用深究。

### 9. Map.Entry

```java
Map.Entry<K, V>  // Map 里嵌套的子接口，代表一对键值对
```

知道 `getKey()` 和 `getValue()` 两个方法就行。

---

## 不需要深究（框架源码层面）

| 内容                                         | 原因              |
| ------------------------------------------ | --------------- |
| `hashCode()` / `equals()` 在 HashMap 中的具体实现 | 框架内部机制，不影响日常使用  |
| 扩容机制（负载因子 0.75、红黑树转换）                      | 了解概念即可，不需要会算    |
| `computeIfAbsent()`、`merge()` 等 Java 8 方法  | 偶尔用，遇到再查        |
| `@Contract`、`@Flow` 等注解                    | 静态分析工具的注解，不影响运行 |

---

## 一张速查表

```
Map 核心知识
│
├── 是什么？    → 键值对集合（key → value），key 唯一
├── 用什么？    → HashMap（最常用）
├── 怎么存？    → put(key, value)
├── 怎么取？    → get(key)
├── 怎么删？    → remove(key)
├── 怎么遍历？  → entrySet() / forEach()
├── 怎么写？    → Map<K,V> map = new HashMap<>()（多态）
└── 注意什么？  → key 不能重复，重复会覆盖
```

> 一句话：**掌握 `put`、`get`、`remove`、`entrySet` 遍历、多态写法**这 5 个点，就能应对若依项目中 95% 的 Map 使用场景。其他内容遇到再查即可。