是的，**遇到异常后立即跳到 catch**，try 中未执行的代码**不再执行**。

## 用项目代码举例

看你的 `Day07.java`：

```java
try {
    ArchiveValidator.validateArchiveNo("DA-2026-001");  // ① 正常执行
    boolean overdue = ArchiveValidator.isOverdue(yesterday);  // ② 正常执行
    System.out.println("是否逾期：" + overdue);  // ③ 正常执行

    ArchiveValidator.validateArchiveNo(" ");  // ④ 抛出异常！
} catch (IllegalArgumentException e) {
    System.out.println("输入错误：" + e.getMessage());  // ⑤ 跳转到这里
}
```

## 执行流程

| 步骤 | 代码 | 结果 |
|------|------|------|
| ① | `validateArchiveNo("DA-2026-001")` | ✅ 正常执行 |
| ② | `isOverdue(yesterday)` | ✅ 正常执行 |
| ③ | `System.out.println(...)` | ✅ 正常执行 |
| ④ | `validateArchiveNo(" ")` | ❌ 抛出异常 |
| ⑤ | `catch` 块 | ✅ 执行异常处理 |

## 关键点

```java
try {
    代码A;  // 执行
    代码B;  // 执行
    代码C;  // 抛出异常 → 立即停止
    代码D;  // ❌ 不执行
    代码E;  // ❌ 不执行
} catch (Exception e) {
    异常处理;  // 跳转到这里
}
```

## 项目中的实际效果

运行你的代码，输出：

```
是否逾期：true
输入错误：档案编号不能为空
```

- 第 ③ 行已执行，所以打印了"是否逾期：true"
- 第 ④ 行抛出异常，try 块结束
- 跳转到 catch，打印"输入错误：档案编号不能为空"

## 总结

> try 块中**一旦某行抛出异常**，该行**之后的所有代码都跳过**，直接进入 catch 块。