# Day 09：Java 阶段测试、三层控制台档案借阅项目与 Debug

> 今日目标：不看新视频，检验 Day01–D08；把控制台代码拆成数据类、业务类、启动类。**10 题至少独立完成 8 题，才进入 SQL。**

## 知识点速记（完成当天任务后复习）

今天不是学习新名词，而是把已有知识放回正确位置：

```text
Day09Main（程序入口：准备测试数据、输出结果）
        ↓ 调用
ArchiveService（业务：查找、判断能否借、改状态）
        ↓ 操作
Archive（数据与基础行为：编号、名称、状态、canBorrow）
        ↓ 继承/接口
BaseRecord / StatusCheckable
```

| 概念     | 在今天项目的位置                                                     |
| ------ | ------------------------------------------------------------ |
| 类与对象   | `Archive` 是模板；每条档案是对象。                                       |
| 集合     | `List<Archive>` 存多条档案。                                       |
| 方法/返回值 | `borrow` 交回成功/失败文字。                                          |
| 条件     | 找不到、不可借、成功三条分支。                                              |
| 循环     | 逐条按编号查找。                                                     |
| 异常     | 编号为空属于非法输入，抛 `IllegalArgumentException`。                     |
| Debug  | 从 `Day09Main → service.borrow → findByNo → canBorrow` 追失败原因。 |

## 今天照做（09:00–23:00）

### 09:00–12:00：10 题闭卷自测

先新建 `Day09Quiz.java`，不看资料完成每题；每题可先写注释再代码。完成后才对照下方答案。

|  编号 | 覆盖点   | 题目                                       |
| --: | ----- | ---------------------------------------- |
|   1 | 变量/类型 | 声明档案名、保管年限、是否借出并输出。                      |
|   2 | 条件    | 状态为 `AVAILABLE` 才输出“可借”。                 |
|   3 | 循环    | 用 `for` 输出 1–5。                          |
|   4 | 数组    | 求 `{4, 9, 2}` 最大值。                       |
|   5 | 方法    | 写 `int add(int a, int b)`。               |
|   6 | 类     | 写含 `private name` 的最小类与 getter。          |
|   7 | 继承    | 写 `Child extends Parent`，调用父类公开方法。       |
|   8 | 接口    | 写接口方法并由类实现。                              |
|   9 | 集合    | 用 `List<String>` 存两条编号并遍历。               |
|  10 | 异常    | 空编号时抛并 catch `IllegalArgumentException`。 |

**记录规则：** 每题标记“独立完成 / 查资料后完成 / 未完成”。独立完成 ≥ 8 才算通过；不足 8，下午前回到对应 D01–D08 的“知识点速记”和练习，不硬进入 SQL。

### 15:00–18:00：完成三层控制台项目

> 为避免 Day04–D08 留下多个版本，请将 `Archive.java` **替换为本最终版**；保留 `BaseRecord.java`、`StatusCheckable.java`。再新建 `ArchiveService.java`、`Day09Main.java`。三份文件都在同一 `src` 下，暂不建 package。

`Archive.java`：

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

`ArchiveService.java`：

```java
import java.util.List;

public class ArchiveService {
    private List<Archive> archives;
    public ArchiveService(List<Archive> archives) { this.archives = archives; }

    public Archive findByNo(String archiveNo) {
        for (Archive archive : archives) {
            if (archive.getArchiveNo().equals(archiveNo)) {
                return archive;
            }
        }
        return null;
    }

    public String borrow(String archiveNo) {
        if (archiveNo == null || archiveNo.isBlank()) {
            throw new IllegalArgumentException("档案编号不能为空");
        }
        Archive archive = findByNo(archiveNo);
        if (archive == null) {
            return "借阅失败：未找到档案";
        }
        if (!archive.canBorrow()) {
            return "借阅失败：档案不可借";
        }
        archive.setStatus("BORROWED");
        return "借阅成功：" + archive.getName();
    }
}
```

`Day09Main.java`：

```java
import java.util.ArrayList;
import java.util.List;

public class Day09Main {
    public static void main(String[] args) {
        List<Archive> archives = new ArrayList<>();
        archives.add(new Archive("DA-001", "采购合同", "AVAILABLE", 1, "admin", "2026-08-30"));
        archives.add(new Archive("DA-002", "验收报告", "BORROWED", 1, "admin", "2026-08-30"));
        ArchiveService service = new ArchiveService(archives);

        try {
            System.out.println(service.borrow("DA-001"));
            System.out.println(service.borrow("DA-001"));
            System.out.println(service.borrow("DA-999"));
            System.out.println(service.borrow(" "));
        } catch (IllegalArgumentException e) {
            System.out.println("输入错误：" + e.getMessage());
        }
    }
}
```

**预期四种结果：** 成功；不可借；未找到；输入错误。

### 19:00–21:00：失败路径 Debug

1. 在第二次 `service.borrow("DA-001")` 打断点，用 Shift+F9。
2. F7 进入 `borrow`；确认参数是 `DA-001`，不是空字符串。
3. F7 进入 `findByNo`，观察循环找到的 `Archive` 不是 `null`。
4. 回到 `borrow`，F7 进入 `canBorrow`，展开对象字段，确认 `status` 已在第一次调用中变为 `BORROWED`。
5. 结论写入笔记：失败发生在“可借判断”，不是“查找”也不是“异常”。
6. 对空字符串调用重复，确认这次在 `throw` 后进入 `catch`；区分两种失败。

### 21:00–23:00：最终验收与学习整理

1. 为成功、不可借、不存在、空编号四个案例各写一行“输入 → 预期输出 → 实际输出”。
2. 删除无用的旧测试类可以吗？**今天不要删除。** 保留它们作为练习证据；只整理命名、在笔记说明每个类的作用。
3. 写 150–250 字项目讲解：用户输入编号后，代码如何经过 `Day09Main`、`ArchiveService`、`Archive` 得到结果。
4. 做一次普通 Run，做一次 Debug；保存 Debug 变量截图。

## 今日完成清单

- [ ] 10 题中至少 8 题独立完成；不足则已标出回补日。
- [ ] `Archive/ArchiveService/Day09Main` 运行四种结果正确。
- [ ] 已沿失败路径完成 F7/F8 调试并能说明状态变化。
- [ ] 已区分“业务失败文本”和“空编号异常”。
- [ ] 已完成 150–250 字讲解与调试截图。

---

## 任务参考答案（完成后再查看）

### 自测的最小答案要点

1. `String name = "合同"; int years = 10; boolean borrowed = false;`
2. `if ("AVAILABLE".equals(status)) { ... }`
3. `for (int i = 1; i <= 5; i++) { ... }`
4. 从 `numbers[0]` 开始，用循环更新 `max`。
5. `static int add(int a, int b) { return a + b; }`
6. 字段 `private String name;`，提供 `getName()`。
7. `class Child extends Parent { }`；调用父类 `public` 方法。
8. `interface Checkable { boolean check(); }`，实现类用 `@Override`。
9. `List<String> nos = new ArrayList<>();`，加入两项后 `for-each` 遍历。
10. `if (no == null || no.isBlank()) throw ...;`，调用处用 `try/catch`。

### 最终流程的口头答案

`Day09Main` 准备 `List<Archive>` 并调用 `ArchiveService.borrow`。服务先校验编号是否为空，空则抛异常；再循环查找对象，找不到返回失败文本；找到后调用对象的 `canBorrow`，状态不是 `AVAILABLE` 或库存不大于 0 就返回不可借；可借时把对象状态改为 `BORROWED` 并返回成功。第二次借同一编号失败，是因为 List 里保存的还是同一个对象，第一次修改已保留。

**阶段通过标准：** 10 题独立 ≥ 8，且能从断点说明“第二次借阅失败”的精确分支；否则先补弱项，不进入 Day10 SQL。
