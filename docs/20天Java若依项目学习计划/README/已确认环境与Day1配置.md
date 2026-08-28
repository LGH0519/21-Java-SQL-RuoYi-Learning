# 已确认环境与 Day 1 配置

## 已有工具（无需重复安装）

| 工具 | 当前状态 | Day 1 要做什么 |
|---|---|---|
| IntelliJ IDEA 试用版 | 已安装 | 用它打开后端根目录和调试 Java；试用到期前导出设置/确认替代方案。 |
| JDK 17 | 已安装 | 只用于与项目分支匹配；不要随意降级或升级。 |
| MySQL 8.0 | 已安装 | 用 DBeaver 连接，创建学习库和项目库。 |
| Redis | 已安装 | 确认服务已启动；暂不学习 Redis 命令。 |
| Node | 已安装 | 仅用于启动/构建 `ruoyi-ui`。 |
| Git | 已安装 | 每天两次提交、推送 GitHub。 |
| DBeaver | 已安装 | 写 SQL、查看表、核对接口写入结果。 |
| Apifox | 已安装 | 保存接口请求、响应和失败用例。 |
| RuoYi-Vue | 已拉取且能运行 | 作为档案管理主项目基础。 |

## Day 1 的 20 分钟兼容性检查

唯一还需要确认的是：你拉取的 RuoYi-Vue 使用哪个 Spring Boot 分支。官方当前说明中，`springboot3` 分支要求 JDK 17+，`springboot2` 分支要求 JDK 8+；因此不要因为本机有 JDK 17 就直接改项目版本。

1. 在后端根目录打开 `pom.xml`，搜索 `java.version`、`spring-boot.version` 或 `parent`。
2. 在终端运行 `git branch --show-current`，记录当前分支名称。
3. 在 IDEA：`File → Project Structure → Project SDK`，确认当前 SDK 为 17。
4. 用当前配置运行一次后端。若本来可以运行，视为兼容；不要为“理论版本”破坏已能运行的环境。
5. 前端根目录执行一次项目原有启动命令；在浏览器登录后，F12 打开 Network，确认有 API 请求返回。

记录格式：

```text
RuoYi-Vue 分支：
pom 中 Java/Spring Boot 版本：
IDEA Project SDK：17
后端启动：成功/失败（完整错误）：
前端启动：成功/失败（完整错误）：
```

## Day 1 中应跳过的内容

- 跳过所有工具下载与安装步骤。
- 不新建第二套 Java/Maven 环境。
- 不升级 RuoYi-Vue、Node、MySQL 或 Redis。
- 不碰正式项目数据库；Day 1 的 SQL 练习只放在 `study_java` 库。

## IDEA 30 天试用的处理

当前计划前 20 天以内，试用期足够。第 12 天把项目的快捷键、运行配置、调试习惯固定下来；若试用结束，再根据你自己的授权选择继续使用或切换到可用的 Java IDE。不要为了规避授权而使用非正规方式。
