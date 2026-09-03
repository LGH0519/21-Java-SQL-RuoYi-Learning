# Day 26：若依代码生成器与标准 CRUD

> 今日目标：在学习库从一张最小表预览生成产物，能列出 8 类文件和“生成器不能替我完成的设计”。今天只预览，不把代码集成进项目。

## 知识点速记（完成当天任务后复习）

- 生成器根据表结构、字段配置、模板生成重复 CRUD 骨架。
- 典型产物：Entity、Controller、Service 接口、ServiceImpl、Mapper 接口、Mapper XML、Vue 页面、前端 API/菜单 SQL。
- 生成器不能替你决定需求、角色权限、状态机、复杂事务、文件语义和测试结论。

## 视频、文档与若依对应点

- [若依 Vue：代码生成](https://doc.ruoyi.vip/ruoyi-vue/document/htsc.html)（页面结构若调整，以官网当前导航为准）。
- 本机：`E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-generator`；只浏览，不改模板。

## 本日重点、易混点与若依对应

代码生成的前提是表设计正确：名称、类型、主键、注释、查询字段、表单控件都会影响结果。生成出来的 CRUD 是起点，不是“功能完成”。尤其档案借阅审批多表更新与状态约束，必须后续由 AI 先输出规则/测试，人工审查后生成最小实现。

## 今天照做（09:00–23:00）

### 09:00–12:00｜检查学习表

1. DBeaver 连接 `archive_learning`，执行：

```sql
CREATE TABLE demo_asset (
  id BIGINT NOT NULL AUTO_INCREMENT COMMENT '资产主键',
  asset_name VARCHAR(100) NOT NULL COMMENT '资产名称',
  asset_status CHAR(1) NOT NULL DEFAULT '0' COMMENT '状态',
  create_time DATETIME DEFAULT CURRENT_TIMESTAMP COMMENT '创建时间',
  PRIMARY KEY (id)
) ENGINE=InnoDB DEFAULT CHARSET=utf8mb4 COMMENT='生成器演示资产表';
```

2. 确认表、字段注释、主键都存在；不要用 archive 业务表做第一次生成实验。
3. IDEA 打开 `ruoyi-generator`，仅浏览目录结构和模板名称，记录它是生成器模块。

### 15:00–18:00｜在后台导入并预览

1. 若依前端登录 → **系统工具** → **代码生成**（菜单名称以页面实际为准）。
2. 点击“导入”，选择 `demo_asset`。若看不到表，先确认生成器数据源是否指向 `archive_learning`；不要修改未知项目数据源，记录问题后再查配置。
3. 导入后打开编辑/字段配置，逐项看字段名、Java 类型、查询方式、表单类型、是否插入/编辑/列表。只记录观察，不盲目保存覆盖。
4. 点击“预览代码”，展开并记录 8 类文件。

### 19:00–23:00｜不集成的审查练习

1. 对每类预览文件写一句作用：Entity 数据对象；Controller 接口入口；Service 业务层；ServiceImpl 实现；Mapper 数据入口；XML SQL；Vue 页面；API 请求封装。
2. 写“生成器仍需人工/AI 协作设计”的清单：唯一编号、借阅状态转换、审批事务、当前用户边界、附件本体、测试用例。
3. 截图表结构和预览目录。不要下载后覆盖项目；若下载仅存个人临时学习目录。

## 今日完成清单

- [ ] `demo_asset` 已在学习库建立。
- [ ] 在生成器成功导入/预览，或记录了数据源问题与证据。
- [ ] 能列 8 类产物及 5 项不能自动生成的业务设计。

---

## 任务参考答案（完成后再查看）

答案：为什么不直接用正式 archive_record 练手？正式模块依赖已审查的完整设计，第一次实验应隔离到学习表，避免污染项目与误导后续生成。
