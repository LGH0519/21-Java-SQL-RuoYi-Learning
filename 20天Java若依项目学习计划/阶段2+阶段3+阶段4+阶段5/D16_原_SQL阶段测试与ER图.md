# Day 16：SQL 阶段测试、ER 图与 MyBatis XML 对照

> 今日目标：闭卷完成 SQL 六项自测，留下第一版学习 ER 图与数据字典；把一条若依 XML 动态查询翻译并验证为 SQL。

## 知识点速记（完成当天任务后复习）

- SQL 基础链：DDL 建结构 → DML 准备数据 → DQL 查询验证 → 事务保证多步一致 → 索引服务高频查询。
- ER 图描述实体、主键、关联和基数；数据字典描述每个字段的业务含义和约束。
- MyBatis XML 是 Java 方法到 SQL 的映射层；动态 `<if>` 根据参数决定是否拼条件。

## 视频、文档与若依对应点

- 本周 D10–D15 的脚本与截图；今天先不看新视频。
- `E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-system\src\main\resources\mapper\system\SysDictTypeMapper.xml`
- [MyBatis 官方动态 SQL 文档](https://mybatis.org/mybatis-3/dynamic-sql.html)：只看 `if` 和 `where` 概念。

## 本日重点、易混点与若依对应

SQL 学会不是记住关键字，而是能把业务问题变成可验证的语句，并用结果检查假设。ER 图不是数据库自动生成图的截图：必须能回答“这条线两端是什么、哪个字段关联、基数是什么”。数据字典是后续 AI/代码生成器可读的设计输入，至少含字段名、类型、非空、默认值、说明、索引/约束。

## 今天照做（09:00–23:00）

### 09:00–12:00｜六项闭卷自测

1. 新建 `day16_sql_exam.sql`，只写题目，不翻 D10–D15 笔记。
2. 完成六项：
   1. DDL：用一条 `CREATE TABLE` 建最小测试表；
   2. DML：插入、更新、删除各一次（删除测试数据）；
   3. 条件查询：LIKE、范围、排序、分页；
   4. 连接/分组：每分类档案数、未申请过档案；
   5. 事务：一条申请和一条状态更新后回滚；
   6. 索引：说明 `archive_no` 唯一约束和一个关联索引用途。
3. 每项完成后在末尾写“自评分：会/需查资料”。至少 5 项会才进入下午设计；否则先回补对应旧脚本再重做。

### 15:00–18:00｜第一版 ER 图与数据字典

1. 用 draw.io、纸笔或 DBeaver 建一个图，至少包含：`archive_category`、`archive_record`、`archive_borrow_application`；标出主键和 `category_id/archive_id`。
2. 建 `data_dictionary_draft.md`，按如下表填完三张学习表：

| 表 | 字段 | 类型 | 可空 | 约束/索引 | 业务说明 |
|---|---|---|---|---|---|
| archive_record | archive_no | VARCHAR(64) | 否 | UNIQUE | 对外可见的档案编号 |

3. 写出两个基数说明：一分类多档案；一档案多次借阅申请。注意：今天这仍是学习库图，不替代 D33 的正式五表设计。

### 19:00–23:00｜XML→SQL 与最终归档

1. 在 IDEA 打开 `SysDictTypeMapper.xml`，定位 `selectDictTypeList`。
2. 写“参数—条件”对照表：参数为空时对应 `<if>` 不拼；参数非空时拼哪一段；`<where>` 如何避免多余 AND。
3. 在 DBeaver 对若依项目数据库执行从 XML 翻出的**只读 SELECT**；传入一组具体条件，例如字典名称关键字和状态。用页面字典查询相同条件作对比。
4. 保存：自测 SQL、ER 图、数据字典、XML 对照笔记和至少三张截图。

## 今日完成清单

- [ ] 六项自测至少独立完成五项。
- [ ] 有可读 ER 图和数据字典初稿。
- [ ] 能把一个 `<if>` 条件翻成普通 SQL。
- [ ] 全部学习材料已按 day-16 保存。

---

## 任务参考答案（完成后再查看）

答案：`<where>` 的价值是什么？帮助动态条件形成合法 WHERE，并处理前导 AND/OR。ER 图为何要与 SQL 对照？图是设计意图，SQL 是实际结构，两者不一致会导致后续生成错误。
