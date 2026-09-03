# D06 今天照做执行清单：SQL 查询、连接、分页

## 上午：准备测试数据（09:00–12:00）

1. [ ] 打开 DBeaver，连接学习库；执行 `SELECT DATABASE();` 确认是 `archive_learning`。
2. [ ] 让 AI 生成 10 条虚构分类/档案测试数据的 INSERT；提示它“不要生成 DELETE/UPDATE，不要使用真实姓名或文件”。
3. [ ] 人工看每条 INSERT 的表名和字段，逐条 Ctrl+Enter 执行。
4. [ ] 执行 `SELECT * FROM archive_record;`，记录实际行数。

## 下午：完成 10 条查询（15:00–18:00）

1. [ ] 新建脚本 `day06_query.sql`，第一行写注释 `-- 仅 SELECT 查询`。
2. [ ] 依次让 AI 给出并执行：全部档案、按状态、名称 LIKE、保管年限范围、创建时间排序、LIMIT 分页、分类 JOIN、状态 COUNT。
3. [ ] 每执行一条，在 SQL 上方写：`-- 目的：`、`-- 预期：`、`-- 实际行数：`。
4. [ ] 若 JOIN 行数异常，先检查 `ON category_id = ...` 是否存在；不要改表结构。

## 晚上：对照若依查询（19:00–23:00）

1. [ ] 打开 `SysDictTypeMapper.xml`，搜索 `<select id="selectDictTypeList"`。
2. [ ] 找 `dictName` 和 `status` 的 `<if>`；用中文在笔记写“有参数才拼哪个 WHERE 条件”。
3. [ ] 浏览器字典类型页面输入一个查询条件，按 F12 → Network → 点击 `type/list`。
4. [ ] 对比 URL Query String 与 XML 条件；截图并遮挡 token。
5. [ ] 让 AI 把一个 `<if>` 翻译为普通 SQL；自己在 XML 逐词核对。

## 收尾验收

- [ ] `day06_query.sql` 有 10 条只读查询和结果说明。
- [ ] 能解释 `JOIN ON`、`ORDER BY`、`LIMIT` 的作用。
- [ ] 已将页面参数对应到 XML 动态 SQL 条件。
