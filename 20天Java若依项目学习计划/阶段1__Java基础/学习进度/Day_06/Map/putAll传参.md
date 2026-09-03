## `Map<? extends K, ? extends V> m` 逐层拆解

---

## 从内到外拆解

```
Map<? extends K, ? extends V> m
```

---

## 逐个解释

### `?` —— 通配符（未知类型）

`?` 表示"某种类型，但我不知道具体是什么"。

> 类比：就像说"某种水果"——我知道它是水果，但不知道是苹果还是香蕉。

### `extends` —— 上限（不超过这个类型）

`? extends K` 表示"K 或者 K 的任意子类"。

| 写法 | 含义 | 类比 |
|------|------|------|
| `? extends K` | K 或 K 的子类都行 | "水果或水果的子类"——苹果✅、香蕉✅、石头❌ |
| `? extends V` | V 或 V 的子类都行 | 同上 |

---

## 实例说明

### 场景 1：完全匹配（最常见）

```java
Map<String, Integer> map1 = new HashMap<>();
map1.put("张三", 25);

Map<String, Integer> map2 = new HashMap<>();
map2.putAll(map1);  // ✅ K=String, V=Integer，完全匹配
```

### 场景 2：key 是子类

```java
Map<String, Integer> map1 = new HashMap<>();
map1.put("张三", 25);

// LinkedHashMap 是 HashMap 的子类
Map<String, Integer> map2 = new LinkedHashMap<>();
map2.putAll(map1);  // ✅ LinkedHashMap 是 Map 的实现类，兼容
```

### 场景 3：value 是子类

```java
Map<String, Number> map1 = new HashMap<>();
map1.put("张三", 25);       // Integer 是 Number 的子类 ✅
map1.put("李四", 3.14);     // Double 是 Number 的子类 ✅

Map<String, Integer> map2 = new HashMap<>();
map2.put("王五", 28);

map1.putAll(map2);  // ✅ map2 的 V=Integer，Integer extends Number，符合 ? extends V
```

### 场景 4：不兼容的情况（编译报错）

```java
Map<String, Integer> map1 = new HashMap<>();

Map<Integer, String> map2 = new HashMap<>();  // K 和 V 都反了

map1.putAll(map2);  // ❌ 编译报错！
// Integer 不是 String 的子类，String 也不是 Integer 的子类
```

---

## 为什么用 `? extends` 而不是直接写 `Map<K, V>`？

**为了更灵活。** 如果写成 `Map<K, V>`，类型必须完全一致：

```java
// 如果签名是 void putAll(Map<K, V> m)
Map<String, Number> map1 = new HashMap<>();
Map<String, Integer> map2 = new HashMap<>();

map1.putAll(map2);  // ❌ 报错！Integer ≠ Number，类型不完全匹配

// 实际签名是 void putAll(Map<? extends K, ? extends V> m)
map1.putAll(map2);  // ✅ 通过！Integer extends Number，符合上限要求
```

> 类比：
> - `Map<K, V>` = "只收 exactly 苹果"——香蕉不行，哪怕香蕉是水果的子类也不行
> - `Map<? extends K, ? extends V>` = "收苹果或苹果的子类"——苹果✅、红富士✅、青苹果✅

---

## 一张图总结

```
void putAll(Map<? extends K, ? extends V> m);

假设当前 Map 是 Map<String, Number>：
  K = String,  V = Number

  ? extends K  →  String 或 String 的子类
  ? extends V  →  Number 或 Number 的子类（Integer、Double、Float...）

  能传入的 Map：
    Map<String, Integer>    ✅  （Integer extends Number）
    Map<String, Double>     ✅  （Double extends Number）
    Map<String, Number>     ✅  （完全匹配）
    Map<Integer, String>    ❌  （Integer 不是 String 的子类）
```

> 一句话：`putAll` 的参数要求是"一个 Map，它的 key 类型不超过 K，value 类型不超过 V"——用 `? extends` 放宽了类型限制，让子类类型的 Map 也能传进来。