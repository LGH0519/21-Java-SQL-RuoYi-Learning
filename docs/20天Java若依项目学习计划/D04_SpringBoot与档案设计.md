# Day 4：从需求设计到可生成的表

## 今日成果

完成档案系统需求、接口、ER 图和建表脚本；理解一个 Spring Boot API 的最小结构。

| 时间 | 知识点 | 学习与操作步骤 | 资源 | 验收 |
|---|---|---|---|---|
| 09:00–10:20 | Spring Boot、依赖、启动类、配置 | 看 Spring Boot 视频“入门案例、配置文件”标题；阅读自己的 `pom.xml` 中 Spring Boot 依赖。 | [Spring Boot 视频](https://www.bilibili.com/video/BV15b4y1a7yG/) | 解释 `pom.xml` 管什么、`application.yml` 管什么。 |
| 10:20–12:00 | REST、Controller/Service/Mapper | 看“SpringMVC/REST、MyBatis”标题；在独立练习项目写 `/hello` GET 接口。 | [REST 示例](https://www.cainiaojc.com/springboot/springboot-rest.html) | 浏览器或 Apifox 返回 JSON。 |
| 15:00–16:20 | 需求拆分 | 写 `requirements.md`：角色、页面、字段、规则。规则仅三条：编号唯一、借阅中不能重复借、归还后可再借。 | 自写 | 文档 ≤2 页且没有模糊词。 |
| 16:20–18:00 | ER 图、表设计 | 使用 draw.io/纸笔：分类 1 对多 档案；档案 1 对多 借阅记录。确定每表主键、时间字段、状态字段。 | [MySQL 创建表](https://www.runoob.com/mysql/mysql-create-tables.html) | ER 图 + 4 表字段清单。 |
| 19:00–20:30 | 手写 SQL | 建 `archive_category/archive_record/archive_borrow/archive_operation_log`；每张表写注释、主键、创建时间。 | Day 2 笔记 | `sql/archive.sql` 可从空库执行。 |
| 20:30–22:00 | 接口设计 | 写 6 个接口：分类列表、档案分页、档案新增、借阅、新增归还、统计。每个写方法/路径/参数/返回。 | Apifox | Apifox 中创建接口草稿。 |
| 22:00–23:00 | 设计复盘 | 检查“每个页面/规则能找到表字段和接口”；提交设计文件。 | 每日模板 | 2 次提交。 |

## 必须验收

- [ ] 不写代码生成器前，已拥有可执行的 `archive.sql`。
- [ ] 能说清借阅为什么要单独一张表：它记录一次发生过的业务，而不是档案本身属性。
