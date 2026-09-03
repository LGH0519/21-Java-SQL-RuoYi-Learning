# `ServiceException`——你需要掌握的核心内容

---

## 一、它是什么？

`ServiceException` 是若依项目自定义的**业务异常类**，用来在业务逻辑中"报告错误"。

> 类比：就像餐厅服务员遇到"菜卖完了"、"座位满了"等情况时，不是自己处理，而是**喊一声"有问题！"**，让经理（全局异常处理器）来统一处理。

---

## 二、必须掌握（3 个点就够了）

### 1. 怎么抛出（日常开发最常用的写法）

```java
// ✅ 最常用：只传错误消息
throw new ServiceException("字典类型已存在");

// ✅ 传错误消息 + 错误码
throw new ServiceException("操作失败", 500);
```

> 这就是你**唯一需要记住的用法**——在业务逻辑中遇到错误，`throw new ServiceException("错误信息")` 就行。

---

### 2. 它继承的是 `RuntimeException`（非受检异常）

```java
public final class ServiceException extends RuntimeException
```

| 父类 | 特点 | 需要 try-catch 吗？ |
|------|------|-------------------|
| `Exception`（受检异常） | 编译器强制你处理 | ✅ 必须 try-catch 或声明 throws |
| `RuntimeException`（非受检异常） | 编译器不强制 | ❌ 可以不管，让它自动往上抛 |

`ServiceException` 继承 `RuntimeException`，所以你在 Service 层抛出它时，**不需要 try-catch**，它会自动被全局异常处理器捕获并返回给前端。

> 类比：受检异常像"必须签字的文件"——你不处理就过不去；非受检异常像"紧急报警"——不用你处理，系统自动有人管。

---

### 3. 三个属性

| 属性 | 类型 | 作用 | 类比 |
|------|------|------|------|
| `message` | `String` | 错误提示（给用户看的） | "菜卖完了" |
| `code` | `Integer` | 错误码（程序判断用） | 错误编号 500 |
| `detailMessage` | `String` | 内部调试错误（给开发看的） | 详细的报错原因 |

---

## 三、应该掌握（理解原理）

### 4. 构造方法有 3 种

```java
new ServiceException();                    // 空构造（基本不用）
new ServiceException("错误消息");            // 最常用 ✅
new ServiceException("错误消息", 500);       // 带错误码
```

### 5. `setMessage()` / `setDetailMessage()` 返回 `this`

```java
public ServiceException setMessage(String message) {
    this.message = message;
    return this;  // ← 返回自己，支持链式调用
}
```

这意味着可以**链式调用**：

```java
throw new ServiceException()
    .setMessage("操作失败")
    .setDetailMessage("数据库连接超时");
```

> 类比：就像填表——填完一栏接着填下一栏，不用重新拿一张表。

---

## 四、了解即可

### 6. `final` 修饰符

```java
public final class ServiceException
```

`final` 表示这个类**不能被继承**。若依不允许你创建 `ServiceException` 的子类，统一用这一个异常类就行。

### 7. `serialVersionUID`

```java
private static final long serialVersionUID = 1L;
```

序列化相关，日常开发不需要关心。

---

## 五、它在项目中的位置

```
你的业务代码（Service 层）
    │
    │  throw new ServiceException("字典类型已存在")
    ▼
全局异常处理器（GlobalExceptionHandler）
    │
    │  捕获 ServiceException
    │  提取 message 和 code
    ▼
返回给前端
    { "code": 500, "msg": "字典类型已存在" }
```

> 你只需要负责"抛出异常"，剩下的"捕获 → 封装 → 返回前端"都是全局异常处理器自动完成的。

---

## 速查表

```
ServiceException 核心知识
│
├── 是什么？    → 业务异常类，用于报告业务错误
── 怎么抛？    → throw new ServiceException("错误消息")
├── 要 try-catch 吗？ →  不需要（继承 RuntimeException）
├── 谁来处理？  → 全局异常处理器自动捕获并返回前端
├── 能继承吗？  →  不能（final 类）
└── 有几个构造方法？ → 3 个（空 / 消息 / 消息+错误码）
```

> 一句话：**只需要记住 `throw new ServiceException("错误消息")` 这一种写法**，其他都是框架自动处理的。它是若依项目中最常用的异常类，几乎所有业务报错都用它。