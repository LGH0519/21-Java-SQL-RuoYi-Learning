# Day 06：SQL 查询、连接、聚合与分页

## 知识速记

`SELECT` 读取数据；`WHERE` 筛选；`ORDER BY` 排序；`LIMIT` 分页；`JOIN` 关联多张表；`GROUP BY` 分组聚合。SQL 写完不能只看“无报错”，必须核对结果行是否符合业务。

## 资料

- [MySQL SELECT](https://www.runoob.com/mysql/mysql-select.html)
- [MySQL WHERE](https://www.runoob.com/mysql/mysql-where.html)
- [MySQL JOIN](https://www.runoob.com/mysql/mysql-join.html)
- [MySQL GROUP BY](https://www.runoob.com/mysql/mysql-group-by.html)

## 知识详解

查询顺序应读作：从哪张表取 (`FROM`) → 哪些行 (`WHERE`) → 如何分组 (`GROUP BY`) → 如何排序 (`ORDER BY`) → 取几行 (`LIMIT`)。`LEFT JOIN` 保留左表所有行，适合“找没有申请过借阅的档案”；连接条件必须是相关键，漏写会产生笛卡尔积。

分页不是前端切数组，而是后端/数据库按页返回；若依 `startPage()` 与响应 `rows/total` 是页面分页链的一部分。

## 今天照做（09:00–23:00）

### 09:00–12:00

1. 阅读资料，写出一条“按档案名模糊查、按创建时间倒序、每页 10 条”的 SQL 骨架。
2. 给学习库插入非敏感测试数据；可由 AI 生成 INSERT，人工先确认目标库和数据量。
3. 在 DBeaver 依次验证全部、按状态、LIKE、排序、LIMIT 查询。

### 15:00–18:00

1. 用 AI 生成 10 条档案查询测试题和预期结果，不让它直接声称结果正确。
2. 自己在 DBeaver 执行并记录每条的实际行数。
3. 打开 `SysDictTypeMapper.xml` 的 `selectDictTypeList`，把 `dictName/status` 的 `<if>` 翻译为普通 SQL。

### 19:00–23:00

1. 在页面字典类型列表填写一个查询条件，Network 记录参数；对比 XML 里对应 `<if>`。
2. 写一条 `archive_record` 与 `archive_category` 的 JOIN，验证分类名称如何显示。
3. 写一条 `COUNT(*) GROUP BY status`，解释每一行统计的含义。

## AI 操作卡

给 AI 表结构和目标查询，要求它给 SQL、参数含义、预期列和边界用例。执行前人工检查表名、条件和 LIMIT；绝不把 AI 的 DELETE/UPDATE 混入查询脚本。

## 验收

- [ ] 能写并验证 WHERE、LIKE、ORDER BY、LIMIT。
- [ ] 能解释 JOIN 条件和 GROUP BY 统计。
- [ ] 能将一段 Mapper XML 动态条件翻译为 SQL。

## 文末答案与自测

**为什么 SQL 需要 `ORDER BY`？** 没有排序时返回顺序不应被业务依赖。  
**通过标准：** 能从页面查询条件追到 Network 参数、XML 条件和 DBeaver 结果。
