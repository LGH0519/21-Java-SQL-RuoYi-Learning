# Day 33：ER 图、字段字典与正式建表 SQL

> 今日目标：完成第一版 5 张业务表的可重复执行 SQL、ER 图和字段字典。执行到**新建项目数据库**，绝不在若依系统库随意建表。

## 知识点速记（完成当天任务后复习）

- 五表：`archive_category`、`archive_record`、`archive_attachment`、`archive_borrow_application`、`archive_borrow_record`。
- 主表档案编号 `archive_no` 必须唯一；附件/申请/借阅记录用 `archive_id` 关联档案。
- 附件表存元数据（原名、存储路径、大小、类型），文件本体由现有上传能力处理。
- 审批申请和实际借出分表：申请是审批过程，借阅记录是发生的借出/归还事实。

## 视频、文档与若依对应点

- D32 的状态图、权限矩阵。
- [MySQL CREATE TABLE](https://dev.mysql.com/doc/refman/8.0/en/create-table.html)
- 若依现有 SQL 仅作风格参考：`backend\sql\ry_20260417.sql`。

## 本日重点、易混点与若依对应

字段字典先于生成器：它决定 Java 类型、表单控件、查询条件和约束。建议业务关联字段类型与被引用主键一致（均 BIGINT）。V1 可以用逻辑关联并以应用/事务保证一致；是否加数据库外键需遵从项目规范并考虑删除/导入限制，本计划先以索引和业务校验为主，不擅自改若依系统表。

## 今天照做（09:00–23:00）

### 09:00–12:00｜字段字典

1. 在 `docs\design\` 新建 `data-dictionary-v1.md`。为每表建立：字段、类型、可空、默认、索引/约束、说明、示例。
2. 必须包含：分类 `parent_id/category_name/status`；档案 `archive_no/archive_name/category_id/status/retention_years/physical_location`；附件 `archive_id/file_name/file_path/file_size/file_type`；申请 `archive_id/applicant_id/application_status/apply_reason`；借阅 `archive_id/application_id/borrower_id/borrow_date/due_date/return_date/borrow_status`。
3. 对每个状态码写枚举表，避免“1 到底是审批通过还是借阅中”的混淆。

### 15:00–18:00｜写并执行 SQL

1. 在 backend 项目 `sql` 下新建 `archive_v1.sql`；首行写清数据库名和“仅 V1 业务表”。
2. 以 `CREATE DATABASE archive_management_v1 ...; USE archive_management_v1;` 开始，写五张 CREATE TABLE。每张表包含 `id BIGINT` 主键、`create_by/create_time/update_by/update_time/remark`（是否采用以项目实际生成器字段规范为准）。
3. 必加：`archive_record.archive_no` 唯一索引；每个关联列索引；申请状态+申请人/借阅状态+到期日按查询需求建复合索引并写注释。
4. DBeaver 新建数据库后完整执行一次。若需要重试，只删除你刚建的 `archive_management_v1` 学习库并确认目标精确，绝不删除若依原库。

### 19:00–23:00｜数据、ER 图与 AI 审查

1. 每表插至少 5 条**虚构测试数据**，用 SELECT 验证关联值存在、编号无重复。
2. 画五表 ER 图，标出 `archive_id`、`application_id`、`category_id` 的基数。
3. 把 SQL 和字段字典交给 AI 做审查：要求输出“类型不一致、状态冲突、缺索引、约束过强/过弱、测试场景”，不要生成/执行替代 SQL。
4. 将采纳与不采纳的建议写入 `design-review-d33.md`。

## 今日完成清单

- [ ] 新库可从空库完整执行 `archive_v1.sql`。
- [ ] 五表、每表 ≥5 条虚构数据、ER 图、字段字典一致。
- [ ] 有 AI 审查记录和人工结论。

---

## 任务参考答案（完成后再查看）

答案：为什么申请表和借阅记录分开？申请会被拒绝/撤销且未必借出；借阅记录代表真正已借出的事实，归还日期与逾期属于它。
