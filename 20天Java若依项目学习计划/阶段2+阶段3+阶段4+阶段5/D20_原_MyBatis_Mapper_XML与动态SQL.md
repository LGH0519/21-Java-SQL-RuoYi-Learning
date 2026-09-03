# Day 20：MyBatis：Mapper、XML、动态 SQL 与结果映射

> 今日目标：理解一条 Java Mapper 方法为何没有实现类，以及查询条件不生效时按 XML、参数、数据库三个位置定位。

## 知识点速记（完成当天任务后复习）

- Mapper 接口定义 Java 方法；同名 XML `<select id="方法名">` 写 SQL；MyBatis 在运行时生成实现。
- `resultMap` 描述查询列如何对应 Java 属性；`<sql>` 是可复用 SQL 片段。
- `<if>` 按参数决定是否加条件；`<where>` 处理动态 WHERE 和前导 AND。
- `#{}` 为预编译参数绑定；不要用 `${}` 拼用户输入。

## 视频、文档与若依对应点

- [MyBatis 官方：XML 映射文件](https://mybatis.org/mybatis-3/sqlmap-xml.html)
- [MyBatis 官方：动态 SQL](https://mybatis.org/mybatis-3/dynamic-sql.html)
- 本机：`...\ruoyi-system\src\main\java\com\ruoyi\system\mapper\SysDictTypeMapper.java`
- 本机：`...\ruoyi-system\src\main\resources\mapper\system\SysDictTypeMapper.xml`

## 本日重点、易混点与若依对应

以 `selectDictTypeList` 为例：Service 调用 Mapper 接口 → MyBatis 按 namespace 找 XML → 以同名 id 找 SELECT → 用实体/参数决定 `<if>` → JDBC 执行 SQL → resultMap 装配对象列表。SQL 条件“不生效”不等于先改页面：先确认请求参数是否到达、XML test 是否为真、日志中 SQL/参数是否符合、最终数据库是否有数据。

## 今天照做（09:00–23:00）

### 09:00–12:00｜逐项读 XML

1. IDEA 打开上方 Mapper 接口和 XML；在 XML 顶部确认 `namespace` 与接口全限定名对应。
2. 搜索 `resultMap`、`selectDictTypeVo`、`selectDictTypeList`。在笔记写每个作用各一句。
3. 对 `selectDictTypeList` 的每一个 `<if>` 写“参数何时不为空、会加入哪一段条件”。不要跳读其他 SQL。

### 15:00–18:00｜三处比对实际查询

1. 浏览器字典页只输入名称条件，查询；F12 Network 记录 Query 参数。
2. 后端以 Debug 或正常日志运行，查控制台中的 Preparing/Parameters SQL 日志。敏感信息不截图公开。
3. 在 DBeaver 的若依项目数据库，以等价 SELECT 查询 `sys_dict_type`。只执行 SELECT。
4. 再输入 status 条件重复一次。对比：请求参数、SQL 是否多一段 AND、结果行数。

### 19:00–23:00｜做定位演练

1. 假设“状态筛选不生效”，按顺序写出检查：Network 参数 → Controller 参数对象 → XML `<if test>` → SQL 日志 Parameters → DBeaver 原始数据。
2. 在 `SysDictTypeMapper.java` 的 `selectDictTypeList` 声明处、Service 调用处、Controller 调用处各加一个断点；调试一次列表请求，观察调用顺序。
3. 写一张 `Java方法 → XML id → SQL表 → 返回对象` 对照卡。

## 今日完成清单

- [ ] 找到 Mapper/namespace/XML id 的对应关系。
- [ ] 完成两组 Network—日志—DBeaver 对比。
- [ ] 能讲出动态 SQL 的四步定位顺序。

---

## 任务参考答案（完成后再查看）

答案：Java Mapper 为什么能没有实现类？MyBatis 根据接口与 XML 映射在运行时创建代理实现。`#{}` 为什么更安全？值作为参数传递，不直接把输入拼进 SQL 结构。
