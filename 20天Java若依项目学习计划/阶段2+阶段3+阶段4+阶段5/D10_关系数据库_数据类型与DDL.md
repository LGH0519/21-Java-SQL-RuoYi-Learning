# D10｜关系数据库、数据类型与 DDL

> 今日目标：在 DBeaver 从零建立学习库、两张档案表，并能用自己的话说明「库、表、行、列、主键、外键、唯一约束」。今天写的是学习库，不修改若依已有库。

## 1. 知识速记

- **数据库**是有组织保存数据的容器；`archive_learning` 是今天新建的学习数据库。
- **表**保存同类数据；行是一条记录，列是一个字段。`archive_record` 的一行就是一份档案。
- **主键（PRIMARY KEY）**唯一定位一行，通常用 `BIGINT` 的 `id`；主键不能重复、不能为 `NULL`。
- **外键**表示“本表的一列引用另一表主键”。本日先理解；后续项目可由 `category_id` 关联分类。
- **唯一约束（UNIQUE）**防止业务编号重复；档案编号 `archive_no` 必须唯一。
- DDL（数据定义语言）负责 `CREATE / ALTER / DROP`，用于定义结构；不是增删具体档案数据。

## 2. 资料

- [菜鸟教程：MySQL 数据类型](https://www.runoob.com/mysql/mysql-data-types.html)：看数值、字符串、日期三部分。
- [菜鸟教程：CREATE TABLE](https://www.runoob.com/mysql/mysql-create-tables.html)：看语法和约束示例。
- 本机若依 SQL：`E:\WorkSpace\ruoyi-archive-management\backend\sql\ry_20260417.sql`；今天只阅读 `sys_dict_type` 的建表段，不执行、不修改。

## 3. 知识详解

### 3.1 为什么档案编号不用 int

`ARC-2026-001` 含字母、连字符和前导零，必须用 `VARCHAR`。`id` 是系统内部定位用的数字；`archive_no` 是业务人员识别用的编号，二者不能混为一谈。

### 3.2 本日字段选型

| 字段 | 类型 | 原因 |
|---|---|---|
| `id` | `BIGINT` | 与若依常用主键风格一致，容量充足。 |
| `category_name`、`archive_name` | `VARCHAR(100/200)` | 中文、英文和标点都是文本。 |
| `archive_no` | `VARCHAR(64)` + UNIQUE | 编号是文本且不能重复。 |
| `category_id` | `BIGINT` | 保存分类主键值。 |
| `status` | `CHAR(1)` | 先以 `0`/`1` 等短状态码存储。 |
| `retention_years` | `INT` | 保管年限是整数。 |
| `create_time` | `DATETIME` | 保存日期与时分秒。 |

易混点：`VARCHAR(100)` 的 100 是最大字符长度，不是“只能输入 100 个字节”的简单同义词；`CHAR(1)` 适合固定长度短码，不等同布尔类型。今天先不加外键，是为了先看懂表结构；这不是说正式项目永远不需要关联约束。

## 4. 今天照做

### 09:00–12:00｜认识对象和准备连接

1. 打开浏览器，阅读上方两篇菜鸟教程；在笔记写下 DDL 的三个词：`CREATE`、`ALTER`、`DROP`。
2. 打开 DBeaver。左侧“数据库导航器”找到你的 MySQL 连接；若未连接，双击连接名，输入你自己设置的 MySQL 密码。
3. 右键该连接 → **SQL 编辑器** → **新建 SQL 脚本**。
4. 粘贴下列 SQL。点击工具栏的橙色播放按钮，或选中语句后按 `Ctrl+Enter` 执行。

```sql
CREATE DATABASE IF NOT EXISTS archive_learning
  DEFAULT CHARACTER SET utf8mb4
  COLLATE utf8mb4_0900_ai_ci;
USE archive_learning;
```

5. 左侧连接右键 → **刷新**。展开 `archive_learning`；现在它仍没有表，这正常。
6. 用资源管理器打开若依 SQL 文件，搜索 `CREATE TABLE \`sys_dict_type\``。只阅读 `id`、`dict_name`、`dict_type`、`status`、`create_time` 的类型和注释，记录一个你看懂的字段。

### 15:00–18:00｜亲手建立两张表

1. 在同一 SQL 编辑器输入并执行以下完整脚本。执行后下方应显示“语句已执行”。

```sql
USE archive_learning;

CREATE TABLE archive_category (
  id BIGINT NOT NULL AUTO_INCREMENT COMMENT '分类主键',
  category_name VARCHAR(100) NOT NULL COMMENT '分类名称',
  status CHAR(1) NOT NULL DEFAULT '0' COMMENT '状态：0正常 1停用',
  create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (id),
  UNIQUE KEY uk_category_name (category_name)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='档案分类学习表';

CREATE TABLE archive_record (
  id BIGINT NOT NULL AUTO_INCREMENT COMMENT '档案主键',
  archive_no VARCHAR(64) NOT NULL COMMENT '档案编号',
  archive_name VARCHAR(200) NOT NULL COMMENT '档案名称',
  category_id BIGINT NOT NULL COMMENT '分类主键',
  status CHAR(1) NOT NULL DEFAULT '0' COMMENT '状态：0在库 1借阅中',
  retention_years INT NOT NULL COMMENT '保管年限',
  physical_location VARCHAR(200) DEFAULT NULL COMMENT '实体存放位置',
  create_time DATETIME NOT NULL DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (id),
  UNIQUE KEY uk_archive_no (archive_no),
  KEY idx_record_category_id (category_id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='档案主表学习表';
```

2. 左侧 `archive_learning` → **表** → 刷新。双击 `archive_record`，选择“列”页签，对照每一列、类型、是否非空和索引。
3. 在笔记写一句：`category_id` 目前是“逻辑关联字段”，还没有 `FOREIGN KEY`；D14 再比较真实项目的中间表关系。

### 19:00–23:00｜验证唯一约束与保存证据

1. 执行下面语句：

```sql
USE archive_learning;
INSERT INTO archive_category (category_name) VALUES ('合同档案');
INSERT INTO archive_record
  (archive_no, archive_name, category_id, retention_years, physical_location)
VALUES
  ('ARC-2026-001', '办公设备采购合同', 1, 10, 'A楼-01柜');

SELECT * FROM archive_record;
```

2. 再次只执行最后这条 `INSERT`。预期失败，错误大意为 `Duplicate entry 'ARC-2026-001' for key 'uk_archive_no'`。不要删表、不要修改约束。
3. 截图保存到自己的 `docs/day-10/`：两张表的列结构、重复编号的报错、`SELECT` 结果各一张。
4. 写排查记录：若报“Unknown database”，先执行创建库并刷新；若报“Table exists”，先确认是否已成功创建，**不要**随手执行 `DROP TABLE`。

## 5. AI Agent 操作卡

可问 AI：

```text
我在 MySQL 8 的学习库设计 archive_record。字段及规则如下：……。
请只检查字段类型、非空、唯一约束和索引是否匹配规则，输出问题清单和建议 SQL；不要执行 SQL，不要改已有若依表。
```

收到建议后，逐项问自己：它服务哪个业务规则？用 DBeaver 执行后表结构是否和建议一致？AI 不能替你决定编号规则。

## 6. 验收与文末答案

- [ ] `archive_learning`、`archive_category`、`archive_record` 都可在 DBeaver 看见。
- [ ] 两张表由你亲手执行 SQL 创建。
- [ ] 重复 `archive_no` 的插入失败，且你保留了错误截图。
- [ ] 能回答：主键定位一行；唯一约束保护业务编号；外键表示跨表引用。

自测答案：为什么不把名称设为 `INT`？因为名称是文本。为什么 `archive_no` 与 `id` 都可能唯一？前者是业务规则，后者是数据库内部主键，它们用途不同。
