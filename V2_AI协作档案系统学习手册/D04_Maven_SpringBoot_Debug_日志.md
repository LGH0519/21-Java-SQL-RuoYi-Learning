# Day 04：Maven、Spring Boot、IDEA Debug 与日志

## 知识速记

Maven 用 `pom.xml` 管理模块、依赖和构建；Spring Boot 从 `RuoYiApplication` 启动；Debug 用断点观察真实变量；日志是按时间记录程序运行事实。定位时先看第一条根因，而不是最后一条堆栈。

## 资料

- [Maven POM](https://www.runoob.com/maven/maven-pom.html)
- [若依环境部署](https://doc.ruoyi.vip/ruoyi-vue/document/hjbs.html)
- 本机：`backend/pom.xml`、`RuoYiApplication.java`、`application.yml`。

## 知识详解

`pom.xml` 的 `<properties>` 集中版本；`<dependencyManagement>` 统一依赖版本；`<modules>` 声明 `ruoyi-admin/framework/system/...` 多模块。Maven 不等于 Java：JDK 编译/运行 Java，Maven 按 pom 组织依赖和构建。

`@SpringBootApplication` 标记启动类；`SpringApplication.run` 拉起应用。Debug：红点是断点；F8 执行当前行；F7 进入业务方法；F9 继续。不要 F7 进入框架底层，先沿 Controller→Service→Mapper 追。日志的 `INFO` 是正常信息，`WARN` 是警告，`ERROR`/`Caused by` 才是故障证据。

## 今天照做（09:00–23:00）

### 09:00–12:00

1. 打开根 `backend/pom.xml`，记录 Java 版本、项目版本、3 个 module；不改任何版本。
2. 打开 `RuoYiApplication.java`，写下启动入口和 `SpringApplication.run` 的作用。
3. 打开 `application.yml`，只找端口、Redis 主机；不记录密码、token 或数据源凭据。

### 15:00–18:00

1. Run 后端，确认 `Started RuoYiApplication` 和“若依启动成功”。
2. 访问 `http://localhost:8080`，记录它是后端提示页而非管理页面。
3. 在 `SysDictTypeController.list` 的 Service 调用行打断点，Debug 后端；浏览器刷新字典类型。
4. F7 进入 Service，F8 执行 Mapper 调用，F9 返回页面。记录每一步变量。

### 19:00–23:00

1. 在 Controller 的 `return getDataTable(list)` 前观察 list 大小。
2. 在 Network 记录对应请求状态和 response；将它与断点结果关联。
3. 故障演练不改配置：假设启动失败，写“先找最早的 Caused by → 判断数据库/Redis/端口 → 收集证据”的流程。

## AI 操作卡

只把第一条异常和脱敏上下文交给 AI，要求“最可能层级、验证步骤、最小安全操作”。禁止接受升级依赖、删除缓存目录、重置 Git 等建议。

## 验收

- [ ] 能解释 pom 的 3 个区块和启动入口。
- [ ] Debug 命中过 Controller 与 Service。
- [ ] 有“日志—断点—Network”三种证据对应记录。

## 文末答案与自测

**为什么不直接 F7 进入 Mapper XML？** MyBatis Mapper 实现在运行时生成，今天用源码、参数和结果验证即可。  
**通过标准：** 能按事实说明一次列表请求经过了哪个断点和哪段日志。
