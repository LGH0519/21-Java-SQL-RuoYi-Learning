# Day 03：Java 数组、方法、参数与返回值

> 今日目标：把多条同类数据放进数组，并把重复逻辑提取为可调用的方法。只在 `E:\WorkSpace\java-learning` 操作；不学习对象、SQL 或若依源码。

## 知识点速记（完成当天任务后复习）

### 1. 数组

数组是**固定长度、元素类型相同**的一组数据。索引从 `0` 开始，最后一个索引是 `length - 1`。

```java
int[] retentionYears = {5, 10, 30};
System.out.println(retentionYears[0]); // 5
System.out.println(retentionYears.length); // 3；length 不是 length()
```

| 写法 | 含义 |
|---|---|
| `int[] numbers = {3, 8, 2};` | 创建并立即放入 3 个整数。 |
| `String[] names = new String[3];` | 创建长度为 3 的数组；元素还未设置。 |
| `numbers[1] = 10;` | 修改第二个元素。 |
| `numbers[i]` | 用变量 `i` 访问第 i 个元素。 |

- 数组越界，例如长度为 3 却访问 `numbers[3]`，会报 `ArrayIndexOutOfBoundsException`；循环通常写 `i < numbers.length`，不要写 `i <= numbers.length`。
- `int[]` 的元素默认是 `0`；`boolean[]` 默认 `false`；引用数组（如 `String[]`）默认 `null`。今天优先直接初始化，避免误把 `null` 当字符串使用。

### 2. 方法、参数与返回值

方法是放在类中的一段可复用代码。今天在 `main` 外写 `static` 方法，再由 `main` 调用。

```java
static int calculateOverdueDays(int borrowDays, int allowedDays) {
    int overdueDays = borrowDays - allowedDays;
    return overdueDays > 0 ? overdueDays : 0;
}
```

| 部分 | 例子 | 含义 |
|---|---|---|
| 返回类型 | `int` | 方法结束后交回的结果类型；没有结果用 `void`。 |
| 方法名 | `calculateOverdueDays` | 小驼峰，表达动作。 |
| 形式参数 | `int borrowDays` | 调用者交进来的数据，在方法内部使用。 |
| 方法体 | `{ ... }` | 实现过程。 |
| `return` | `return overdueDays;` | 立即结束方法并交回结果。 |

```java
int overdueDays = calculateOverdueDays(10, 7); // 10、7 是实参
System.out.println(overdueDays);               // 3
```

- **参数**是调用方法时传入的数据；**返回值**是方法交回调用处的数据。
- `void` 方法不交回结果，不能写进 `int result = ...`。
- 方法名相同、参数列表不同叫**重载**；只改返回类型不构成重载。

### 3. Day 03 易混点

| 问题 | 正确结论 |
|---|---|
| 数组为什么从 0 开始？ | Java 的数组索引规则就是从 0 到 `length - 1`；先按规则使用。 |
| `length` 和 `length()`？ | 数组用字段 `length`；`String` 的长度以后用方法 `length()`。 |
| `return` 和 `System.out.println`？ | `return` 把数据交回调用者；`println` 只输出到控制台。 |
| 方法参数改了会改变外部基本类型变量吗？ | 不会；基本类型的值被复制给参数。数组/对象的细节后续学习。 |
| `findMax` 初始值为什么取 `numbers[0]`？ | 才能正确处理全为负数的数组，不能随便从 0 开始。 |

## 视频与文档重点整理

| 资源 | 只学习这些内容 | 学完要能做什么 |
|---|---|---|
| [Java 零基础视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/) | 选集标题含“数组”“方法”“方法重载”。 | 写出数组声明、方法签名和调用。 |
| [Java 数组](https://www.runoob.com/java/java-array.html) | 一维数组声明、初始化、索引、`length`、普通 `for` 遍历。 | 不越界地找最大值。 |
| [Java 方法](https://www.runoob.com/java/java-methods.html) | 参数、返回类型、`return`、`void`、重载。 | 将三段逻辑写成方法。 |

**今天跳过：** 二维数组、`Arrays` 工具类、可变参数、递归、对象数组（明天学习）。

## 今天照做（09:00–23:00）

### 09:00–12:00：理解并手写最小模板

1. 打开 `E:\WorkSpace\java-learning`，确认 Project SDK 是 17。
2. 看指定视频的“数组、方法、方法重载”选集；视频出现对象、集合时跳过。
3. 阅读两个网页，只抄下列模板各一次：

```java
int[] numbers = {1, 2, 3};
for (int i = 0; i < numbers.length; i++) {
    System.out.println(numbers[i]);
}

static boolean methodName(String value) {
    return true;
}
```

4. 不运行，先在纸上写：`int[] values = {-2, -9, -3};` 的最大值应该是 `-2`；`values.length` 是 `3`。

**验收：** 能解释 `i < numbers.length` 为什么不会越界，能区分形参和实参。

### 15:00–18:00：完成核心类 `ArchiveTools`

1. `src` 右键 → **New → Java Class** → `ArchiveTools`。
2. 先手敲下方代码，再运行。每个方法至少改两组实参测试。

```java
public class ArchiveTools {
    public static boolean isValidArchiveNo(String no) {
        return no != null && no.startsWith("DA-") && no.length() >= 7;
    }

    public static int calculateOverdueDays(int borrowDays, int allowedDays) {
        int overdueDays = borrowDays - allowedDays;
        return overdueDays > 0 ? overdueDays : 0;
    }

    public static int findMax(int[] numbers) {
        int max = numbers[0];
        for (int i = 1; i < numbers.length; i++) {
            if (numbers[i] > max) {
                max = numbers[i];
            }
        }
        return max;
    }

    public static void main(String[] args) {
        System.out.println(isValidArchiveNo("DA-2026-001"));
        System.out.println(isValidArchiveNo("XX-1"));
        System.out.println(calculateOverdueDays(10, 7));
        System.out.println(calculateOverdueDays(5, 7));

        int[] retentionYears = {10, 30, 5, 20};
        System.out.println(findMax(retentionYears));
    }
}
```

3. 仅为今天的练习约定：`findMax` 的参数一定是“非空且至少有一个元素”的数组。Day 07 才学习怎样把非法输入变成明确异常。

**预期输出：** `true`、`false`、`3`、`0`、`30`，每行一个。

### 19:00–20:10：方法调试流程

1. 在 `main` 中的 `isValidArchiveNo("DA-2026-001")` 那一行打断点，按 **Shift + F9**。
2. Variables 中确认实参字符串；按 **F7** 进入方法。
3. 在方法内看形参 `no` 的值；按 **F8** 逐步执行，观察 `return` 前表达式为 `true`。
4. 按 **F8** 回到 `main`，观察调用结果；对 `"XX-1"` 重复一次，观察结果为 `false`。
5. 在 `findMax` 的 `if` 行打断点，输入数组 `{10, 30, 5, 20}`；用 F8 观察 `i` 和 `max`：当 `i = 1` 时 `max` 变成 `30`。

| 快捷键 | 今天的含义 |
|---|---|
| F7 | 进入当前调用的方法。 |
| F8 | 执行当前行，不进入新的方法。 |
| F9 | 继续到下一个断点或结束。 |

### 20:10–22:10：三道独立题（先写预期，再编码）

| 类名 | 题目 | 测试要求 |
|---|---|---|
| `ArchiveArrayDemo` | 创建 5 个保管年限，计算总和与平均值。 | 总和和平均值各输出一次。 |
| `ArchiveNoPrinter` | 写 `static void printArchiveNo(String no)`，输出“档案编号：…”。 | 调用两次，证明 `void` 不返回值。 |
| `MethodOverloadDemo` | 写 `printStatus(String status)` 与 `printStatus(String status, boolean canBorrow)`。 | 两个方法名相同、参数不同。 |

### 22:10–23:00：复盘、验收与 AI 提问

不看资料回答：数组索引范围是什么？参数和返回值分别是什么？`void` 表示什么？为什么最大值不能从 0 开始？重载的判断条件是什么？

卡住时先检查：数组是否为空、循环是否写成 `< length`、方法是否在 `main` 外、调用实参数量/类型是否和形参一致。再向 AI 提问：

```text
我在 Day 03 的 ArchiveTools.findMax 中调试。
输入数组：……；预期最大值：……；实际结果/第一条报错：……。
我已在 if 行看到 i=…、max=…。
请先解释数组索引和比较过程，再给最小修改建议。
```

## 今日完成清单

- [ ] 能画出数组索引 `0` 到 `length - 1`。
- [ ] `ArchiveTools` 三个方法各至少通过两组测试。
- [ ] 已用 F7 进入 `isValidArchiveNo`，看到形参和 `return`。
- [ ] 已完成三道独立题。
- [ ] 能说出参数、返回值、`void` 和重载的含义。

---

## 任务参考答案（完成后再查看）

```java
public class ArchiveArrayDemo {
    public static void main(String[] args) {
        int[] years = {5, 10, 30, 20, 15};
        int sum = 0;
        for (int i = 0; i < years.length; i++) {
            sum += years[i];
        }
        double average = (double) sum / years.length;
        System.out.println("总和：" + sum);
        System.out.println("平均值：" + average);
    }
}
```

```java
public class ArchiveNoPrinter {
    public static void printArchiveNo(String no) {
        System.out.println("档案编号：" + no);
    }
    public static void main(String[] args) {
        printArchiveNo("DA-2026-001");
        printArchiveNo("DA-2026-002");
    }
}
```

```java
public class MethodOverloadDemo {
    public static void printStatus(String status) {
        System.out.println("状态：" + status);
    }
    public static void printStatus(String status, boolean canBorrow) {
        System.out.println("状态：" + status + "，可借：" + canBorrow);
    }
    public static void main(String[] args) {
        printStatus("AVAILABLE");
        printStatus("BORROWED", false);
    }
}
```

**今日通过标准：** 你能不看答案解释 `findMax` 的循环，三道题均可运行；若做不到，明天开始前只重做这一份，不进入对象。
