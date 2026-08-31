## 先搞懂：什么是"实例"？

**实例（Instance）= 根据"图纸"造出来的"实物"。**

| 概念 | 类比 | 代码对应 |
|------|------|---------|
| **类（Class）** | 月饼的**模具/图纸** | `class MoonCake { ... }` |
| **实例（Instance）** | 用模具压出来的**一个月饼** | `new MoonCake()` |

```java
MoonCake moonCake1 = new MoonCake();  // 第 1 个月饼（第 1 个实例）
MoonCake moonCake2 = new MoonCake();  // 第 2 个月饼（第 2 个实例）
```

> 类比：模具只有 1 个（类），但可以用它压出无数个 月饼（实例）。每个月饼都是独立的——你咬了第一个，第二个不会少一口。

**"同一个实例"** = 同一个实物，不是重新造一个新的。

---

## 再搞懂：什么是 `@Bean` 方法？

`@Bean` 是一个注解，贴在**方法**上面，告诉 Spring："这个方法的返回值，请当作一个 Bean（交给 Spring 管理的对象）来管理。"

```java
@Configuration
public class MyConfig {

    @Bean
    public MoonCake moonCake() {
        return new MoonCake();  // 造一个月饼，交给 Spring 管理
    }
}
```

**`@Bean` 方法 = 被 `@Bean` 注解标记的方法**，不是"@Bean 里面的方法"。`@Bean` 本身不是容器，它只是一张贴纸，贴在方法上。

> 类比：`@Bean` 就像在工厂流水线上贴了一个标签——"这个工位生产的东西，要送到仓库（Spring 容器）统一管理"。

---

## 最后搞懂：整句话是什么意思？

现在把三个概念串起来。假设你有这样的代码：

```java
@Configuration
public class MyConfig {

    @Bean
    public MoonCake moonCake() {
        System.out.println("正在造月饼...");
        return new MoonCake();
    }

    @Bean
    public GiftBox giftBox() {
        GiftBox box = new GiftBox();
        box.setMoonCake(moonCake());  // 第 1 次调用 moonCake()
        box.setExtraMoonCake(moonCake());  // 第 2 次调用 moonCake()
        return box;
    }
}
```

`giftBox()` 方法里调用了**两次** `moonCake()`。这时候就出现了关键问题：

### `proxyBeanMethods = true`（默认）

```
第 1 次调用 moonCake() → 造了 1 个月饼 → 月饼A
第 2 次调用 moonCake() → 不造新的！直接返回月饼A
```

> 两次调用拿到的是**同一个月饼**（同一个实例）。Spring 发现这个 `@Bean` 方法已经被调用过了，就直接把之前的结果给你，不再重新执行方法体。

### `proxyBeanMethods = false`

```
第 1 次调用 moonCake() → 造了 1 个月饼 → 月饼A
第 2 次调用 moonCake() → 又造了 1 个月饼 → 月饼B
```

> 两次调用拿到的是**两个不同的月饼**（两个不同的实例）。Spring 不拦截，每次都是真正执行方法体，`new MoonCake()` 每次都造一个新的。

---

## 用生活例子彻底说清楚

想象你开了一个月饼工厂：

```java
@Configuration  // 这是一个"工厂配置单"
public class FactoryConfig {

    @Bean  // "这个工位的产品要入库管理"
    public MoonCake moonCake() {
        return new MoonCake();  // 造一个月饼
    }
}
```

| 模式 | 行为 | 类比 |
|------|------|------|
| `proxyBeanMethods = true` | 有人来要月饼，第一次给他现做一个。第二次再来要，**直接把第一次做的那个给他**，不再重新做 | 就像餐厅的"今日推荐菜"——不管几个客人点，都上**同一盘菜** |
| `proxyBeanMethods = false` | 每次有人来要月饼，都**重新做一个新的**给他 | 就像自助餐——每个客人都拿到**自己那份新的** |

---

## 一句话总结

| 你不懂的词 | 一句话解释 |
|-----------|-----------|
| **实例** | 用 `new` 造出来的具体对象（月饼），类是模具，实例是月饼 |
| **`@Bean` 方法** | 被 `@Bean` 注解标记的方法（不是"@Bean 里面的方法"，而是"贴着 @Bean 标签的方法"） |
| **"同一个 @Bean 方法被多次调用时，返回同一个实例"** | 同一个方法被调用多次，Spring 只执行一次方法体，后面的调用直接返回第一次的结果，不再重新 `new` |