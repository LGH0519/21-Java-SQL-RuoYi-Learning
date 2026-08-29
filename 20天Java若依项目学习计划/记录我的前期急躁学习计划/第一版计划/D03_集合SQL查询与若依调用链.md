# Day 3：跟踪一次真实请求

## 今日成果

你能从页面/接口一路跟到 MyBatis SQL；能写筛选、排序、分页、统计和连接查询。

| 时间 | 知识点 | 学习与操作步骤 | 资源 | 验收 |
|---|---|---|---|---|
| 09:00–10:20 | 继承、接口、重写 | 只理解“接口定义能力，实现类完成能力”；写 `StatusChecker` 接口和 `ArchiveStatusChecker`。 | [Java 接口](https://www.runoob.com/java/java-interfaces.html) | 调用接口变量的方法，输出一条状态判断结果。 |
| 10:20–12:00 | List、Set、Map | 创建 10 个 Archive 放入 List；筛选“可借阅”；用 Set 去重编号；Map 按编号取对象。 | [Java 集合](https://www.runoob.com/java/java-collections.html) | 3 个集合小任务全部运行。 |
| 15:00–16:20 | DQL：WHERE、LIKE、IN、ORDER BY、LIMIT | 在 Day 2 表写 10 条查询，每条注释业务含义。 | [MySQL 查询](https://www.runoob.com/mysql/mysql-select-query.html) | SQL 文件有 10 条不同查询。 |
| 16:20–18:00 | COUNT、GROUP BY、LEFT JOIN | 添加 `archive_borrow` 表和 5 条借阅数据；查“每分类档案数”“未借出档案”。 | [MySQL 分组](https://www.runoob.com/mysql/mysql-group-by.html) | 2 条聚合、1 条左连接 SQL。 |
| 19:00–20:00 | 若依分层 | 任选系统已有“用户列表”或“字典列表”功能：IDEA 搜 Controller 名称，依次找到 Service、Mapper、XML。 | [若依项目结构](https://doc.ruoyi.vip/ruoyi/document/xmjs.html) | 在笔记写 4 层各做什么。 |
| 20:00–21:30 | 请求调试 | Controller 的列表方法打断点；Debug 启动后用页面点击查询。看请求对象、进入 Service、再看 Mapper 返回值。 | 调试手册第 3 节 | 保存 1 张断点截图，写参数值。 |
| 21:30–23:00 | SQL 验证与提交 | 从 Mapper XML 复制查询条件，先在 DBeaver 验证；比对页面行数。 | DBeaver | 页面、Apifox、DBeaver 三处结果一致。 |

## 必须验收

- [ ] 你可画出 Controller → Service → Mapper → MySQL。
- [ ] 能解释 F7 是“进入当前调用的方法”，F8 是“执行当前行但不进入”。
- [ ] 完成一次真实列表请求的断点跟踪。
