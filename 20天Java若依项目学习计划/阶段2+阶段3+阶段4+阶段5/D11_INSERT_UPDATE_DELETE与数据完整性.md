# D11｜INSERT、UPDATE、DELETE 与数据完整性

> 今日目标：为昨天两张表准备可重复执行的测试数据，理解 DML 的影响范围，并形成至少 25 条带业务注释的 SQL。

## 1. 知识速记

- DML：`INSERT` 新增行、`UPDATE` 修改已有行、`DELETE` 删除行。
- `WHERE` 是 UPDATE/DELETE 的安全边界；漏写它会影响整张表。
- 先 `SELECT` 预览，再执行修改；修改后再 `SELECT` 验证。
- 数据完整性包含：非空、唯一、合理类型、正确关联和合法状态。
- 当前 MySQL 客户端可能开启自动提交；D15 专门学习 `COMMIT/ROLLBACK`。

## 2. 资料

- [菜鸟教程：INSERT INTO](https://www.runoob.com/mysql/mysql-insert-query.html)
- [菜鸟教程：UPDATE](https://www.runoob.com/mysql/mysql-update-query.html)
- [菜鸟教程：DELETE](https://www.runoob.com/mysql/mysql-delete-query.html)

## 3. 知识详解

`INSERT INTO 表(列...) VALUES(值...)` 应明确列名，不依赖表列顺序。`UPDATE 表 SET 列=值 WHERE 条件` 不会自动保护你：条件写错就是业务事故。`DELETE` 是物理删除；正式系统通常需要先确认是否允许删除、是否有关联数据，不能仅因页面有删除按钮就直接删。

易混点：`NULL` 表示未知/未设置，不是空字符串 `''`，更不是数字 0。日期、编号、名称用引号；数值通常不加引号。面试常问：为何 Update 必须有 where？答：避免全表误修改，且应先查后改、受事务与权限保护。

## 4. 今天照做

### 09:00–12:00｜读资料并建立脚本

1. 打开 DBeaver → 连接 → `archive_learning` → SQL 编辑器 → 新建脚本。
2. 点击 **文件** → **另存为**，保存为 `day11_dml.sql` 到你自己的学习笔记目录；不要保存进若依源代码目录。
3. 在文件首行写：`-- Day11：仅操作 archive_learning 学习库`。
4. 阅读三篇资料，分别记录一句：INSERT 是什么、UPDATE 必须注意什么、DELETE 的风险是什么。

### 15:00–18:00｜插入至少 20 条数据

1. 执行以下分类数据；若昨天已插入“合同档案”，先用 `SELECT * FROM archive_category;` 看现有 ID，再避免重复名称。

```sql
USE archive_learning;
INSERT INTO archive_category (category_name) VALUES
('项目资料'), ('行政文件'), ('人事档案'), ('财务凭证'),
('制度文件'), ('会议纪要'), ('设备档案'), ('安全资料'), ('其他资料');
```

2. 执行下列 10 条档案。`category_id` 以你的实际分类 ID 为准；若自动编号不是 1–10，请先查询再改数字。

```sql
INSERT INTO archive_record
(archive_no, archive_name, category_id, status, retention_years, physical_location)
VALUES
('ARC-2026-002','软件采购合同',1,'0',10,'A楼-01柜'),
('ARC-2026-003','办公楼改造项目资料',2,'0',20,'A楼-02柜'),
('ARC-2026-004','年度行政发文汇编',3,'0',30,'A楼-03柜'),
('ARC-2026-005','劳动合同样本',4,'0',30,'A楼-04柜'),
('ARC-2026-006','报销凭证-01',5,'0',10,'B楼-01柜'),
('ARC-2026-007','档案管理制度',6,'0',30,'B楼-02柜'),
('ARC-2026-008','周例会纪要',7,'0',10,'B楼-03柜'),
('ARC-2026-009','服务器维保记录',8,'0',10,'B楼-04柜'),
('ARC-2026-010','消防检查资料',9,'0',10,'C楼-01柜'),
('ARC-2026-011','其他材料样本',10,'0',5,'C楼-02柜');
```

3. 执行 `SELECT COUNT(*) AS record_count FROM archive_record;`。若少于 10，查执行错误，不要为了凑数复制同一个 `archive_no`。

### 19:00–23:00｜安全更新、删除与验证

1. 在 SQL 文件写注释和预览：

```sql
-- 业务：确认编号 ARC-2026-002 当前状态，再标记为借阅中。
SELECT id, archive_no, status FROM archive_record WHERE archive_no = 'ARC-2026-002';
UPDATE archive_record SET status = '1' WHERE archive_no = 'ARC-2026-002';
SELECT id, archive_no, status FROM archive_record WHERE archive_no = 'ARC-2026-002';
```

2. 插入一条专门用于删除的测试记录 `ARC-TEST-DELETE`；先 SELECT，随后执行：

```sql
DELETE FROM archive_record WHERE archive_no = 'ARC-TEST-DELETE';
SELECT * FROM archive_record WHERE archive_no = 'ARC-TEST-DELETE';
```

3. 最后检查 SQL 编辑器状态栏。统计你的脚本：分类新增约 10 条、档案新增 10 条、预览/更新/删除/验证语句补足到至少 25 条有效 SQL。每条改变数据的 SQL 前都写 `-- 业务：` 注释。
4. 截图：行数、更新前后状态、删除后空结果；把 `day11_dml.sql` 保存。

## 5. AI Agent 操作卡

把**已脱敏的表结构和一两条 SQL**贴给 AI，要求它找“缺失 WHERE、类型不符、重复编号、误删风险”。不要把数据库密码、完整配置文件或真实数据发给 AI。收到建议后先在副本或学习数据上验证。

## 6. 验收与文末答案

- [ ] `day11_dml.sql` 有至少 25 条有效 SQL 和业务注释。
- [ ] 每张表至少 10 条学习数据（分类可多于 10）。
- [ ] 完成并验证一次定向 UPDATE 和一次定向 DELETE。
- [ ] 能解释 `NULL`、`''`、`0` 的区别。

答案：UPDATE/DELETE 前为什么先 SELECT？因为同一 WHERE 能预览影响行，减少误操作。重复编号为何插不进去？昨天建立的 `uk_archive_no` 唯一约束在数据库层拒绝它。
