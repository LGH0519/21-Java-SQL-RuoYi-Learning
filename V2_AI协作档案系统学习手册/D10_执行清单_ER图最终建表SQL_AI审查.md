# D10 今天照做执行清单：ER 图、最终 SQL、AI 审查

## 上午：画 ER 图（09:00–12:00）

1. [ ] 新建 `docs/design/er-diagram.md`。
2. [ ] 写出 5 张表：`archive_category`、`archive_record`、`archive_attachment`、`archive_borrow_application`、`archive_borrow_record`。
3. [ ] 在每表下列主键、关联字段、状态字段。
4. [ ] 使用 Mermaid 或手画箭头写出：分类 1→N 档案、档案 1→N 附件/申请/借阅记录。

## 下午：最终 DDL（15:00–18:00）

1. [ ] 向 AI 提供 D08–D09 文档和 ER 图，要求先输出“表/字段/索引审查表”，不生成 SQL。
2. [ ] 人工逐项确认后，要求 AI 生成 `sql/archive_v1.sql`；要求 SQL 分表、带注释、无 DROP DATABASE。
3. [ ] 在 DBeaver 打开目标项目/学习库，先执行 `SELECT DATABASE();`。
4. [ ] 仅在确认的非生产库逐段执行 SQL；每执行一段刷新 Tables。
5. [ ] 用 `SHOW CREATE TABLE` 和 `SHOW INDEX` 对照 ER 图。

## 晚上：AI 反向审查（19:00–23:00）

1. [ ] 把 ER 图和 SQL 交给 AI，要求只列不一致、缺失约束、状态字段风险。
2. [ ] 每条建议写“接受/拒绝/理由”；拒绝与需求无关的扩展表。
3. [ ] 修改后重新执行于空测试库，确认 5 表都能创建。
4. [ ] Git status 确认只出现 `docs/design` 和 `sql/archive_v1.sql`。

## 收尾验收

- [ ] ER、字段字典、SQL 三者一致。
- [ ] 5 表及索引在 DBeaver 可见。
- [ ] 有 AI 审查接受/拒绝记录。
