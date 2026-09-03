# D05 今天照做执行清单：MySQL 建模与 DDL

## 开始前（09:00–09:20）

1. [ ] 打开 DBeaver；左侧 Database Navigator 找到本机连接。
2. [ ] 单击连接名称，查看其下数据库列表；确认不要在 `ry-vue` 执行今天的 DDL。
3. [ ] 新建 `D05-notes.md`，写：`目标库：archive_learning`、`禁止操作：若依库写入`。

## 上午：字段字典（09:20–12:00）

1. [ ] 打开 D05 学习文档，复制“分类/档案”字段要求到笔记表格。
2. [ ] 在 AI Agent 输入：`请根据以下字段生成字段字典，不生成 SQL：分类、档案编号、名称、状态、保管年限、物理位置、创建时间。每列说明类型、是否空、约束、原因。`
3. [ ] 人工检查：`archive_no` 必须唯一；年限不能是日期；创建时间不是 VARCHAR；状态必须有说明。
4. [ ] 将接受后的表保存为 `docs/design/field-dictionary-draft.md`（学习目录内）。

## 下午：生成并执行 DDL（15:00–18:00）

1. [ ] 让 AI 根据字段字典生成：`CREATE DATABASE archive_learning`、`CREATE TABLE archive_category`、`CREATE TABLE archive_record`；要求 SQL 每段前有注释。
2. [ ] 不执行，先在编辑器检查每条 `CREATE` 的库/表名。
3. [ ] DBeaver 点击 **SQL Editor → New SQL Script**。
4. [ ] 在编辑器第一行手工输入：`SELECT DATABASE();`，选中该行按 **Ctrl+Enter**；若结果不是预期学习库，停止。
5. [ ] 确认后粘贴经审查 DDL；逐条选中 `CREATE` 语句 → Ctrl+Enter，不要一次执行未知整页脚本。
6. [ ] 左侧数据库右键 → **Refresh**；展开 `archive_learning → Tables`，确认两表出现。

## 晚上：结构核对（19:00–23:00）

1. [ ] 在 SQL 编辑器输入并执行：`SHOW CREATE TABLE archive_record;`。
2. [ ] 对照字段字典，勾选主键、`archive_no` 唯一、分类关联字段、状态、创建时间。
3. [ ] 打开若依 `ry_20260417.sql`，Ctrl+F 搜 `sys_dict_type`；只读其建表段，写 3 个可借鉴字段习惯。
4. [ ] 写调试记录：若建表失败，记录目标库、执行的单条 SQL、第一条错误；再向 AI 询问原因，不让 AI 改全部 SQL。

## 收尾验收

- [ ] 两表只在学习库创建。
- [ ] 字段字典与 `SHOW CREATE TABLE` 一致。
- [ ] 有一次“先 SELECT DATABASE 再写入”的证据。
