# Day 2：把业务概念写成类和表

## 今日成果

手写 `Archive` Java 类；能不用代码生成器创建档案分类和档案两张表，并理解字段/主键/唯一约束。

| 时间 | 知识点 | 学习与操作步骤 | 资源 | 验收 |
|---|---|---|---|---|
| 09:00–10:20 | `if/else`、`switch`、比较和逻辑运算符 | 视频定位“条件语句、switch”；分别写成绩等级、闰年、档案状态文字转换。每题先写 3 组输入输出再编码。 | [Java 条件语句](https://www.runoob.com/java/java-if-else.html) | 3 题、每题 3 组测试。 |
| 10:20–12:00 | `for/while`、数组 | 看“循环、数组”标题；写数组求和、最大值、查询指定编号；用纸写出循环变量每次变化。 | [Java 循环](https://www.runoob.com/java/java-loop.html) | 3 个数组题，不能越界。 |
| 15:00–16:30 | 方法、参数、返回值、重载 | 看“方法”标题；从 Day 1 程序中抽取 `calculateAverage`、`isValidArchiveNo`、`findMax`。 | [Java 方法](https://www.runoob.com/java/java-methods.html) | 3 个方法均有参数、返回值，主方法调用它们。 |
| 16:30–18:00 | 类、对象、封装、构造方法 | 看“类与对象、构造方法、封装”；创建 `Archive`：编号、名称、分类 ID、状态、保管期限、创建日期。字段设 `private`，补 getter/setter。 | [对象和类](https://www.runoob.com/java/java-object-classes.html) | `new Archive(...)` 后打印对象关键字段。 |
| 19:00–20:20 | MySQL DDL：库/表/类型/主键/非空/唯一 | 看 MySQL 视频第 12–18 集；先用纸列字段，再执行 SQL。建 `archive_category`、`archive_record`。 | [创建表](https://www.runoob.com/mysql/mysql-create-tables.html) | 两表存在；`archive_no` 有唯一约束。 |
| 20:20–21:40 | DML：插入、更新、删除 | 为分类插 5 条、档案插 10 条；故意重复插入编号，观察报错；修改一个状态，删除一条测试记录。 | [插入数据](https://www.runoob.com/mysql/mysql-insert-query.html) | `day02.sql` 含 20 条以上 SQL。 |
| 21:40–23:00 | 调试“对象数据从何而来” | 在 `main` 中 `new Archive` 那一行打断点；展开变量看每个字段；用 Alt+F8 执行 `archive.getArchiveNo()`。 | 调试手册 | 写下“对象是内存中的数据，表是数据库中的数据”。 |

## 必须验收

- [ ] `Archive` 类 6 个私有字段、构造方法、getter/setter。
- [ ] 两张表不是从若依生成器得到的，而是自己写 SQL 建成。
- [ ] 明确说出主键与唯一约束的差别：主键唯一且不能为空；唯一约束用于业务字段也可限制重复。
