# D12｜SELECT、条件、排序与分页

> 今日目标：能从页面筛选条件写出 SQL，并把若依字典类型 XML 中的动态条件翻译成人能执行的普通 SQL。

## 1. 知识速记

- `SELECT` 查询；`WHERE` 筛选；`ORDER BY` 排序；`LIMIT offset, size` 分页。
- 比较：`=`、`<>`；范围：`BETWEEN ... AND ...`；集合：`IN (...)`；空值：`IS NULL`；模糊：`LIKE '%关键词%'`。
- SQL 常见逻辑顺序：`WHERE` → `ORDER BY` → `LIMIT`；书写顺序是 SELECT、FROM、WHERE、ORDER BY、LIMIT。
- MyBatis `<if test="...">` 只在条件有值时拼接一段 SQL；`#{dictName}` 是安全参数占位。

## 2. 资料

- [菜鸟教程：SELECT](https://www.runoob.com/mysql/mysql-select-query.html)
- [菜鸟教程：WHERE](https://www.runoob.com/mysql/mysql-where-clause.html)
- [菜鸟教程：LIKE](https://www.runoob.com/mysql/mysql-like-clause.html)
- [菜鸟教程：ORDER BY](https://www.runoob.com/mysql/mysql-order-by.html)

## 3. 知识详解

`LIKE '%合同%'` 是“任意位置包含合同”；`LIKE '合同%'` 是“以合同开头”，二者不能混用。`WHERE status = '0' AND retention_years >= 10` 要同时满足；`OR` 只要一边满足，括号决定组合范围。`LIMIT 0, 10` 取第 1 页，`LIMIT 10, 10` 取第 2 页（每页 10 条）。

易混点：`= NULL` 永远不是判断空的正确写法，必须 `IS NULL`。表没有唯一排序时分页可能不稳定，应使用例如 `ORDER BY id DESC`。面试点：SQL 注入风险主要来自字符串拼接；若依 XML 一般用 `#{}` 参数绑定，不能把用户输入直接以 `${}` 拼入查询。

## 4. 今天照做

### 09:00–12:00｜建立查询脚本

1. 新建并另存 DBeaver 脚本为 `day12_select.sql`；第一行写 `USE archive_learning;`。
2. 依次阅读资料，只练上面的六类条件。每看完一类，在 SQL 文件写一条对应档案查询。
3. 先执行全表基线：

```sql
SELECT id, archive_no, archive_name, category_id, status, retention_years, create_time
FROM archive_record
ORDER BY id;
```

### 15:00–18:00｜完成 12 条可解释查询

1. 逐条写并执行下列类型的查询，保留每条业务注释：名称模糊、指定分类、在库状态、保管期限范围、创建日期范围、物理位置非空、分类 IN、状态 OR、倒序、第一页、第二页、组合条件。
2. 可直接采用并改造以下例子：

```sql
-- 1. 名称包含“合同”的档案
SELECT * FROM archive_record WHERE archive_name LIKE '%合同%';
-- 2. 在库且保管不少于 10 年，按创建时间倒序
SELECT * FROM archive_record
WHERE status = '0' AND retention_years >= 10
ORDER BY create_time DESC, id DESC;
-- 3. 每页 3 条的第 2 页
SELECT * FROM archive_record ORDER BY id ASC LIMIT 3, 3;
-- 4. 没填写实体位置的记录
SELECT * FROM archive_record WHERE physical_location IS NULL;
```

3. 对每条结果写一句解释：“条件是什么、预期几行、实际几行”。没有匹配结果也可接受，只要你能解释为什么。

### 19:00–23:00｜把 MyBatis 动态 SQL 变成普通 SQL

1. IDEA 打开：`E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-system\src\main\resources\mapper\system\SysDictTypeMapper.xml`。
2. 使用 `Ctrl+F` 搜索 `selectDictTypeList`；只读这个 `<select>` 与它引用的 `<sql id="selectDictTypeVo">`。
3. 找到名称、状态、类型对应的 `<if>`。在笔记写：当 `dictName = '用户'` 且 `status = '0'` 时，名称条件和状态条件会被加入；没有传 `dictType` 时，类型条件不加入。
4. 在 DBeaver 的若依项目数据库（不是 `archive_learning`）新建只读脚本，按 XML 结构写普通 SQL。表名以 XML 实际 FROM 为准；只执行 SELECT，不更新任何 `sys_` 表。
5. 在字典管理页面输入相同条件，F12 → Network 看请求参数；再对比 DBeaver 结果数量。参数/数据差异优先检查页面条件、XML `<if>`、DBeaver 是否连接到同一数据库。

## 5. AI Agent 操作卡

输入：一条 SQL、期望筛选规则、实际/预期结果。要求 AI **解释** WHERE、排序与 LIMIT 是否吻合，并给 3 个边界测试，不让它改数据库。你自己在 DBeaver 逐条执行边界测试。

## 6. 验收与文末答案

- [ ] `day12_select.sql` 有 12 条带注释查询。
- [ ] 至少包含 LIKE、IN、IS NULL、范围、排序、两页 LIMIT。
- [ ] 能将 `selectDictTypeList` 的一组条件翻译为普通 SQL。
- [ ] 能解释 `LIMIT 3, 3` 的含义。

答案：`WHERE x = NULL` 为什么不对？NULL 表示未知，比较结果也未知，需 `IS NULL`。页面筛选无效先看哪里？先看 Network 参数，再看 Mapper XML 的 `<if>`，最后用同等 SQL 查数据库。
