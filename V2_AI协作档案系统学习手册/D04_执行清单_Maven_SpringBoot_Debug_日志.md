# D04 今天照做执行清单：Maven、启动、Debug、日志

## 上午：Maven 与配置只读（09:00–12:00）

1. [ ] 新建 `D04-notes.md`，写：`properties`、`dependencyManagement`、`modules`、`启动`、`日志`。
2. [ ] 打开根 `backend/pom.xml`；Ctrl+F 搜 `<properties>`，写其中 Java 和项目版本属性名，不修改。
3. [ ] 搜 `<dependencyManagement>`，写“统一依赖版本”。
4. [ ] 搜 `<modules>`，写出 `ruoyi-admin`、`ruoyi-system`、`ruoyi-common`。
5. [ ] 打开 `application.yml`；仅搜索 `port:`、`redis:`，写端口，不抄任何密码。

## 下午：启动与日志（15:00–18:00）

1. [ ] 右键 `RuoYiApplication.java` → **Run**。
2. [ ] 在 Run 面板 Ctrl+F 搜 `Started RuoYiApplication`；记录启动秒数。
3. [ ] 浏览器打开 `http://localhost:8080`，写下显示内容的含义。
4. [ ] 若失败，向上找第一个 `Caused by:`；复制异常类型和非敏感消息到笔记，停止，不改配置。

## 晚上：三证据定位（19:00–23:00）

1. [ ] 在字典 Controller Service 调用行打断点，Shift+F9 Debug。
2. [ ] 浏览器刷新列表；Variables 记录参数和 list 大小。
3. [ ] F7 到 Service，F8 继续，F9 回页面。
4. [ ] F12 Network 打开同一请求，记录状态码与 rows 数量。
5. [ ] 在笔记画三行：`日志证明启动`、`断点证明后端执行`、`Network 证明浏览器响应`。

## 收尾验收

- [ ] 能解释 Maven、JDK、Spring Boot 启动类不是一回事。
- [ ] 有一条“第一条根因”排查规则。
- [ ] 有日志/断点/Network 三种证据。
