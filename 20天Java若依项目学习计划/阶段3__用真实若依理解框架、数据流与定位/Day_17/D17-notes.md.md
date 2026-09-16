今日目标：**我能从端口、代理、日志判断问题属于前端、后端、Redis、MySQL 还是业务代码。**

了解：
[服务器](服务器.md)
[跨域](跨域.md)
[RuoYiApplication.java](RuoYiApplication.java.md)
[@SpringBootApplication注解](@SpringBootApplication注解.md)
[注解的内部属性](注解中的内部属性.md)
[@Bean方法](@Bean方法.md)
类对象[Xxxx.class](Xxxx.class.md)
[RuoYiApplication 关联关系流程图](RuoYiApplication 关联关系流程图)
[SpringApplication.run()函数流程](SpringApplication.run()函数流程.md)
[try关键字](try关键字.md)
[vite.config.js](vite.config.js.md)

**XHR** 是 **XMLHttpRequest** 的缩写。  
简单来说，**它代表这是一个异步网络请求**。  
当你在前端页面点击菜单或按钮时，页面并没有整体刷新，而是偷偷向后台服务器请求数据（比如获取用户列表、字典数据等），后台把数据（通常是 JSON 格式）返回给前端展示。这种在后台偷偷进行的请求，在开发者工具里就被归类为 `xhr`。
![[Pasted image 20260903172624.png]]

为什么需要 Redis？
如果 Session(会话) 信息只存放在 Spring Boot (Tomcat :8080) 的本地内存（JVM）里，就会面临两个致命问题：
服务器重启即丢失：内存是“易失”的，只要后端服务重启，所有用户都会被迫下线，需要重新登录。
多实例无法共享：当用户量大时，你需要部署多台 Tomcat 服务器。如果用户A的请求被负载均衡分发到了服务器1，建立了Session；下一次请求被分发到了服务器2，服务器2的内存里根本没有服务器1建立的Session，用户就会发现自己莫名其妙掉线了。
Redis 管理 Session 的精髓：为了解决单机内存的局限性，利用 Redis 高性能、独立部署、支持过期销毁的特性，实现会话状态的集中共享。

当 Spring Boot 接收到一个请求（比如“获取用户信息”）时，它的处理逻辑通常是这样的：
1. **先问 Redis（快）**：“嘿，你那儿有没有这个用户的资料？”
2. **如果 Redis 有**：直接返回给 Spring Boot，Spring Boot 再返回给用户。**（流程结束，非常快！）**
3. **如果 Redis 没有（缓存未命中）**：
    - Spring Boot 会**自己转身去问 MySQL**：“喂，数据库，把用户资料给我。”
    - MySQL 查询后返回数据。
    - Spring Boot 拿到数据后，**顺手**写一份到 Redis 里存着。
    - 最后把数据返回给用户。
**下一次**再有同样的请求时，Redis 里就有缓存了，就不用再去麻烦 MySQL 了。

