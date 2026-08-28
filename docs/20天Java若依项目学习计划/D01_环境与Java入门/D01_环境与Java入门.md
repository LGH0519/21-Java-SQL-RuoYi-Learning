# Day 1：能独立启动、运行和验证

## 今日成果

你能说清楚项目用的工具版本；若依仍可运行；能自己建一个 Java 类并运行；DBeaver、Apifox、Git 至少各完成一次最小操作。

## 开始前检查

- [ ] 已阅读 `00_工具安装与调试手册.md` 的“工具安装顺序”。
- [ ] 本地若依在昨天状态可运行；今天不升级任何依赖。
- [ ] 新建 `docs/day-01/` 保存版本截图和笔记。

| 时间          | 学什么（知识点）                                 | 怎么做（逐步操作）                                                                                           | 资源                                                                                                                      | 验收                          |
| ----------- | ---------------------------------------- | --------------------------------------------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------- | --------------------------- |
| 09:00–09:40 | 项目结构：后端 `pom.xml`、前端 `package.json`、配置文件 | 资源管理器分别找到这三个文件；打开 README；记录仓库名、分支、端口、数据库名。                                                          | [若依 Vue 环境文档](https://doc.ruoyi.vip/ruoyi-vue/document/hjbs.html)                                                       | `environment.md` 写下 5 项信息。  |
| 09:40–10:30 | JDK、Maven、MySQL、Redis、Node 的“版本决定兼容性”概念  | 在终端依次运行 `java -version`、`mvn -v`、`node -v`；DBeaver 中执行 `SELECT VERSION();`；Redis 不会查则只记录“已由项目运行验证”。 | 若依环境文档                                                                                                                  | 截图/复制输出，不改版本。               |
| 10:30–12:00 | Java：JDK/JRE/JVM、`main`、注释、变量、字符串、输出     | 看 Java 视频中“JDK/JRE、HelloWorld、IDEA、第一个代码、变量”标题；在 IDEA 新建 `JavaBasics` 项目和 `HelloCodex` 类。           | [Java 视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/)，[Java 基础语法](https://www.runoob.com/java/java-basic-syntax.html) | 运行并输出姓名、目标、学习天数；写 3 个变量。    |
| 15:00–16:20 | 基本类型、赋值、算术运算、类型转换                        | 先看视频对应“基本数据类型、变量、运算符”；写“厘米转米”“三门课平均分”“整数除法与小数除法”各一题。                                                | [Java 教程](https://www.runoob.com/java/java-data-types.html)                                                             | 3 个类都能运行，输出与手算一致。           |
| 16:20–18:00 | DBeaver：连接、选择库、执行 SQL；SQL `SELECT`       | 按手册建 MySQL 连接；建库 `study_java`；在 SQL 编辑器执行 `CREATE TABLE student` 和 5 条 `SELECT`。                    | [MySQL 教程](https://www.runoob.com/mysql/mysql-tutorial.html)                                                            | 保存 `day01.sql`；查询结果有 3 行数据。 |
| 19:00–20:30 | 若依运行链路                                   | 在 IDEA 找启动类；用普通 Run 启动；浏览器登录一次；打开配置文件，找端口和数据库连接配置。                                                  | 若依环境文档                                                                                                                  | 画出浏览器→前端→后端→MySQL 的 4 格图。   |
| 20:30–21:30 | 第一次调试                                    | 在启动类 `main` 第一行打断点，用 Debug 启动，观察程序停住、F8 单步一次、F9 继续。                                                 | 调试手册第 3 节                                                                                                               | 截图 Debug 面板；不要求看懂框架代码。      |
| 21:30–23:00 | Git 与复盘                                  | `git status` → `git add` → `git commit` → `git push`；填写每日模板。                                        | [Git 第 4、7、8 集](https://www.bilibili.com/video/BV1MU4y1Y7h5)                                                            | 2 个有意义提交；日志 ≥100 字。         |

## 必须验收

- [ ] 若依能启动并登录，且你没修改依赖版本。
- [ ] Java 项目有 4 个运行成功的小类。
- [ ] DBeaver 能查询到 `study_java.student`。
- [ ] 你知道红色断点、Shift+F9、F8、F9 分别是什么。

## 卡住时

优先检查“IDEA 项目 JDK”和“项目要求 JDK”是否一致；不要删除 `.m2`、不要重装系统。
