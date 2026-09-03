# Day 19：Spring Boot 与 Spring MVC：Controller 是 HTTP 入口

> 今日目标：只结合字典类型模块读懂“浏览器请求如何进入 Controller、参数如何成为 Java 对象、结果如何成为 JSON”。

## 知识点速记（完成当天任务后复习）

- Spring Boot 负责把应用按配置启动；入口是 `RuoYiApplication.java`。
- Spring MVC 负责 HTTP 请求到 Controller 方法的映射。
- `@RestController`：方法返回对象会以 JSON 响应；`@RequestMapping`：类/方法 URL 前缀；`@GetMapping`：GET 路由。
- `@RequestBody` 从请求 JSON 绑定对象；`@PathVariable` 读取 URL 路径变量；`@RequestParam`/对象字段可接收查询参数。

## 视频、文档与若依对应点

- [Spring Boot 官方 Web 入门](https://spring.io/guides/gs/rest-service/)：看 Controller、GET、JSON 示例。
- 本机：`E:\WorkSpace\ruoyi-archive-management\backend\ruoyi-admin\src\main\java\com\ruoyi\RuoYiApplication.java`
- 本机：`...\ruoyi-admin\src\main\java\com\ruoyi\web\controller\system\SysDictTypeController.java`

## 本日重点、易混点与若依对应

一次列表请求可理解为：浏览器 GET `/system/dict/type/list?...` → Spring 找 URL 匹配的 `list` → Java 读取参数、调用 Service → `TableDataInfo` 被序列化为 JSON。Controller 不是 SQL 执行位置；它是协议边界，负责接收 HTTP、触发业务、返回 HTTP 格式结果。

易混点：URL 前缀通常由类上的 `@RequestMapping` 与方法上的 `@GetMapping` 拼出。HTTP 200 只表示请求被服务器处理成功，不必然代表业务允许；业务失败可能仍在 JSON 的 code/message 中体现。

## 今天照做（09:00–23:00）

### 09:00–12:00｜找启动配置与入口

1. IDEA 打开 backend 项目。按 `Ctrl+N` 搜索 `RuoYiApplication`，打开后只看类头注解和 `main` 方法。
2. 打开 `backend\ruoyi-admin\src\main\resources\application.yml`；搜索 `server:`、`port:`、`spring:`、`datasource`。只记录配置名和值，不改任何行。
3. 在浏览器打开 `http://localhost:8080`。看到“请通过前端地址访问”是正常的：8080 是后端接口端口，不是 Vue 管理页面。

### 15:00–18:00｜标注字典 Controller

1. IDEA 打开 `SysDictTypeController.java`；在笔记建立表：注解、出现位置、作用、页面动作。
2. 用 `Ctrl+F` 依次找 `@RestController`、`@RequestMapping`、`@GetMapping`、`@PostMapping`、`@PathVariable`、`@RequestBody`。有的注解可能不在每个方法出现，以文件实际内容为准。
3. 打开前端登录页，进入“系统管理→字典管理”。F12 → Network，点击查询，选择列表请求，记录 Request URL、Method、Query String、Response 的 `rows/total`。
4. 将 Network 的路径与 Controller 的类/方法映射拼起来写在笔记。

### 19:00–23:00｜用 Apifox/浏览器验证参数绑定

1. 不手工猜登录 Token。Network 中右键成功的列表请求，选择“复制为 cURL”或导入 Apifox；保留认证信息在本机，不粘贴到学习文档/聊天中。
2. 在 Apifox 执行一次列表请求；仅改变一个名称或状态查询参数，再执行一次。
3. 返回 IDEA，在 `list` 方法第一行打断点，Debug 启动后重新查询。观察方法参数对象的字段值。
4. 截图：Network 请求、断点参数、JSON 响应。若断点不进，确认运行的是 Debug 而非 Run、URL/端口与当前后端一致。

## 今日完成清单

- [ ] 找到启动类和 application.yml 的端口配置。
- [ ] 标注了字典 Controller 的 5 类注解。
- [ ] 用 Network/Apifox 做了一次列表验证并进过断点。

---

## 任务参考答案（完成后再查看）

答案：Controller 的核心职责？把 HTTP 请求适配为 Java 调用并返回 HTTP/JSON；不是堆放全部业务规则或 SQL。
