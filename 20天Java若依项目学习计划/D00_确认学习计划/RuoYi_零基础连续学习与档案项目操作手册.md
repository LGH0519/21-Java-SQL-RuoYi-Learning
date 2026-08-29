# RuoYi-Vue 零基础连续学习与档案项目操作手册

## 1. 这份手册解决什么问题

这份手册以《若依（RuoYi-Vue）项目零基础学习路径》为骨架，改成一条连续路线：**先连续 Java，再连续 SQL，再用真实若依代码学习进阶内容，最后只补完成项目必需的前端。**

你不需要一边学 Java、一边学 SQL、一边写 Vue。每个学习日只有一个主主题；上午看少量材料，下午和晚上始终用同一个主题写代码、写 SQL 或调试。

### 已确认的项目事实

| 项目部分 | 实际情况 | 后续使用的真实路径 |
|---|---|---|
| 后端 | RuoYi 3.9.2、Java 17、Spring Boot 4.1.0、MyBatis、Security、Redis | `E:\WorkSpace\ruoyi-archive-management\backend` |
| 前端 | Vue 3.5、Vite 6、Element Plus、Axios、Pinia | `E:\WorkSpace\ruoyi-archive-management\frontend` |
| 后端端口 | `8080` | `backend/ruoyi-admin/.../RuoYiApplication.java` |
| 前端端口 | `80`，通过 `/dev-api` 代理到后端 | `frontend/vite.config.js` |
| 第一条阅读链 | 字典类型列表 | 本手册第 3 阶段使用的真实文件 |

### 总时长与节奏

最低为 **42 个学习日，约 420 小时**。这是“从零开始 + 能看懂真实框架 + 做一个可讲解项目”的保守下限；当天验收不通过，就留在当天补齐，**不要跳到下一天**。

每天的固定结构：

| 时段 | 固定动作 |
|---|---|
| 09:00–12:00 | 只学当天主题的知识；视频不超过 90 分钟，必须边看边写。 |
| 15:00–18:00 | 完成当天练习或项目操作。 |
| 19:00–22:30 | 对同一主题调试、整理、做验收题。 |
| 22:30–23:00 | 写当天记录：概念解释、证据、卡点、明日开始条件。 |

## 2. 使用规则

1. **学习项目和若依项目分开。** Java/SQL 练习放在 `E:\WorkSpace\java-learning` 或 `study_java` 数据库；前 25 天不修改若依业务源码。
2. 只有当天验收全部完成，才进入下一天。不会的内容在当天问 AI 或问我，不能“先跳过”。
3. Git 只提交真实项目设计、SQL、功能或文档；不为凑次数提交空改动。
4. 看代码时不是从头翻目录，而是总按“前端页面 → API → Controller → Service → Mapper → XML → 数据库”追一条真实请求。
5. 使用 AI 前，先写“现象、已检查位置、预期结果”；AI 先解释和列排查顺序，最后才给最小代码。

## 3. 资源索引（固定，只看指定部分）

| 代码 | 资源 | 用途 |
|---|---|---|
| J | [Java 零基础视频](https://www.bilibili.com/video/BV1Ho4y1f7Gd/) | 选集按标题找“变量、流程控制、数组、方法、类与对象、继承、接口、集合、异常”。不看网络、多线程、反射。 |
| JD | [Java 中文教程](https://www.runoob.com/java/java-tutorial.html) | 视频后查语法和完成示例。 |
| M | [MySQL 视频](https://www.bilibili.com/video/BV1Kr4y1i7ru/) | 看基础 SQL：第 2–18 集开始，再按标题看连接、事务、索引。 |
| MD | [MySQL 中文教程](https://www.runoob.com/mysql/mysql-tutorial.html) | 查 DDL、DML、查询、连接、事务、索引。 |
| S | [Spring Boot 视频](https://www.bilibili.com/video/BV15b4y1a7yG/) | 只在项目阅读阶段看：入门、配置、Web、MyBatis、事务、测试。 |
| R | [RuoYi-Vue 官方文档](https://doc.ruoyi.vip/ruoyi-vue/document/kslj.html) | 看项目能力、模块概念和官方术语。 |
| RH | [若依代码生成说明](https://doc.ruoyi.vip/ruoyi/document/htsc.html) | 二开阶段学习生成器。 |
| V | [Vue 3 视频](https://www.bilibili.com/video/BV1Ac411K7EQ/) | 只看 Day1-02 至 Day1-10：Vue、`setup`、`ref/reactive`、`computed`、`watch`、生命周期。 |
| VD | [Vue 官方中文文档](https://cn.vuejs.org/guide/introduction.html) | 查模板语法、响应式、事件和组件。 |
| G | [Git 视频](https://www.bilibili.com/video/BV1MU4y1Y7h5) | 只看 04、07–12、16–24；用于真实项目节点。 |

---

# 阶段 0：已完成环境确认（不计学习日）

你已完成：后端启动成功、前端登录页可见、当前 Git 分支为 `main`、JDK 17 与项目匹配。保留现有版本，**不升级 Spring Boot、Node、Vue 或依赖**。

---

# 阶段 1：Java 基础（Day 1–9，连续 9 天）

目标：不借助框架，能看懂并手写“档案、借阅、状态判断”这些 Java 代码；能用 IDEA 断点观察变量。

## Day 1：变量、类型、输出与 IDEA Debug

- 09:00–12:00：J 看“JDK/JRE/JVM、HelloWorld、变量、基本数据类型”；JD 阅读“简介、基础语法、数据类型”。只理解：Java 文件要编译运行；变量是带类型的值。
- 15:00–18:00：在 `E:\WorkSpace\java-learning` 新建 Java 17 项目，建 `Day01.java`。写 `String archiveName`、`int retentionYears`、`boolean borrowed`，打印三行。
- 19:00–22:30：在赋值行打断点，用 Debug 启动，观察三个变量；F8 单步，F9 结束。再写“分数平均值”和“厘米转米”两题。
- 验收：能解释 `String/int/boolean` 分别适合什么档案字段；Debug 面板有变量截图。

## Day 2：条件与循环

- 09:00–12:00：J 看“运算符、if/else、switch、for、while”；JD 阅读对应章节。
- 15:00–18:00：写 `ArchiveStatusDemo.java`：状态为 `AVAILABLE` 时打印“可借阅”，否则打印“不可借阅”；再用 `for` 打印 1–10 条模拟档案编号。
- 19:00–22:30：写 4 题：闰年、成绩等级、1–100 求和、数组中最大值。每题先手写 3 组输入与预期输出。
- 验收：能说明 `if` 是条件分支，`for` 是重复执行；4 题运行正确。

## Day 3：数组、方法、参数和返回值

- 09:00–12:00：J 看“数组、方法、方法重载”；JD 阅读“数组、方法”。
- 15:00–18:00：新建 `ArchiveTools.java`，写 `isValidArchiveNo(String no)`、`calculateOverdueDays(int borrowDays, int allowedDays)`、`findMax(int[] numbers)` 三个方法。
- 19:00–22:30：在调用 `isValidArchiveNo` 处断点；F7 进入方法，观察参数 `no`；F8 逐行看 `return`。
- 验收：能解释“参数是传进方法的数据，返回值是方法交回的结果”；3 个方法各有至少 2 组测试。

## Day 4：类、对象、构造方法、封装

- 09:00–12:00：J 看“类和对象、构造方法、this、封装”；JD 阅读对应章节。
- 15:00–18:00：建立 `Archive.java`：私有字段 `archiveNo/name/category/status/retentionYears`；写构造方法、getter/setter、`printSummary()`。
- 19:00–22:30：建立 `Day04.java`，创建 3 个 Archive 对象装入数组；断点查看每个对象的字段。
- 验收：能说“类是模板，对象是具体档案”；所有字段是 `private`，通过方法读写。

## Day 5：继承、接口、多态（只学若依会遇到的部分）

- 09:00–12:00：J 看“继承、重写、抽象类、接口”；JD 阅读同名章节。
- 15:00–18:00：建立 `BaseRecord`（公共创建人/创建时间），让 `Archive` 继承它；建立 `StatusCheckable` 接口，声明 `boolean canBorrow()`，由 Archive 实现。
- 19:00–22:30：打开项目中的 `ruoyi-common/.../BaseEntity.java`，只看字段与 getter/setter，和自己的 BaseRecord 对比；不改源码。
- 验收：能解释 `extends` 表示继承公共内容，`implements` 表示实现接口规定的能力。

## Day 6：List、Set、Map 与泛型

- 09:00–12:00：J 看“集合框架、ArrayList、HashSet、HashMap、泛型”；JD 阅读同名章节。
- 15:00–18:00：建立 `CollectionDemo.java`：List 存 5 个 Archive；Set 存档案编号并演示重复编号；Map 用编号查档案。
- 19:00–22:30：打开 `SysDictTypeController.java`，只观察 `List<SysDictType> list`。写笔记：List 里面放的是什么，`<SysDictType>` 的作用是什么。
- 验收：用自己的话区分 List（有序可重复）、Set（去重）、Map（键找值）；三个示例运行。

## Day 7：字符串、日期、异常

- 09:00–12:00：J 看“String、日期时间、异常处理”；JD 阅读同名章节。
- 15:00–18:00：用 `LocalDate` 写 `isOverdue(LocalDate dueDate)`；空档案编号时抛出 `IllegalArgumentException`。
- 19:00–22:30：用 `try/catch` 调用该方法；分别测试正常、空编号、逾期三种输入。打开项目 `ruoyi-common/.../ServiceException.java`，只读它的用途。
- 验收：能区分“正常返回 false”和“非法输入抛异常”。

## Day 8：注解、Maven 与 Java 复盘

- 09:00–12:00：J 只看“注解”概念；看项目 `backend/pom.xml` 中的 `properties/dependencyManagement/modules`，不改。
- 15:00–18:00：打开 `SysDictTypeController.java`，找到 `@RestController`、`@RequestMapping`、`@GetMapping`、`@PreAuthorize`，每个写一句“它对这个类/方法的作用”。
- 19:00–22:30：写 Java 小练习“档案借阅控制台版”：建 5 条档案、编号查找、可借才创建一条文本借阅结果。禁止 AI 直接生成完整程序。
- 验收：能解释注解是“给框架/工具的标记”；能解释 `pom.xml` 是依赖与模块配置。

## Day 9：Java 阶段测试日

- 09:00–12:00：不看新视频，完成 10 题自测：变量、条件、循环、数组、方法、类、继承、接口、集合、异常各一题。
- 15:00–18:00：将 Day 8 控制台版拆成 `Archive/ArchiveService/Day09Main` 三个类。
- 19:00–22:30：对“借阅失败”打断点，能说明字段、参数、方法返回值的变化；整理 `java-learning` 笔记。
- 通过条件：10 题中至少 8 题不查资料完成；不能完成则补对应 Day，不进入 SQL。

---

# 阶段 2：MySQL 与 SQL 基础（Day 10–16，连续 7 天）

目标：能独立设计本项目基础表，写出查询、连接、聚合、事务和索引；能读 MyBatis XML。

## Day 10：关系数据库、库表列、数据类型与 DDL

- 09:00–12:00：M 第 2–10 集；MD 阅读“创建数据库、数据类型、创建表”。
- 15:00–18:00：DBeaver 新建 `archive_learning` 库；建 `archive_category` 和 `archive_record` 两表。字段包括编号、名称、分类、状态、保管年限、物理位置、创建时间。
- 19:00–22:30：对 `archive_no` 加唯一约束；尝试插入重复编号并记录报错。打开 `backend/sql/ry_20260417.sql`，只阅读 `sys_dict_type` 的建表段。
- 验收：解释主键、外键概念、唯一约束；两张表来自自己手写 SQL。

## Day 11：INSERT、UPDATE、DELETE 与数据完整性

- 09:00–12:00：M 第 12–18 集；MD 阅读“插入、更新、删除”。
- 15:00–18:00：每张表插至少 10 条数据；更新一条状态；删除一条无关联测试数据。
- 19:00–22:30：为字段选择正确类型：编号/名称为 VARCHAR，年限/状态为 INT 或 CHAR，时间为 DATETIME；给每条 SQL 写业务注释。
- 验收：`day11_dml.sql` 至少 25 条有效 SQL，DBeaver 中确认结果。

## Day 12：SELECT、条件、排序、分页

- 09:00–12:00：M 看 DQL 基础、条件查询标题；MD 阅读 SELECT、WHERE、LIKE、ORDER BY。
- 15:00–18:00：写 12 条档案查询：按名称模糊、分类、状态、保管年限、创建日期筛选；排序和 `LIMIT` 分页。
- 19:00–22:30：打开 `SysDictTypeMapper.xml` 的 `selectDictTypeList`，逐个解释 `<if>` 会在什么条件下给 SQL 加 where；把它翻译成普通 SQL。
- 验收：能从前端查询条件写出 where SQL；12 条查询有注释和结果。

## Day 13：聚合、分组、连接

- 09:00–12:00：M 看聚合、分组、连接查询标题；MD 阅读 GROUP BY、连接。
- 15:00–18:00：新增 `archive_borrow_application`，插 10 条申请。写：每分类档案数、每状态申请数、借阅中清单、没有申请过的档案。
- 19:00–22:30：画分类→档案→借阅申请关系图。明确一对多：一个分类有多个档案，一个档案有多次申请。
- 验收：至少 4 条聚合/连接 SQL；每条能解释 join 条件。

## Day 14：表关系、外键概念、真实若依表

- 09:00–12:00：M 看实体关系标题；阅读项目 SQL 中 `sys_user`、`sys_role`、`sys_user_role`、`sys_menu`、`sys_role_menu` 的建表和插入数据。
- 15:00–18:00：DBeaver 画出“用户—角色—菜单”表关系；写 2 条连接查询：某用户拥有哪些角色、某角色有哪些菜单。
- 19:00–22:30：对比“项目中关联表的用途”和“档案借阅申请表的用途”。
- 验收：能解释多对多为何需要中间表；两条若依连接查询能运行。

## Day 15：事务与索引（只学项目第一版需要的）

- 09:00–12:00：M 看事务、索引标题；MD 阅读事务和索引。
- 15:00–18:00：在学习库写事务：插入借阅申请 + 更新档案状态；故意让第二步失败，再 `ROLLBACK`，确认无半条数据。
- 19:00–22:30：给 `archive_no`、`category_id`、`status`、申请状态/到期日设计索引；用一句话说明每个索引服务哪个查询。
- 验收：成功演示一次 commit 和一次 rollback；索引不是“每列都加”。

## Day 16：SQL 阶段测试和档案系统 ER 图

- 09:00–12:00：不看新视频，完成：建 4 表、插数据、条件查询、连接、分组、事务六项自测。
- 15:00–18:00：画第一版 ER 图（最终表见第 5 阶段），写数据字典初稿。
- 19:00–22:30：打开 `SysDictTypeMapper.xml`，将一条 XML 动态查询转换为实际 SQL 并在 DBeaver 验证。
- 通过条件：6 项至少 5 项不查资料完成；否则补 SQL，不进框架。

---

# 阶段 3：用真实若依理解框架、数据流与定位（Day 17–27）

目标：不是背概念，而是调试真实字典、用户、登录、权限流程。所有路径均对应你的项目。

## Day 17：前后端整体运行架构

- 09:00–12:00：阅读 `frontend/vite.config.js`：`port:80`、`/dev-api` 代理到 8080；阅读 `backend/ruoyi-admin/.../RuoYiApplication.java`。
- 15:00–18:00：打开前端、后端、DBeaver。浏览器 F12→Network，登录后点击“系统管理→字典管理”，记录请求 URL、方法、状态码、响应 JSON。
- 19:00–22:30：画一张图：浏览器 Vue 页面→Axios `/dev-api`→Vite proxy→Spring Controller→MyBatis→MySQL→原路返回。
- 验收：能解释为什么访问 8080 不是管理页面，以及为什么 Network 是第一定位工具。

## Day 18：第一条完整数据流——字典类型列表

- 09:00–12:00：打开以下真实文件，按顺序只阅读 `getList/listType/list/selectDictTypeList`：
  1. `frontend/src/views/system/dict/index.vue` 的 `getList()`；
  2. `frontend/src/api/system/dict/type.js` 的 `listType()`；
  3. `backend/ruoyi-admin/.../SysDictTypeController.java` 的 `list()`；
  4. `backend/ruoyi-system/.../SysDictTypeServiceImpl.java` 的 `selectDictTypeList()`；
  5. `backend/ruoyi-system/.../SysDictTypeMapper.java`；
  6. `backend/ruoyi-system/.../mapper/system/SysDictTypeMapper.xml` 的 `selectDictTypeList`；
  7. MySQL 的 `sys_dict_type`。
- 15:00–18:00：在 Controller `list()` 第一行、Service 方法、Mapper XML 查询前打断点；Debug 启动后，在页面点“搜索”。
- 19:00–22:30：F8/F7 逐步；DBeaver 同时执行同等 SQL，对比页面 `rows/total`。
- 验收：写一页“字典列表数据流”，指出每层输入和输出；能解释分页来自 `startPage()`。

## Day 19：Spring Boot 与 Spring MVC（只结合字典代码）

- 09:00–12:00：S 看“入门、配置、Web/MVC”标题；打开 `application.yml`，只找 server、profile、数据源相关配置名。
- 15:00–18:00：标注 `SysDictTypeController`：`@RestController`、`@RequestMapping`、`@GetMapping`、`@RequestBody`、`@PathVariable` 分别在哪个方法出现。
- 19:00–22:30：在 Apifox 用已登录的请求（或浏览器 Network 复制）测试列表和详情。将 HTTP 参数与 Java `SysDictType` 字段对照。
- 验收：能说 Controller 是入口，URL 是门牌号，JSON/参数如何进入 Java 对象。

## Day 20：MyBatis（Mapper、XML、动态 SQL、结果映射）

- 09:00–12:00：S 看“MyBatis”标题；逐行读 `SysDictTypeMapper.xml` 的 `resultMap`、`<sql>`、`<where>`、`<if>`、`#{}`。
- 15:00–18:00：在 DBeaver 对 `dictName/status` 各写一条实际 SQL，再改变页面条件，观察日志打印出的 SQL 和参数。
- 19:00–22:30：Mapper 接口 `selectDictTypeList` 与 XML 的同名 `id` 对照；写“Java 方法为何没有实现类”的解释。
- 验收：能定位“SQL 条件不生效”该看 XML/参数/数据库哪三个位置。

## Day 21：Service、业务校验、统一异常与事务

- 09:00–12:00：读 `SysDictTypeController.add()`、`SysDictTypeServiceImpl.checkDictTypeUnique()`、`insertDictType()`、`updateDictType()`。
- 15:00–18:00：在页面新增一个临时字典类型，再重复新增；断点观察唯一性校验返回的对象和最终错误消息。测试完删除临时数据。
- 19:00–22:30：读 `updateDictType` 的 `@Transactional`，画“改字典类型→改字典数据→改字典类型本身”的事务关系。
- 验收：能区分 Controller 的 HTTP 处理、Service 的业务规则、Mapper 的 SQL；知道重复数据不应靠前端判断。

## Day 22：用户、角色、菜单的数据库关系

- 09:00–12:00：用 Day 14 的 SQL 复习 `sys_user/sys_role/sys_user_role/sys_menu/sys_role_menu`。
- 15:00–18:00：在页面创建一个测试角色和测试用户；只授予“字典查看”权限。分别以管理员/测试用户登录并截图菜单差异。
- 19:00–22:30：执行连接 SQL，验证该用户为什么只看到对应功能；测试结束删除测试数据或明确保留为学习账号。
- 验收：解释认证是“你是谁”，授权是“你能做什么”；能从中间表查权限来源。

## Day 23：登录、JWT、Security 过滤器链

- 09:00–12:00：S 只看安全/认证相关基础概念；打开 `SysLoginController`、`SysLoginService`、`TokenService`、`JwtAuthenticationTokenFilter`，不要求一次读懂全部。
- 15:00–18:00：浏览器 Network 查看登录请求与登录后任意列表请求，观察 Authorization 请求头；不要复制或公开 Token。
- 19:00–22:30：在 `JwtAuthenticationTokenFilter` 入口、`SysLoginController.login` 打断点，各走一次登录和列表请求。
- 验收：画“登录得到 Token，后续请求携带 Token，过滤器识别用户，权限注解放行/拒绝”的流程图。

## Day 24：Redis、字典缓存、操作日志

- 09:00–12:00：读 `SysDictTypeServiceImpl.init/loadingDictCache/selectDictDataByType`；只理解“缓存优先，查不到才访问数据库”。
- 15:00–18:00：在页面“字典管理”中刷新缓存；在 IDE 断点观察缓存逻辑；不直接删除 Redis 中未知 key。
- 19:00–22:30：新增/修改一个临时字典，进入系统操作日志看记录；定位 Controller 上的 `@Log`。
- 验收：能说明 Redis 在此处缓存什么、刷新缓存解决什么；操作日志与业务日志的区别。

## Day 25：异常、拦截器、重复提交与定位策略

- 09:00–12:00：读 `GlobalExceptionHandler.java`、`RepeatSubmitInterceptor.java`，只理解全局处理和重复提交保护。
- 15:00–18:00：故意提交缺少必填字段的字典请求；分别看浏览器 Response、Apifox、后端日志。
- 19:00–22:30：写“故障定位决策表”：页面无请求→Network；401/403→登录/权限；500→后端日志/断点；数据错误→Mapper SQL/DBeaver。
- 验收：能按这个顺序定位一个模拟问题，不能凭感觉改代码。

## Day 26：代码生成器与标准 CRUD

- 09:00–12:00：阅读 RH；打开 `ruoyi-generator` 模块和生成模板目录，但不改模板。
- 15:00–18:00：在 **学习库** 创建最小 `demo_asset` 表；若依后台“系统工具→代码生成”导入、预览，逐一看 Entity/Controller/Service/Mapper/XML/Vue/API/菜单 SQL。
- 19:00–22:30：先不要集成；写“生成器生成什么，业务规则仍需手写什么”。
- 验收：能列出 8 类生成文件；理解生成器不替你设计表和业务状态。

## Day 27：框架阶段综合复盘

- 09:00–12:00：从字典页面启动一次完整 Debug；不看笔记说出 7 个节点。
- 15:00–18:00：从用户角色权限启动一次完整验证；写角色如何影响菜单和按钮。
- 19:00–22:30：完成 15 题口头自测：配置、代理、Controller、Service、Mapper、XML、事务、Token、权限、Redis、日志、异常、代码生成、Network、DBeaver。
- 通过条件：能完整解释字典列表与新增流程；否则重复 Day 18–25 的薄弱项。

---

# 阶段 4：最低限度前端（Day 28–31）

目标：前端为后端服务。能定位/修改若依的 API 和表单字段，不追求前端工程师水平。

## Day 28：HTML、CSS、JavaScript 的最小集

- 09:00–12:00：阅读 [HTML](https://www.runoob.com/html/html-tutorial.html) 的表单/表格、[CSS](https://www.runoob.com/css/css-tutorial.html) 的 class/盒模型、[JavaScript](https://www.runoob.com/js/js-tutorial.html) 的变量/对象/数组/函数。
- 15:00–18:00：写一个静态档案录入页：编号、名称、分类、保管期限、保存按钮；不接接口。
- 19:00–22:30：写一个 JS 对象和数组，控制台输出档案列表；F12 Console 调试。
- 验收：能区分 HTML 结构、CSS 样式、JS 行为。

## Day 29：Vue 3 的数据与事件

- 09:00–12:00：V 只看 Day1-02 到 Day1-10；VD 查模板、事件、响应式。
- 15:00–18:00：只读 `frontend/src/views/system/dict/index.vue`：标出 `<template>`、`<script setup>`、`ref/reactive`、`v-model`、`@click`、`v-for`。
- 19:00–22:30：在独立 Vue 练习或静态小例子中完成“表单输入→数组新增→表格显示”；不改若依。
- 验收：能解释 `v-model` 双向绑定、`@click` 事件、`ref` 响应式变量。

## Day 30：若依前端请求、路由、权限指令

- 09:00–12:00：读 `frontend/src/api/system/dict/type.js` 和 `index.vue` 中 `getList/submitForm/handleDelete`。
- 15:00–18:00：浏览器 Network 依次触发查询、新增、删除；将每个动作对照到 `listType/addType/delType`。
- 19:00–22:30：读 `v-hasPermi` 的用法，和 Day 22 后端 `@PreAuthorize` 对照。
- 验收：能说清一个“新增”按钮调用哪个 JS 函数、哪个 URL、哪个 Controller 方法。

## Day 31：前端阶段复盘与首个小改动

- 09:00–12:00：阅读菜单路由相关文件，找到“字典管理”菜单为什么能打开 `index.vue`。
- 15:00–18:00：仅改字典页面一个**显示文字或列宽**，重启/热更新后观察；不改数据逻辑。
- 19:00–22:30：用 Git 查看改动，确认只改 1 个文件后提交或撤回该学习改动。
- 验收：前端修改可定位、可验证、可撤回；不再把 Vue 页面当黑箱。

---

# 阶段 5：档案管理系统第一版设计与开发（Day 32–42）

## 第一版功能范围（锁定，不扩展）

角色：普通员工、档案管理员、系统管理员。

| 模块 | 第一版包含 | 不做 |
|---|---|---|
| 档案 | 分类、唯一档案编号、合同/项目资料/行政文件、保管期限、物理位置、状态 | 全文检索、标签设备 |
| 附件 | 上传、下载、附件元数据 | 复杂预览、版本管理 |
| 借阅 | 提交、撤销、审批/驳回、确认借出、确认归还、逾期列表 | 多级审批、跨部门调阅 |
| 权限与日志 | 复用若依角色菜单按钮权限、`@Log` 操作日志 | 自建权限框架 |
| 逾期提醒 | Dashboard 逾期数量/列表；每日 Quartz 任务更新逾期状态（后期实现） | 邮件、短信、企业微信 |

### 最终业务表（先设计，后生成）

1. `archive_category`：分类，可树形；
2. `archive_record`：档案主表，`archive_no` 唯一；
3. `archive_attachment`：电子附件元数据，关联档案；
4. `archive_borrow_application`：申请，状态为已提交/已批准/已驳回/已撤销；
5. `archive_borrow_record`：实际借出/归还记录，状态为借阅中/已归还/逾期。

不要自建操作日志表；复用若依 `@Log` 和现有系统操作日志。

## Day 32：需求、状态和权限设计

- 09:00–12:00：写 `docs/design/requirements.md`：3 个角色逐项能做/不能做什么。
- 15:00–18:00：画两条状态图：申请（提交→批准/驳回/撤销）；借阅（借出→归还 或 逾期→归还）。
- 19:00–22:30：将每个按钮映射为权限字符串，如 `archive:record:add`、`archive:borrow:approve`；不写代码。
- 验收：任何功能均能回答“谁操作、改哪张表、状态怎么变”。

## Day 33：ER 图、字段字典和建表 SQL

- 09:00–12:00：为 5 张表写字段字典：字段名、类型、是否为空、说明、索引。
- 15:00–18:00：写 `backend/sql/archive_v1.sql`，在**新建的 archive 项目数据库**执行。
- 19:00–22:30：插入每表至少 5 条模拟数据，写关键查询；检查外键/关联字段和唯一编号。
- 验收：从空库执行一次成功；ER 图与实际 SQL 一致。

## Day 34：代码生成与档案分类模块

- 09:00–12:00：在若依生成器导入 `archive_category`，预览生成代码，先逐个说明文件作用。
- 15:00–18:00：集成分类模块和菜单 SQL；启动前后端；验证列表/新增/修改/删除。
- 19:00–22:30：在 API/Controller/Service/Mapper/XML/表之间重复 Day 18 调试。
- 验收：分类 CRUD 运行，且你能解释每个生成文件。

## Day 35：档案主表模块 + 必要业务规则

- 09:00–12:00：导入 `archive_record`，生成代码，逐项检查生成的字段、查询条件、表单校验。
- 15:00–18:00：集成后实现一条手写规则：档案编号重复时拒绝新增；借阅中档案禁止删除。
- 19:00–22:30：用 Apifox 测正常新增、重复编号、借阅中删除；DBeaver 核对数据。
- 验收：至少 3 正常/失败用例；规则在 Service 层，不只在页面。

## Day 36：附件上传下载

- 09:00–12:00：阅读 `CommonController` 中已有上传/下载能力和文件配置；先画“文件本体与附件元数据”关系。
- 15:00–18:00：生成/手写 `archive_attachment` 元数据 CRUD；页面调用现有上传能力后保存附件记录。
- 19:00–22:30：测试文件类型、空附件、下载；只使用非敏感测试文件。
- 验收：一个档案能有多条附件记录；文件路径不硬编码在前端。

## Day 37：借阅申请与撤销

- 09:00–12:00：为申请模块先写接口草案与状态规则，不写页面。
- 15:00–18:00：生成 `archive_borrow_application` 基础 CRUD；在 Service 写“仅可借阅档案能申请”“申请人只能撤销自己的待审批申请”。
- 19:00–22:30：Apifox 测提交、重复申请、撤销；断点观察登录用户从哪里取得。
- 验收：普通员工只可提交/撤销自己的申请。

## Day 38：审批、借出登记与事务

- 09:00–12:00：设计批准事务：更新申请状态 + 写入借阅记录 + 更新档案状态，三步必须一起成功或失败。
- 15:00–18:00：实现管理员批准/驳回；批准时创建 `archive_borrow_record`，档案改“借阅中”。
- 19:00–22:30：在事务内制造失败并确认回滚；DBeaver 检查三张表无半成品数据。
- 验收：能解释为什么必须 `@Transactional`；审批通过三张表一致。

## Day 39：归还、逾期与提醒看板

- 09:00–12:00：实现归还确认：填实际归还日、更新借阅记录、恢复档案状态。
- 15:00–18:00：写逾期 SQL：应还日小于今天且未归还；做逾期列表与数量。
- 19:00–22:30：学习 `ruoyi-quartz` 现有任务，新增一个每日检查任务仅更新逾期状态；先在测试时间手动执行验证。
- 验收：借阅、归还、逾期三个状态可连续演示；逾期看板数据与 SQL 一致。

## Day 40：权限、日志、前后端联调

- 09:00–12:00：配置三种角色的菜单和按钮；后端关键操作加 `@PreAuthorize` 和 `@Log`。
- 15:00–18:00：逐角色跑完整流程：录入→申请→审批→借出→归还；Network、日志、DBeaver 三处验证。
- 19:00–22:30：测试越权：员工审批、管理员以外删除、撤销非本人申请。
- 验收：所有越权请求被后端拒绝；操作日志可追踪审批/归还。

## Day 41：测试、README 和项目讲解

- 09:00–12:00：建立测试表：至少 15 个场景，包含 5 个失败场景。
- 15:00–18:00：写 README：业务背景、角色、功能、技术栈、ER 图、启动、测试账号、截图、已知限制。
- 19:00–22:30：写 3 分钟讲解：表设计→申请/借阅状态→事务→权限→附件→逾期提醒。
- 验收：陌生人按 README 可理解项目；能不看稿讲完整流程。

## Day 42：GitHub、部署准备与复盘

- 09:00–12:00：检查 `.gitignore`、SQL、配置示例、截图；绝不提交密码、真实附件、Token。
- 15:00–18:00：推送 GitHub；检查 commit 只含真实设计/功能改动。
- 19:00–22:30：如果已拥有服务器，按官方部署文档分别打包后端 jar 和前端 dist；没有服务器则完成本地部署说明，不购买/开通任何服务。
- 验收：GitHub 项目完整；本地可复现启动；列出第二版候选（多级审批、全文检索、跨部门调阅），但不实现。

---

# 调试与定位固定卡（每次问题按此顺序）

| 现象 | 第一处看哪里 | 第二处 | 第三处 |
|---|---|---|---|
| 点按钮无反应 | 浏览器 F12 Network/Console | Vue 事件函数 | 前端 API 文件 |
| 404 | Network URL | 前端 API 路径 | Controller `@RequestMapping` |
| 401/403 | Network Response | 登录 Token/角色菜单 | `@PreAuthorize` 与权限字符串 |
| 500 | 后端完整日志 | Controller/Service 断点 | Mapper XML 与 DBeaver SQL |
| 数据保存不对 | Apifox 请求 JSON | Service 参数/规则 | DBeaver 表数据 |
| 查询结果不对 | 页面 query 参数 | Mapper XML 动态条件 | DBeaver 执行同等 SQL |
| 状态混乱 | Service 状态转换 | 事务是否覆盖全部修改 | 多表数据是否一致 |

## AI Agent 的正确使用时机

| 可以用 AI 的任务 | 先提供什么 | AI 输出后必须做什么 |
|---|---|---|
| 看不懂一条调用链 | 文件路径和具体方法 | 自己画输入/输出和下一跳。 |
| SQL/表设计初稿 | 业务规则、现有表 | 自己在 DBeaver 执行并检查边界。 |
| 生成重复 CRUD 骨架 | 表结构、若依版本 | 逐文件解释后再集成。 |
| 报错排查 | 完整错误、复现步骤、已检查位置 | 按最小改动验证，不能整段替换。 |
| 代码评审 | 单一功能相关文件 | 只接受与当前需求有关的建议。 |

固定提问模板：

```text
我正在 RuoYi-Vue 项目中实现【具体功能】。
现象/完整报错：【粘贴】
复现步骤：【1、2、3】
预期结果：【应发生什么】
已检查：【文件路径、SQL、接口响应】
请先说明最可能的三个原因和每个原因的验证步骤；
不要直接重写整个模块。确认原因后，再给最小改动方案。
```
