# Day 07：Java String、LocalDate 与异常

> 今日目标：正确处理档案编号、到期日期和非法输入；分清“业务正常结果”与“输入不合法”。

## 知识点速记（完成当天任务后复习）

### 1. String

`String` 是不可变的文本引用类型；修改看起来像修改，实际得到新字符串。比较内容用 `equals`。

| 方法/写法 | 作用 | 档案例子 |
|---|---|---|
| `isBlank()` | 空/只有空白时为 `true`（JDK 17 可用）。 | 编号是否为空。 |
| `length()` | 文本长度。 | 编号最小长度。 |
| `startsWith("DA-")` | 是否以指定前缀开始。 | 判断档案编号格式。 |
| `equals` | 比较内容。 | 状态是否为 `AVAILABLE`。 |
| `trim()` | 去掉首尾空白并返回新文本。 | 输入预处理。 |

### 2. LocalDate

`LocalDate` 表示“年-月-日”，没有时间和时区，适合借阅到期日。

```java
LocalDate today = LocalDate.now();
LocalDate dueDate = today.plusDays(7);
boolean overdue = dueDate.isBefore(today);
```

- `plusDays`/`minusDays` 返回新的日期，原日期不变。
- 到期日等于今天不算逾期：`isBefore(today)` 为 `false`。
- `LocalDate.parse("2026-09-01")` 要求 ISO 格式；今天优先使用 `of`、`now`、`plusDays`。

### 3. 异常：正常结果与非法输入分开

```java
if (dueDate.isBefore(LocalDate.now())) {
    return true; // 合法输入下的正常业务结果：已逾期
}
if (archiveNo == null || archiveNo.isBlank()) {
    throw new IllegalArgumentException("档案编号不能为空"); // 调用者给了非法输入
}
```

`try` 放可能抛异常的代码；`catch` 接住并处理。今天使用 `IllegalArgumentException`，它表示方法参数不合法。

| 易混点 | 正确结论 |
|---|---|
| `false` 与异常 | `false` 可以是正常业务结果；异常表示无法按约定继续执行。 |
| `null` 与空字符串 | `null` 是没有引用；`""` 是长度为 0 的字符串。 |
| `catch` 能替代校验吗？ | 不能；先写清晰校验，catch 只在调用边界处理异常。 |

## 视频、文档与若依对应点

| 资源 | 只学习这些内容 |
|---|---|
| [Java 零基础视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/) | String、日期时间、异常处理。 |
| [Java LocalDate](https://www.runoob.com/java/java-localdate-class.html) | `now/of/parse/plusDays/isBefore`。 |
| [Java 教程目录](https://m.runoob.com/java/) | 从目录进入 String、异常处理，只看基础方法和 `try/catch`。 |
| 本机 `ServiceException.java` | `E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-common\src\main\java\com\ruoyi\common\exception\ServiceException.java`；只读它继承 `RuntimeException` 的用途。 |

## 今天照做（09:00–23:00）

### 09:00–12:00：资料学习与预测输出

1. 看视频，读资源；跳过正则表达式、格式化器细节、自定义异常体系。
2. 写下并预测：`" DA-001 ".trim()`、`"  ".isBlank()`、`LocalDate.now().plusDays(1)` 的意义。
3. 打开 `ServiceException.java`，只记“若依用运行时异常表达业务服务层失败”；不要背构造方法，也不要修改。

### 15:00–18:00：完成 `ArchiveValidator.java`

```java
import java.time.LocalDate;

public class ArchiveValidator {
    public static void validateArchiveNo(String archiveNo) {
        if (archiveNo == null || archiveNo.isBlank()) {
            throw new IllegalArgumentException("档案编号不能为空");
        }
        if (!archiveNo.startsWith("DA-")) {
            throw new IllegalArgumentException("档案编号必须以 DA- 开头");
        }
    }

    public static boolean isOverdue(LocalDate dueDate) {
        if (dueDate == null) {
            throw new IllegalArgumentException("到期日不能为空");
        }
        return dueDate.isBefore(LocalDate.now());
    }
}
```

再新建 `Day07.java`：

```java
import java.time.LocalDate;

public class Day07 {
    public static void main(String[] args) {
        try {
            ArchiveValidator.validateArchiveNo("DA-2026-001");
            boolean overdue = ArchiveValidator.isOverdue(LocalDate.now().minusDays(1));
            System.out.println("是否逾期：" + overdue);

            ArchiveValidator.validateArchiveNo(" ");
        } catch (IllegalArgumentException e) {
            System.out.println("输入错误：" + e.getMessage());
        }
    }
}
```

**验收：** 先输出 `是否逾期：true`，再输出“输入错误：档案编号不能为空”。

### 19:00–20:20：Debug 异常与日期

1. 在 `validateArchiveNo(" ")` 打断点，F7 进入；观察 `archiveNo` 是空白文本，不是 `null`。
2. F8 到 `throw` 行；按 F8 后程序跳转至 `catch`，查看异常对象 `e` 和 `e.getMessage()`。
3. 将 `minusDays(1)` 分别改为 `LocalDate.now()` 和 `plusDays(1)`；解释三个结果。

### 20:20–23:00：综合题、验收与 AI 边界

1. 给 `validateArchiveNo` 增加长度至少 7 的检查；测试 `null`、`""`、`"XX-1"`、`"DA-001"`。
2. 写 `DueDateDemo`：显示今天、到期日、是否逾期；测试昨天、今天、明天。
3. 不看资料回答：为什么 `"AVAILABLE".equals(status)` 更稳妥？为什么“未逾期”应返回 `false` 而非抛异常？`try/catch` 何时执行？

卡住先看：是否 `import java.time.LocalDate;`；日期变量是否为 `null`；异常是否在 `try` 内触发；不要用 catch 吞掉错误后什么都不输出。向 AI 提问时附上输入、异常消息和断点值。

## 今日完成清单

- [ ] 会用 `isBlank/startsWith/equals/trim` 处理编号。
- [ ] 会用 `LocalDate` 判断昨天、今天、明天是否逾期。
- [ ] 能区分正常 `false` 与 `IllegalArgumentException`。
- [ ] 已只读查看 `ServiceException`，未修改若依。
- [ ] Debug 中看到了 `throw → catch` 跳转。

---

## 任务参考答案（完成后再查看）

```java
if (archiveNo.length() < 7) {
    throw new IllegalArgumentException("档案编号长度不足");
}
```

| 到期日 | `isOverdue` 结果 |
|---|---|
| 昨天 | `true` |
| 今天 | `false` |
| 明天 | `false` |

**今日通过标准：** 能在一句话中区分“合法但未逾期”和“编号为空的非法输入”。
