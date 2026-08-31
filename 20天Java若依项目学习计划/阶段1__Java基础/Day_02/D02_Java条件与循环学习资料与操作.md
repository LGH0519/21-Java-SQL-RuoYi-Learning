# Day 02：Java 条件、运算符与循环学习资料与操作

> 学习主题：让程序能够根据档案状态作出判断，并重复处理多条模拟数据。  
> 今日只使用 Day 01 已学过的变量、类型、输出和 IDEA Debug；**不学习数组、方法、对象、SQL 或若依源码。**

## 知识点速记（完成当天任务后用于复习）

### 1. 今天要解决的两类问题

Java 程序默认按从上到下的顺序执行。今天增加两种控制能力：

```text
条件为真吗？
  ├─ 是 → 执行某个分支（if / switch）
  └─ 否 → 执行另一个分支或跳过

还有任务没处理完吗？
  ├─ 是 → 再执行一次循环体（for / while）
  └─ 否 → 结束循环，继续后面的代码
```

- **条件分支**：在多种结果中选择一条路径，例如“档案是否可借”。
- **循环**：重复执行一段代码，例如“依次打印 10 个档案编号”。
- 条件表达式的结果必须是 `boolean`，即只能是 `true` 或 `false`；Java 不能像 C 语言那样写 `if (1)`。

### 2. 运算符：先算出值，再用于判断

| 类别 | 运算符 | 示例 | 结果/用途 |
|---|---|---|---|
| 算术 | `+ - * / %` | `10 % 3` | `1`；`%` 是取余。 |
| 比较 | `> < >= <= == !=` | `borrowDays > 7` | 得到 `true` 或 `false`。 |
| 逻辑 | `&&`、`||`、`!` | `available && hasPermission` | 组合或取反多个条件。 |
| 赋值 | `=`、`+=`、`-=` | `count += 1` | 把值保存回变量。 |
| 自增/自减 | `++`、`--` | `i++` | 让变量加 1 或减 1。 |
| 条件（三元） | `条件 ? 值1 : 值2` | `borrowed ? "已借出" : "在库"` | 根据条件选择一个值。 |

必须记住：

```java
int quotient = 10 / 3;       // 3：两个 int 相除，结果仍是 int
int remainder = 10 % 3;      // 1：取余
double average = 10.0 / 3;   // 3.333...：有一个操作数是 double

boolean canBorrow = true && false;  // false：两边都为 true 才为 true
boolean needAttention = false || true; // true：至少一边为 true 就为 true
boolean notBorrowed = !false;        // true：取反
```

`&&` 和 `||` 都是**短路运算符**：

- `false && 后续条件`：后续条件不会执行，因为整体必定为 `false`。
- `true || 后续条件`：后续条件不会执行，因为整体必定为 `true`。

今天写多个条件时，主动加括号，不靠记忆优先级：

```java
boolean overdue = (borrowDays > allowedDays) && (archiveStatus.equals("BORROWED"));
```

### 3. `String` 比较：使用 `equals`，不要使用 `==`

```java
String archiveStatus = "AVAILABLE";

boolean correct = "AVAILABLE".equals(archiveStatus); // true：比较文字内容
boolean risky = archiveStatus == "AVAILABLE";        // 不要这样写：比较的不是文字内容
```

- `==` 用于比较基本类型的值，例如 `score >= 60`、`count == 10`。
- `String` 是引用类型，比较文字内容使用 `equals`。
- 把确定不为 `null` 的常量写在前面：`"AVAILABLE".equals(archiveStatus)`。即使 `archiveStatus` 是 `null`，结果也是 `false`，不会报错。
- `archiveStatus.equals("AVAILABLE")` 在 `archiveStatus` 为 `null` 时会抛出空指针异常；异常的细节以后再学，今天只养成前一种安全写法。

> `equals(...)` 是一个方法调用。今天先会正确使用，Day 03 再系统学习“方法”。

### 4. `if / else if / else`：按条件选择唯一分支

```java
if (条件1) {
    // 条件1 为 true 时执行
} else if (条件2) {
    // 条件1 为 false 且条件2 为 true 时执行
} else {
    // 前面的条件都为 false 时执行
}
```

`if - else if - else` 从上到下判断，**第一个为 `true` 的分支执行后，整条链结束**。因此成绩判断必须从高分向低分写：

```java
if (score >= 90) {
    System.out.println("A");
} else if (score >= 80) {
    System.out.println("B");
} else {
    System.out.println("未达到 B");
}
```

两个独立的 `if` 不会互相排斥，可能会都执行；只有确实需要分别做两件事时才这样写。

```java
if (score >= 60) {
    System.out.println("及格");
}
if (score >= 90) {
    System.out.println("优秀");
}
```

### 5. `switch`：根据一个离散状态选择分支

当同一个变量只会对应多个固定值时，`switch` 比一长串 `else if` 更清楚。今天先掌握传统写法：

```java
switch (applicationStatus) {
    case "PENDING":
        System.out.println("待审批");
        break;
    case "APPROVED":
        System.out.println("审批通过");
        break;
    case "REJECTED":
        System.out.println("已驳回");
        break;
    default:
        System.out.println("未知状态");
        break;
}
```

- `case`：一个固定取值；今天使用 `String`。
- `break`：跳出整个 `switch`。传统写法遗漏它会发生“贯穿”：匹配一个 `case` 后继续执行后面的 `case`。
- `default`：没有任何 `case` 匹配时的兜底分支；业务状态处理通常应保留它。
- `switch` 适合固定状态；范围判断（如 `score >= 90`）、多个组合条件（如“有权限且在库”）用 `if`。

JDK 17 代码中还可能看到箭头写法；今天**只需认识，不要求使用**：

```java
switch (applicationStatus) {
    case "APPROVED" -> System.out.println("审批通过");
    default -> System.out.println("未知状态");
}
```

箭头写法不会向下贯穿；不要把它和今天的冒号写法混在同一个 `case` 中。

### 6. `for` 循环：次数已知时的首选

```java
for (初始化; 循环条件; 更新) {
    // 循环体
}
```

```java
for (int archiveNumber = 1; archiveNumber <= 10; archiveNumber++) {
    System.out.println("DA-2026-" + archiveNumber);
}
```

执行顺序是：**初始化一次 → 判断条件 → 执行循环体 → 更新 → 再判断条件**。当 `archiveNumber` 变成 `11` 时，`archiveNumber <= 10` 为 `false`，循环结束。

- `int archiveNumber = 1`：从 1 开始。
- `archiveNumber <= 10`：只要条件为真就继续。
- `archiveNumber++`：每轮结束后加 1。
- 在 `for (...)` 内声明的 `archiveNumber` 只在这个循环中可用。

### 7. `while` 与 `do...while`：次数不确定时更自然

```java
int remainingCopies = 3;
while (remainingCopies > 0) {
    System.out.println("处理一份档案");
    remainingCopies--;
}
```

- `while` 先判断条件，再执行；如果第一次就是 `false`，一次也不执行。
- 循环体内必须让条件有机会改变。上例若忘写 `remainingCopies--`，就可能成为死循环。

```java
int attempts = 0;
do {
    attempts++;
    System.out.println("至少执行一次");
} while (attempts < 1);
```

- `do...while` 先执行一次再判断，所以至少执行一次。
- `while (条件);` 末尾的英文分号不能漏。

### 8. `break` 与 `continue`

```java
for (int day = 1; day <= 10; day++) {
    if (day == 5) {
        continue; // 跳过第 5 天，立刻开始下一轮
    }
    if (day == 8) {
        break;    // 彻底结束这个循环
    }
    System.out.println("检查第 " + day + " 天");
}
```

- `continue`：跳过**当前这一轮**剩余代码，循环还会继续。
- `break`：结束**最内层**循环或 `switch`，执行其后的代码。
- 业务代码中不要为了少写一行而滥用它们；先把普通 `if` 和循环写清楚。

### 9. Day 02 重点、易混点、易面试点

| 类型 | 必须回答的问题 | 正确答案要点 |
|---|---|---|
| 重点 | `if` 与 `switch` 怎么选？ | 范围、大小比较、多个条件组合用 `if`；同一变量的固定状态值用 `switch`。 |
| 重点 | `for` 与 `while` 怎么选？ | 次数/范围明确常用 `for`；结束条件依赖运行中状态常用 `while`。 |
| 易混 | `=` 和 `==` 区别？ | `=` 是赋值；`==` 是比较。条件中通常要写比较表达式。 |
| 易混 | `==` 能比较 `String` 吗？ | 语法允许，但比较的是引用是否相同；比较文本内容用 `equals`。 |
| 易混 | 为什么 `10 / 3` 是 3？ | 两个操作数都是 `int`，执行整数除法；写 `10.0 / 3` 才会得到小数结果。 |
| 易混 | `&&` 与 `||` 区别？ | `&&` 要全部条件为真；`||` 只要有一个条件为真。 |
| 易混 | `break` 与 `continue` 区别？ | `break` 结束整个当前循环/`switch`；`continue` 只跳过当前一轮。 |
| 面试入门 | `else if` 为什么按从高到低判断成绩？ | 该链命中第一个 `true` 就结束；从低到高会让高分先落入低等级。 |
| 面试入门 | 什么是死循环？ | 循环条件一直为 `true` 或无法变为 `false`，程序无法正常结束循环。 |

## 视频与文档重点整理（完成当天学习后复习）

本节把当天外部资料中需要真正带走的结论提前整理好。看视频时只对照这些点记笔记，不追求记住每一个语法细节。

### 1. 指定资源与阅读范围

| 顺序 | 资源 | 只学习的内容 | 完成动作 |
|---:|---|---|---|
| 1 | [Java 零基础视频（B 站）](https://www.bilibili.com/video/BV1Ho4y1f7Gd/) | 在选集标题中依次找“运算符”“流程控制 / if”“switch”“for / while / do while”“break / continue”。 | 每个选集看完暂停，写出一个语法模板和一个档案业务例子。 |
| 2 | [Java 运算符](https://www.runoob.com/java/java-operators.html) | 算术、比较、逻辑、赋值、自增、自减、三元运算符。 | 自己算出 `10 / 3`、`10 % 3`、`true && false`。 |
| 3 | [Java 条件语句](https://www.runoob.com/java/java-if-else-switch.html) | `if`、`if...else`、`if...else if...else`、嵌套条件。 | 写一条成绩等级判断。 |
| 4 | [Java switch case](https://www.runoob.com/java/java-switch-case.html) | `case`、`break`、`default` 和遗漏 `break` 的后果。 | 用借阅申请状态写一个 `switch`。 |
| 5 | [Java 循环结构](https://www.runoob.com/java/java-loop.html) | `for`、`while`、`do...while`、`break`、`continue`。 | 手写 1 到 100 的累加。 |

**今天不看：** 数组、增强 `for`、方法、面向对象、输入 `Scanner`、网络、多线程。视频里出现这些标题时直接跳过；它们会在后续学习日集中处理。

### 2. 你看视频时应形成的业务映射

| Java 结构 | 档案系统中的最小例子 |
|---|---|
| `if` | 档案状态为 `AVAILABLE`、有库存且用户有权限，才允许申请借阅。 |
| `switch` | 借阅申请为 `PENDING`、`APPROVED`、`REJECTED` 时显示不同文字。 |
| `for` | 输出第 1 到第 10 条模拟档案编号。 |
| `while` | 只要还有待处理的归还登记，就继续处理。 |
| `%` | 用 `year % 4` 等条件参与闰年判断。 |
| `&&` | “在库”**并且**“有权限”才可借。 |
| `||` | 分数小于 0 **或**大于 100 都是无效输入。 |

## 今天只完成什么

完成后，你能不看资料解释条件和循环的执行顺序，能手写 7 个可运行的 Java 类，并能在 IDEA 中看见条件结果和循环变量如何变化。

### 开始前检查（09:00–09:20）

1. 打开 IDEA，打开 Day 01 创建的项目：`E:\WorkSpace\java-learning`。
2. 确认左侧有 `src`，且 **File → Project Structure → Project SDK** 显示 17。
3. 不修改 `E:\WorkSpace\ruoyi-archive-management` 里的任何文件。
4. 在 `src` 上右键 → **New → Java Class**；今天所有类都直接建在 `src` 下，暂不建包。
5. 新建本地笔记文件 `D02-notes.md` 或纸质笔记，写下本文件第 9 节的 9 个问答标题；看完后自己补答案。

## 1. 上午：理解运算符、条件与循环（09:20–12:00）

### 09:20–10:20：运算符和布尔条件

1. 按“指定资源与阅读范围”的顺序，观看视频中的“运算符”选集。
2. 阅读“Java 运算符”页面，只看表中列出的 6 类；不要抄完整表格。
3. 在纸上写出以下结果，再到 IDEA 验证：

```java
System.out.println(10 / 3);
System.out.println(10 % 3);
System.out.println(8 >= 8);
System.out.println((true && false) || true);
```

4. 写一句自己的解释：`=` 是“把右侧结果放进左侧变量”，`==` 是“比较两侧是否相等”。

**验收：** 能正确说出四行依次输出 `3`、`1`、`true`、`true`。

### 10:20–11:10：`if / else if / else`

1. 观看“流程控制 / if”选集，阅读“Java 条件语句”页面。
2. 在笔记上抄写一次如下模板，并用中文标出每一行的作用：

```java
if (condition) {
    // 条件为 true
} else {
    // 条件为 false
}
```

3. 把 `score = 95` 代入“从高到低”的成绩代码，手工追踪会进入哪个分支。
4. 把条件顺序故意改为 `score >= 60` 在前，说明为什么 95 会被错判。

**验收：** 能说明 `else if` 链只会执行其中一个分支。

### 11:10–12:00：`switch`、`for`、`while`

1. 观看“switch”和“for / while / do while”选集；阅读对应两个网页。
2. 对照本文件第 5–8 节，分别写出 `switch`、`for`、`while` 的空模板。
3. 在笔记中回答：`default` 的作用是什么？`break` 少写会怎样？`while` 中什么操作避免死循环？

**验收：** 不看资料写出 `for (int i = 1; i <= 10; i++)`，并能解释三段的含义。

## 2. 下午：按步骤写出状态判断与循环（15:00–18:00）

### 15:00–16:20：编码练习一——档案是否可借

1. 在 `src` 上右键 → **New → Java Class** → 输入 `ArchiveStatusDemo` → 回车。
2. 将以下代码先**手敲**进去；不要直接复制。每敲完一个变量，停下来读中文注释。
3. 点击类左侧绿色三角 → **Run 'ArchiveStatusDemo.main()'**。
4. 将 `archiveStatus` 改成 `"BORROWED"`、`remainingCopies` 改成 `0`、`hasBorrowPermission` 改成 `false`，每次只改一个变量再运行，记录输出为什么变化。

```java
public class ArchiveStatusDemo {
    public static void main(String[] args) {
        String archiveStatus = "AVAILABLE";
        int remainingCopies = 2;
        boolean hasBorrowPermission = true;

        boolean canBorrow = "AVAILABLE".equals(archiveStatus)
                && remainingCopies > 0
                && hasBorrowPermission;

        if (canBorrow) {
            System.out.println("可提交借阅申请");
        } else {
            System.out.println("当前不可借阅");
        }
    }
}
```

**验收：** 初始值输出“可提交借阅申请”；三个条件中任意一个不满足时输出“当前不可借阅”。

### 16:20–17:10：编码练习二——用 `switch` 显示审批状态

1. 新建 `BorrowStatusSwitchDemo`。
2. 不看答案，先按第 5 节模板完成 `PENDING`、`APPROVED`、`REJECTED`、`default` 四个分支。
3. 在 `PENDING` 分支临时删除 `break` 后运行一次，观察多输出了什么；立刻恢复 `break`。
4. 分别把 `applicationStatus` 改为 `"APPROVED"`、`"REJECTED"`、`"CANCELED"` 后运行。

**验收：** 未知状态 `CANCELED` 会进入 `default`，而不是静默不输出。

### 17:10–18:00：编码练习三——用 `for` 生成 10 条模拟编号

1. 新建 `ArchiveNumberLoopDemo`。
2. 写一个从 1 到 10 的 `for` 循环；每轮输出一行 `DA-2026-数字`。
3. 把循环条件故意改成 `archiveNumber < 10`，对比少了哪一条；再改回 `<= 10`。
4. 再写一个 `while` 循环，从 `remainingTasks = 3` 倒数到 1 并输出“剩余待处理：x”。

**验收：** 控制台恰好打印 10 个编号，并且 `while` 打印 3、2、1 后结束。

## 3. 晚上：巩固、Debug 与综合练习（19:00–23:00）

### 19:00–19:50：第一次看“条件如何走分支”

使用 `ArchiveStatusDemo.java`：

1. 在 `if (canBorrow) {` 这一行行号左侧单击，出现红色圆点，即断点。
2. 点击绿色小虫，或按 **Shift + F9** 以 Debug 方式启动。
3. 程序暂停后，在底部 **Debug** 面板的 Variables 区确认：
   - `archiveStatus` 是什么；
   - `remainingCopies` 是多少；
   - `hasBorrowPermission` 是什么；
   - `canBorrow` 最终是 `true` 还是 `false`。
4. 按 **F8** 执行当前行，观察黄色/蓝色执行位置进入哪个大括号。
5. 修改 `archiveStatus = "BORROWED"`，重复 1–4；确认执行位置进入 `else`。
6. 按 **F9** 结束运行，再单击红点移除断点。

**验收截图：** 断点停在 `if` 行，Variables 面板同时显示 4 个变量及其值。

### 19:50–20:30：第二次 Debug——看循环变量变化

使用 `ArchiveNumberLoopDemo.java`：

1. 在 `for` 循环体的第一条 `System.out.println` 行打断点。
2. 用 Debug 启动。第一次暂停时记录 `archiveNumber = 1`。
3. 连按 **F8**，观察输出一行后，循环如何更新 `archiveNumber`。
4. 再次暂停时确认 `archiveNumber = 2`；说明“循环体 → 更新 → 再判断”的顺序。
5. 按 **F9** 让其余循环执行完毕，移除断点。

> 若断点只停一次，检查红点是否打在循环体内，而不是循环结束后的行；无需设置复杂的断点条件。

### 20:30–22:20：四个综合小题（先写测试，再写代码）

每题都执行同一流程：先在纸上写至少 3 组“输入 → 预期输出”，再新建类、写代码、运行、核对。本节**不使用数组**。

| 题目 | 类名 | 必做要求 | 最少测试组 |
|---|---|---|---|
| 1. 闰年判断 | `LeapYearDemo` | 年份可被 400 整除，或可被 4 整除但不能被 100 整除，才是闰年。 | `2024`、`1900`、`2000`。 |
| 2. 成绩等级 | `GradeDemo` | 先判断 0–100 合法性；90+ A，80+ B，60+ C，其余 D。 | `100`、`89`、`60`、`-1`。 |
| 3. 1–100 求和 | `SumOneToHundredDemo` | 用 `for` 循环累计，不能直接写 `5050`。 | 输出应为 `5050`。 |
| 4. 逾期提醒扫描 | `OverdueReminderDemo` | 用 `for` 检查借阅第 1–10 天；超过第 7 天才打印提醒，并统计提醒次数。 | 预期打印第 8、9、10 天，共 3 次。 |

**验收：** 四题都独立运行；每题在代码顶部注释写出你的测试组。完成后再看本文末尾答案。

### 22:20–23:00：收尾复盘与 AI 使用边界

不再看视频，完成下面 8 个问题：

1. `=` 与 `==` 分别是什么？
2. 为什么比较 `String` 内容应该用 `equals`？
3. `if` 与 `switch` 的适用场景分别是什么？
4. `10 / 3` 与 `10.0 / 3` 的结果为何不同？
5. `for` 三个部分的执行顺序是什么？
6. `while` 为什么容易写成死循环？
7. `break` 与 `continue` 的区别是什么？
8. 结合档案系统，用 100–200 字解释“从档案状态到是否允许借阅”的判断过程。

只有在你已经完成“读第一条报错、检查类名和分号、用 Debug 看变量”后，才可向 AI 求助。提问模板：

```text
我在 E:\WorkSpace\java-learning 的 ArchiveStatusDemo 中遇到问题。
我的目标：当状态为 AVAILABLE、库存大于 0 且有权限时输出“可提交借阅申请”。
实际输出/报错：……
我已检查：第一条报错、类名、分号，并在 if 行 Debug 看到 canBorrow = ……
请先解释条件为什么是这个结果，再只给我最小修改建议，不要重写整个程序。
```

## 今日完成清单

- [ ] 已看指定的运算符、条件、`switch`、循环视频选集，跳过数组和方法。
- [ ] 已读 4 个指定网页，并能写出 `if`、`switch`、`for`、`while` 模板。
- [ ] `ArchiveStatusDemo.java` 在 4 组条件下输出正确。
- [ ] `BorrowStatusSwitchDemo.java` 正确处理 3 个状态和未知状态。
- [ ] `ArchiveNumberLoopDemo.java` 输出 10 个编号，且 `while` 正常结束。
- [ ] 已完成两次 Debug：看到 `canBorrow` 和 `archiveNumber` 的变化。
- [ ] 四个综合题都已运行并与预期结果核对。
- [ ] 已完成 8 个复盘问题，能解释重点、易混点、易面试点。

## 卡住时只按这个顺序检查

1. 读 IDEA 显示的**第一条**报错，找到红色波浪线所在行。
2. 类名是否等于文件名，例如 `GradeDemo` 与 `GradeDemo.java`。
3. `if`、`while`、`for` 后的圆括号和大括号是否成对；语句末尾是否有英文分号。
4. 条件是否产生 `boolean`：例如写 `score >= 60`，不是只写 `score`。
5. 是否把 `=` 误写成 `==`，或把字符串内容比较误写为 `==`。
6. 循环变量是否初始化、会更新、终止条件能变为 `false`。
7. 在条件行或循环体打断点，用 Debug 看真实变量值，而不是猜测。

仍无法解决时，保留代码和第一条报错，按上面的 AI 提问模板描述；不要直接把整段代码替换掉。

---

## 任务参考答案（完成后再查看）

### 1. 编码练习二：`BorrowStatusSwitchDemo`

```java
public class BorrowStatusSwitchDemo {
    public static void main(String[] args) {
        String applicationStatus = "PENDING";

        switch (applicationStatus) {
            case "PENDING":
                System.out.println("待审批");
                break;
            case "APPROVED":
                System.out.println("审批通过");
                break;
            case "REJECTED":
                System.out.println("已驳回");
                break;
            default:
                System.out.println("未知申请状态");
                break;
        }
    }
}
```

`applicationStatus = "PENDING"` 时输出“待审批”；改成 `"CANCELED"` 时输出“未知申请状态”。如果删除第一个 `break`，当状态为 `PENDING` 时还会继续输出“审批通过”和“已驳回”，这就是贯穿。

### 2. 编码练习三：`ArchiveNumberLoopDemo`

```java
public class ArchiveNumberLoopDemo {
    public static void main(String[] args) {
        for (int archiveNumber = 1; archiveNumber <= 10; archiveNumber++) {
            System.out.println("DA-2026-" + archiveNumber);
        }

        int remainingTasks = 3;
        while (remainingTasks > 0) {
            System.out.println("剩余待处理：" + remainingTasks);
            remainingTasks--;
        }
    }
}
```

### 3. 综合题一：`LeapYearDemo`

```java
public class LeapYearDemo {
    public static void main(String[] args) {
        int year = 2024;
        boolean leapYear = (year % 400 == 0)
                || (year % 4 == 0 && year % 100 != 0);

        if (leapYear) {
            System.out.println(year + " 是闰年");
        } else {
            System.out.println(year + " 不是闰年");
        }
    }
}
```

| 年份 | 结果 | 原因 |
|---:|---|---|
| 2024 | 闰年 | 能被 4 整除，不能被 100 整除。 |
| 1900 | 不是闰年 | 能被 100 整除，但不能被 400 整除。 |
| 2000 | 闰年 | 能被 400 整除。 |

### 4. 综合题二：`GradeDemo`

```java
public class GradeDemo {
    public static void main(String[] args) {
        int score = 86;

        if (score < 0 || score > 100) {
            System.out.println("成绩无效");
        } else if (score >= 90) {
            System.out.println("A");
        } else if (score >= 80) {
            System.out.println("B");
        } else if (score >= 60) {
            System.out.println("C");
        } else {
            System.out.println("D");
        }
    }
}
```

### 5. 综合题三：`SumOneToHundredDemo`

```java
public class SumOneToHundredDemo {
    public static void main(String[] args) {
        int sum = 0;

        for (int number = 1; number <= 100; number++) {
            sum += number;
        }

        System.out.println("1 到 100 的和：" + sum);
    }
}
```

输出应为：

```text
1 到 100 的和：5050
```

### 6. 综合题四：`OverdueReminderDemo`

```java
public class OverdueReminderDemo {
    public static void main(String[] args) {
        int reminderCount = 0;

        for (int borrowDay = 1; borrowDay <= 10; borrowDay++) {
            if (borrowDay <= 7) {
                continue;
            }

            reminderCount++;
            System.out.println("第 " + borrowDay + " 天：该档案已逾期，请提醒归还");
        }

        System.out.println("逾期提醒次数：" + reminderCount);
    }
}
```

预期末尾输出：

```text
第 8 天：该档案已逾期，请提醒归还
第 9 天：该档案已逾期，请提醒归还
第 10 天：该档案已逾期，请提醒归还
逾期提醒次数：3
```

### 7. 复盘题参考答案

**`=` 与 `==` 分别是什么？**  
`=` 是赋值，把右侧结果保存到左侧变量；`==` 是比较。基本类型使用 `==` 比较值。

**为什么比较 `String` 内容应该用 `equals`？**  
`String` 是引用类型，`==` 比较的是引用是否相同；`equals` 比较文本内容。推荐写 `"AVAILABLE".equals(status)`，可避免变量为 `null` 时出错。

**`if` 与 `switch` 的适用场景分别是什么？**  
`if` 适合范围、大小比较和条件组合；`switch` 适合同一个变量的固定状态分支。

**`10 / 3` 与 `10.0 / 3` 的结果为何不同？**  
前者两个操作数都是 `int`，进行整数除法，结果是 `3`；后者含 `double`，结果带小数。

**`for` 三个部分的执行顺序是什么？**  
初始化一次 → 判断条件 → 执行循环体 → 更新 → 回到判断条件。

**`while` 为什么容易写成死循环？**  
如果条件始终为 `true`，或循环体没有改变与条件相关的变量，循环就没有结束机会。

**`break` 与 `continue` 的区别是什么？**  
`break` 结束当前最内层循环或 `switch`；`continue` 只跳过当前一轮剩余语句，下一轮仍会进行。

**档案能否借阅的判断过程是什么？**  
先用变量保存档案状态、剩余数量和用户权限；再用 `&&` 把三个条件组合为 `canBorrow`。只有状态文字等于 `AVAILABLE`、数量大于 0 且用户有权限时，`canBorrow` 才为 `true`，程序进入 `if` 输出“可提交借阅申请”；任意一个条件不满足则进入 `else`，输出“当前不可借阅”。

### 8. 今日验收的正确状态

- `ArchiveStatusDemo.java` 的初始值输出“可提交借阅申请”；任意条件不满足输出“当前不可借阅”。
- `BorrowStatusSwitchDemo.java` 有 `PENDING`、`APPROVED`、`REJECTED` 和 `default` 四种处理，传统 `case` 都有 `break`。
- `ArchiveNumberLoopDemo.java` 输出 `DA-2026-1` 到 `DA-2026-10` 共 10 行，并能正常结束 `while`。
- `LeapYearDemo`、`GradeDemo`、`SumOneToHundredDemo`、`OverdueReminderDemo` 都独立运行并通过题中测试组。
- Debug 时，你能指出 `canBorrow` 为什么为 `true/false`，以及 `archiveNumber` 为何从 1 递增到 10。
