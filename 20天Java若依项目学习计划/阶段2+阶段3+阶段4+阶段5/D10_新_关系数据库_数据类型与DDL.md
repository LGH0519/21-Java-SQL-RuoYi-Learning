# Day 10：关系数据库、数据类型与 DDL

> **今日唯一目标：** 在 DBeaver 从零建立 archive_learning 学习库和 archive_category、archive_record 两表，能解释每个字段和约束。
>
> **今天不做：** 不改若依已有 sys_ 表，不删除已有数据库。 只完成本日主题；验收未通过就停在当天补练，不用“先往后做”掩盖缺口。

## 知识点速记（完成当天任务后用于复习）

- **库、表、行、列：**库是数据集合；表保存同类实体；行是一条记录；列是字段。
- **主键/唯一/外键：**id 主键定位行；archive_no 唯一保护业务编号；category_id 表示分类关联。
- **DDL：**CREATE 定义库表，ALTER 改结构，DROP 删除对象；今天只使用 CREATE。
- **类型：**编号/名称用 VARCHAR，年限用 INT，状态短码用 CHAR(1)，时间用 DATETIME。

### 本日必须形成的业务映射

档案分类是一张表；一份档案是一行 archive_record；分类 id 将在后续作为档案 category_id 的关联值。

### 本日重点、易混点与易面试点

`id` 与 `archive_no` 都可唯一但职责不同；VARCHAR 不是数字；外键概念不等于今天必须给学习表加 FOREIGN KEY；DROP 是破坏操作，表已存在时先查结构。

## 视频、文档与若依对应点

| 资源/文件 | 今天只看、只做这些 |
|---|---|
| [菜鸟 MySQL 数据类型](https://www.runoob.com/mysql/mysql-data-types.html) | 只看字符串、数值、日期类型 |
| [菜鸟 CREATE TABLE](https://www.runoob.com/mysql/mysql-create-tables.html) | 看 CREATE、PRIMARY KEY、UNIQUE |
| `backend\sql\ry_20260417.sql` | 搜索并只读 `sys_dict_type` 建表段 |

### 看资料的固定方法

1. 每看 10–15 分钟，暂停一次，用自己的话写“这段解决哪个问题”。不要只开着视频播放。
2. 遇到术语，先在本日“知识点速记”找定义；仍不懂才查指定文档，不同时打开十个网页。
3. 对代码文件只读指定方法：先看输入、输出、下一跳；不要从目录第一行开始硬读。
4. 每个操作只保留一个证据：SQL 结果、IDEA 断点、Network 请求或截图。证据要能说明结论。

## 今天只完成什么

完成三个时段的操作、一个学习记录和下方勾选项。学习记录保存在项目根目录 docs\learning\day-10\（目录不存在时自行新建），至少放：
otes.md、一张证据截图、一个本日脚本/代码文件。**不要**将密码、Token、真实单位数据、真实附件放入记录或 Git。

## 今天照做（09:00–23:00）

### 开始前检查（09:00–09:20）

1. 打开资源管理器，进入 E:\WorkSpace\ruoyi-archive-management；确认今天是只读学习、学习库操作，还是已明确允许的业务开发日。
2. 新建 docs\learning\day-10\notes.md，写入四行：日期、今日主题、开始前状态、预计交付物。
3. 运行 git status。若显示不是你今天产生的改动，截图记录后不要触碰；不要使用 git reset --hard。
4. 按今天资源启动需要的工具：Java/框架日用 IDEA；SQL 日用 DBeaver；接口观察用浏览器 F12/Apifox。工具打不开先看“卡住时”而不是改项目配置。

### 09:20–12:00：理解本日概念并建立最小证据

1. 打开 DBeaver，双击 MySQL 连接，右键连接 → SQL 编辑器 → 新建 SQL 脚本；另存为 `day10_ddl.sql`。
2. 输入并执行 `CREATE DATABASE IF NOT EXISTS archive_learning DEFAULT CHARACTER SET utf8mb4;`，再执行 `USE archive_learning;`；左侧右键刷新确认数据库出现。
3. 在 notes.md 画四层：库→表→行→列，并写 `ARC-2026-001` 为什么是 VARCHAR。

### 15:00–18:00：按步骤完成练习或项目操作

1. 在脚本中逐行输入 archive_category：id BIGINT AUTO_INCREMENT 主键、category_name VARCHAR(100) 非空唯一、status CHAR(1) 默认 0、create_time DATETIME 默认当前时间。
2. 再输入 archive_record：id、archive_no VARCHAR(64) 非空唯一、archive_name VARCHAR(200)、category_id BIGINT、status、retention_years INT、physical_location VARCHAR(200)、create_time。每行加 COMMENT。
3. 执行后在左侧 表 → 刷新；双击 archive_record → 列，核对类型、非空、主键、uk_archive_no。

### 19:00–23:00：验证、调试、复盘与安全使用 AI

1. 插入分类 `合同档案` 和档案 `ARC-2026-001`；执行 SELECT * FROM archive_record 保存结果。
2. 再次插入同 archive_no，预期出现 Duplicate entry；截图并在 notes 写“约束在数据库层拒绝重复”。
3. 打开 ry_20260417.sql 对比 sys_dict_type 的字段注释/状态设计；只读，不执行该文件。

### 本日调试固定动作

1. **Java/后端问题：** 先看 IDEA Run/Debug 控制台第一段完整异常；在入口方法第一行打断点，F8 单步、F7 进入、F9 继续。不要只看最后一行报错。
2. **页面/接口问题：** 浏览器按 F12 → Network → 勾选 Preserve log → 触发一次动作 → 记录 URL、Method、Status、Response。无请求先检查 Console、事件函数和 API 文件。
3. **数据问题：** 复制请求条件（不要复制 Token），在 DBeaver 用同等 SELECT 验证。写入操作须比较操作前后两次查询结果。
4. **AI 使用边界：** 先把“现象、复现、预期、已查文件/SQL”写进 notes；要求 AI 先列原因和验证步骤。确认原因后才接受最小改动；每次改动都看 Git diff、编译/启动、接口、数据库四项。

## 深度执行补充：每个结论都要留证据

### 1. 本日学习记录的逐项填写法

在 
otes.md 依次建立下列小节，不要只写“今天学了什么”。每一项至少写两句，写不出来就说明尚未真正理解：

`md
## 1. 今日对象
- 本日核心对象/文件/表：
- 它接收什么输入：
- 它产生什么输出：

## 2. 一条真实路径
1. 我从哪里开始操作：
2. 下一跳到哪里：
3. 最终结果在哪里看见：

## 3. 一个失败分支
- 我故意怎样构造边界：
- 正确结果应当是什么：
- 实际证据（截图/日志/SQL）：

## 4. 今日仍不懂的一个点
- 我已查的位置：
- 下一次只要验证的一个问题：
`

### 2. 对照验证的四步法

1. **输入证据：**页面操作时记录表单值；SQL 练习记录完整 WHERE；代码练习记录测试输入。不要用“应该传了”代替证据。
2. **过程证据：**接口类题在 Network/断点中确认参数；SQL 类题在执行历史中确认语句实际运行；设计类题在 ER/字段字典中确认每个关系有字段支撑。
3. **输出证据：**查询看行数和关键列；写入看操作前后 SELECT；代码看控制台输出和变量；权限看拒绝状态及消息。
4. **反证：**至少改变一个条件再执行一次，例如换编号、清空条件、换角色、把状态切到非法值。若结果仍不变，先停下查原因。

### 3. 按工具点击的最小流程

| 工具 | 从哪里开始 | 必看信息 | 正确结束条件 |
|---|---|---|---|
| IDEA | Project 文件树 → 双击文件 → 行号左侧单击打断点 → Debug | Variables、Call Stack、控制台完整异常 | 能说出当前方法参数、返回值和下一跳。 |
| DBeaver | 连接名 → SQL 编辑器 → 新建脚本 → Ctrl+Enter | 当前连接/数据库、影响行数、结果表格 | 操作前后 SELECT 对比，脚本已保存。 |
| 浏览器 | F12 → Network → Preserve log → 触发一次操作 | Request URL、Method、Payload/Query、Status、Response | 能把请求对应到 API 与 Controller。 |
| Apifox | 从成功 Network 请求导入/复制 → 检查 URL/方法/参数 → Send | 环境地址、请求体、响应 code/message | 只用测试数据完成一个成功与失败请求。 |
| Git | 项目根目录 → git status → git diff -- 文件 | 是否有无关文件、每行变化原因 | 只保留本日已验证的改动，绝不重置全仓。 |

### 4. 本日失败分支的最低要求

今天必须选择一个与主题相关的失败分支。SQL 日可用重复编号、空条件、回滚；接口日可用必填缺失、权限不足或非法状态；前端日可用空输入/错误事件定位；设计日可用“字段类型或状态不一致”的审查。失败分支的目的不是破坏环境，而是证明规则真正有效。测试完的临时数据必须按当日规则清理或明确标注保留用途。

### 5. 当天结束前的五个复盘题

1. 今天最重要的一个名词是什么？请用档案项目举例定义它。
2. 今天一个正常动作的输入、处理、输出各是什么？
3. 如果结果不对，第一处、第二处、第三处分别查哪里？
4. 今天哪条规则必须由后端/数据库保证，为什么前端不够？
5. 明天会在哪个位置复用今天的知识？

回答必须写在 notes.md；不能回答的题目不是“记忆不好”，而是明天开始前需要补练的清单。

## 今日量化验收

- [ ] archive_learning 与两张表可在 DBeaver 看见
- [ ] 手写 DDL 脚本可保存并从头执行
- [ ] 重复 archive_no 失败且有截图
- [ ] 能说出主键、唯一、逻辑关联各自作用

### 今日通过标准

不查资料能手写两张表的核心字段与 archive_no UNIQUE；能用字段类型解释一个业务原因。

## 卡住时只按这个顺序检查

1. 确认本日使用的是正确目录、正确数据库连接和正确端口；不要因为报错就立即换版本或升级依赖。
2. 看本日操作的上一条是否真的成功：SQL 是否执行、服务是否启动、页面是否刷新、文件是否保存。
3. 只对照本日指定文件/命令；若结果不同，记录完整提示和截图。
4. 向 AI 或我提问时使用：今天是 Day 10，我在【步骤号】。现象是【完整提示】；预期是【结果】；我已检查【文件/SQL/Network】。请先给 3 个原因和逐项验证方法，不要重写模块。

---

## 任务参考答案（完成后再查看）

参考 DDL 的核心不是照抄表名，而是：主键 `id BIGINT NOT NULL AUTO_INCREMENT`；业务编号 `archive_no VARCHAR(64) NOT NULL` 加 UNIQUE；分类关系列 `category_id BIGINT`；时间 `DATETIME`。表已存在时先 `SHOW CREATE TABLE archive_record;`，不能直接 DROP。

**完成后复述：** 档案编号是业务识别码，必须用 VARCHAR 并加唯一约束；id 是数据库内部主键。
