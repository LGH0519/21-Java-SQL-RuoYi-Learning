# Day 04：Java 类、对象、构造方法与封装

> 今日目标：把“档案”从零散变量升级为一个对象。只操作 `E:\WorkSpace\java-learning`；不学习继承、集合、SQL。

## 知识点速记（完成当天任务后复习）

### 1. 类与对象

- **类（class）**是模板，规定一类数据有哪些字段、能做哪些事；`Archive` 是档案模板。
- **对象（object）**是依据类创建的具体实例；`new Archive(...)` 创建一份具体档案。
- **字段**保存对象状态；**实例方法**描述对象行为。

```java
Archive contract = new Archive("DA-001", "采购合同", "合同", "AVAILABLE", 10);
contract.printSummary();
```

`contract` 是引用变量，保存“找到这个对象”的引用；同一个对象的字段变化，会在后续所有通过该引用的访问中体现。

### 2. 构造方法与 `this`

构造方法在 `new` 时自动执行，用于初始化字段：方法名必须等于类名，**没有返回类型，不能写 `void`**。

```java
public Archive(String archiveNo, String name) {
    this.archiveNo = archiveNo;
    this.name = name;
}
```

- 左侧 `this.archiveNo` 是当前对象的字段；右侧 `archiveNo` 是参数。
- 写了带参数构造方法后，不能再直接 `new Archive()`，除非自己另写无参构造方法。

### 3. 封装

字段通常写 `private`，外部不能随意直接改；通过 `public` 的 getter/setter 读取、修改。

```java
private String status;
public String getStatus() { return status; }
public void setStatus(String status) { this.status = status; }
```

封装不是为了把代码写复杂，而是让“改变状态”有明确入口；以后可在 setter 中加入校验而不必让所有调用处都重写。

| 易混点                | 正确理解                    |
| ------------------ | ----------------------- |
| 类等于对象？             | 不等于；类是模板，对象是具体实例。       |
| 构造方法是普通方法？         | 不是；它没有返回类型，`new` 时自动调用。 |
| `this` 是什么？        | 当前正在操作的对象。当前对象的地址       |
| `private` 后无法使用字段？ | 外部不能直接用，仍可通过公开方法访问。     |

## 视频与文档重点整理

| 资源 | 只学习这些内容 |
|---|---|
| [Java 零基础视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/) | 标题含“类和对象、构造方法、this、封装”的选集。 |
| [Java 对象和类](https://www.runoob.com/java/java-object-classes.html) | 类、对象、字段、方法、`new`、构造方法。 |
| [Java 封装](https://www.runoob.com/java/java-encapsulation.html) | `private`、getter、setter、`this`。 |

**今天跳过：** 继承、接口、`static` 字段、`toString` 重写、包。

## 今天照做（09:00–23:00）

### 09:00–12:00：先理解模板和实例

1. 看视频和两篇文档，只围绕“档案类”做笔记。
2. 在纸上区分：`Archive`（类）、`contract`（引用变量）、`new Archive(...)`（创建对象）、`contract.status`（对象字段）。
3. 手写构造方法模板与 getter/setter 各一遍；指出 `this.status = status` 的左右分别是谁。

### 15:00–18:00：创建 `Archive.java`

1. 新建 `Archive` 类，完整手敲下面代码。
2. 每个字段都必须是 `private`；不要用 IDEA 自动生成后不阅读。

```java
public class Archive {
    private String archiveNo;
    private String name;
    private String category;
    private String status;
    private int retentionYears;

    public Archive(String archiveNo, String name, String category, String status, int retentionYears) {
        this.archiveNo = archiveNo;
        this.name = name;
        this.category = category;
        this.status = status;
        this.retentionYears = retentionYears;
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
}
```

### 19:00–20:30：创建 `Day04.java` 并调试对象

```java
public class Day04 {
    public static void main(String[] args) {
        Archive[] archives = {
                new Archive("DA-001", "采购合同", "合同", "AVAILABLE", 10),
                new Archive("DA-002", "项目验收报告", "项目资料", "BORROWED", 30),
                new Archive("DA-003", "行政通知", "行政文件", "AVAILABLE", 5)
        };

        archives[0].setStatus("BORROWED");
        for (int i = 0; i < archives.length; i++) {
            archives[i].printSummary();
        }
    }
}
```

1. 运行，确认第一条状态已变成 `BORROWED`。
2. 在 `archives[0].setStatus("BORROWED")` 打断点，用 Shift+F9 启动。
3. Variables 中展开 `archives` → `[0]`，看 5 个私有字段；F7 进入 `setStatus`，确认 `this.status` 被赋值。

### 20:30–23:00：巩固题与验收

1. 增加 `setRetentionYears(int retentionYears)`；传入 `0` 时先打印“年限不合法”，不修改原值；传入 `1` 时修改成功。
2. 创建第二个对象，证明各对象字段互不影响。
3. 不看资料回答：为什么字段要 `private`？为什么构造方法不能写 `void`？`this` 用来解决什么问题？

卡住时按顺序检查：类名和文件名；构造方法参数顺序；是否用对象名调用实例方法；`private` 字段是否误从 `Day04` 直接访问。AI 提问必须附上构造调用和第一条报错。

## 今日完成清单

- [ ] 已能区分类、对象、字段、方法。
- [ ] `Archive` 的 5 个字段均为 `private`。
- [ ] 构造方法、getter、`setStatus`、`printSummary` 可运行。
- [ ] `Day04` 有 3 个对象，Debug 中已展开一个对象字段。
- [ ] 已完成保管年限校验小题。

---

## 任务参考答案（完成后再查看）

```java
public void setRetentionYears(int retentionYears) {
    if (retentionYears <= 0) {
        System.out.println("年限不合法");
        return;
    }
    this.retentionYears = retentionYears;
}
```

**今日通过标准：** 你能说“类是模板、对象是具体档案”，并能在断点处说明 `archives[0]` 的 `status` 如何从 `AVAILABLE` 变为 `BORROWED`。
