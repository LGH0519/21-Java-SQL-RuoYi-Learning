# Day 05：Java 继承、接口与多态（只学若依会遇到的部分）

> 今日目标：理解“公共字段放父类、能力写接口、用父类型/接口类型接收对象”。只读若依源码，不修改。

## 知识点速记（完成当天任务后复习）

### 1. 继承：`extends`

继承表示“子类是一种父类”，用于复用真正共同的数据/行为，不是为了少写代码就随便继承。

```java
public class Archive extends BaseRecord { }
```

- `Archive` 自动拥有 `BaseRecord` 的公开/受保护成员；`private` 字段仍需通过父类 getter 使用。
- 子类构造方法第一步用 `super(...)` 初始化父类部分；若不写，编译器尝试调用父类无参构造方法。
- Java 类只能 `extends` 一个直接父类。

### 2. 接口：`implements`

接口规定“具备什么能力”，不是保存对象具体状态的地方。类可实现多个接口。

```java
public interface StatusCheckable {
    boolean canBorrow();
}
public class Archive extends BaseRecord implements StatusCheckable { }
```

实现类必须完成接口要求的方法；写 `@Override` 让编译器帮助检查方法签名。

### 3. 多态与重写

```java
StatusCheckable checkable = archive;
System.out.println(checkable.canBorrow());
```

变量的**声明类型**决定可调用哪些方法；实际对象类型决定运行哪一个重写实现。`@Override` 是重写，不是重载；重载是同名但参数不同。

| 易混点                      | 正确结论                             |
| ------------------------ | -------------------------------- |
| `extends` 与 `implements` | 前者继承一个父类；后者实现一个/多个接口。            |
| `private` 父类字段能直接访问吗？    | 不能；使用父类提供的 getter/setter。        |
| 接口能直接 `new` 吗？           | 不能；用实现它的类创建对象。                   |
| 抽象类与接口                   | 都可表达抽象；今天只要求会读抽象类，项目能力约定优先用接口练习。 |

## 视频、文档与若依对应点

| 资源 | 只学习这些内容 |
|---|---|
| [Java 零基础视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/) | “继承、重写、抽象类、接口、多态”选集。 |
| [Java 接口](https://www.runoob.com/java/java-interfaces.html) | `interface`、`implements`、实现方法。 |
| [Java 对象和类](https://www.runoob.com/java/java-object-classes.html) | 继承/多态的概念部分。 |
| 本机 `BaseEntity.java` | `E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-common\src\main\java\com\ruoyi\common\core\domain\BaseEntity.java`；只看公共创建/更新字段和 getter/setter。 |

## 今天照做（09:00–23:00）

### 09:00–12:00：建立正确判断

1. 看指定视频，记住“is-a（是一种）”才考虑继承：档案是一种基础记录；借阅申请不是档案，不应继承 `Archive`。
2. 读接口文档，分别写出 `extends`、`implements`、`@Override` 的一句话解释。
3. 打开上述 `BaseEntity.java`，只看 `createBy/createTime/updateBy/updateTime/remark` 与 getter/setter；不要改、不要试图全部理解 `Serializable`。

### 15:00–18:00：改造 Day04 的 `Archive`

1. 新建 `BaseRecord.java` 和 `StatusCheckable.java`。
2. 将 Day04 的 `Archive.java` 替换为下列版本；这不是新建第二个 `Archive` 类。它特意保留 Day04 的五参数构造方法和 `printSummary()`，所以旧练习仍可运行。

```java
public class BaseRecord {
    private String createBy;
    private String createTime;
    public BaseRecord(String createBy, String createTime) {
        this.createBy = createBy;
        this.createTime = createTime;
    }
    public String getCreateBy() { return createBy; }
    public String getCreateTime() { return createTime; }
}
```

```java
public interface StatusCheckable {
    boolean canBorrow();
}
```

```java
public class Archive extends BaseRecord implements StatusCheckable {
    private String archiveNo;
    private String name;
    private String category;
    private String status;
    private int retentionYears;
    private int remainingCopies;

    public Archive(String archiveNo, String name, String category, String status, int retentionYears) {
        super("system", "未记录");
        this.archiveNo = archiveNo;
        this.name = name;
        this.category = category;
        this.status = status;
        this.retentionYears = retentionYears;
        this.remainingCopies = 1;
    }
    public Archive(String archiveNo, String name, String status, int remainingCopies,
                   String createBy, String createTime) {
        super(createBy, createTime);
        this.archiveNo = archiveNo;
        this.name = name;
        this.category = "未分类";
        this.status = status;
        this.retentionYears = 0;
        this.remainingCopies = remainingCopies;
    }
    public String getArchiveNo() { return archiveNo; }
    public String getName() { return name; }
    public String getCategory() { return category; }
    public String getStatus() { return status; }
    public int getRetentionYears() { return retentionYears; }
    public void setStatus(String status) { this.status = status; }
    public void printSummary() {
        System.out.println(archiveNo + " | " + name + " | " + category
                + " | " + status + " | " + retentionYears + "年");
    }

    @Override
    public boolean canBorrow() {
        return "AVAILABLE".equals(status) && remainingCopies > 0;
    }
}
```

### 19:00–23:00：多态、Debug 与验收

新建 `Day05.java`：

```java
public class Day05 {
    public static void main(String[] args) {
        Archive archive = new Archive("DA-001", "采购合同", "AVAILABLE", 1,
                "admin", "2026-08-30");
        StatusCheckable checkable = archive;
        System.out.println("创建人：" + archive.getCreateBy());
        System.out.println("可借阅：" + checkable.canBorrow());
        archive.setStatus("BORROWED");
        System.out.println("可借阅：" + checkable.canBorrow());
    }
}
```

1. 在 `checkable.canBorrow()` 打断点，F7 进入 `Archive.canBorrow()`；确认实际执行的是 `Archive` 的实现。
2. 把 `remainingCopies` 临时设为 0，解释为什么返回 `false`。
3. 在 `BaseRecord` 增加 `printCreator()`，从 `Archive` 对象调用，证明子类可继承公共行为。
4. 用一句话对比你自己的 `BaseRecord` 和若依的 `BaseEntity`：两者都收纳多类业务记录共有的创建/更新信息；若依版本更完整。

## 今日完成清单

- [ ] 能解释 `extends`、`implements`、`@Override`。
- [ ] `Archive` 已继承 `BaseRecord` 并实现 `StatusCheckable`。
- [ ] `Day05` 两次 `canBorrow` 分别输出 `true`、`false`。
- [ ] 已只读查看真实 `BaseEntity`，未改若依代码。

---

## 任务参考答案（完成后再查看）

```java
public void printCreator() {
    System.out.println("创建人：" + createBy + "，创建时间：" + createTime);
}
```

这段方法应加在 `BaseRecord` 内；因为字段是它自己的 `private` 字段，所以能直接使用。`Archive` 通过继承得到 `printCreator()`。

**今日通过标准：** 能用“公共数据”“规定能力”“实际对象执行实现”三句话分别解释继承、接口和多态。
