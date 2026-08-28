## ruoyi-common/pom.xml 完整解析文档

---

## 前置知识：ruoyi-common 在整个项目中的定位

> 如果说 ruoyi-admin 是"前台大门"，ruoyi-framework 是"安保部"，ruoyi-system 是"人力资源部"，ruoyi-quartz 是"保洁队"，ruoyi-generator 是"文印室"，那 **ruoyi-common 就是"后勤仓库"**——所有部门都需要从它这里领取工具。
>
> 它是整个项目中**依赖第三方库最多的模块**——因为它要把所有通用工具集中管理，供其他模块共享使用。
>
> 同时它也是**依赖链的最底层**——不依赖任何内部模块，但被所有其他内部模块依赖。就像地基一样，所有楼层都建在它上面。

---

## 第一部分：文件头、父模块声明与描述（第 1-16 行）

### 对应代码

```xml
<?xml version="1.0" encoding="UTF-8"?>
<project xmlns="http://maven.apache.org/POM/4.0.0"
         xmlns:xsi="http://www.w3.org/2001/XMLSchema-instance"
         xsi:schemaLocation="http://maven.apache.org/POM/4.0.0 
         http://maven.apache.org/xsd/maven-4.0.0.xsd">
    <parent>
        <artifactId>ruoyi</artifactId>
        <groupId>com.ruoyi</groupId>
        <version>3.9.2</version>
    </parent>
    <modelVersion>4.0.0</modelVersion>

    <artifactId>ruoyi-common</artifactId>

    <description>
        common通用工具
    </description>
```


### 逐行解释

- **第 1-4 行**：XML 文件头，固定模板。
- **第 5-9 行** `<parent>`：继承根 pom.xml。
- **第 10 行** `<modelVersion>4.0.0</modelVersion>`：固定值。
- **第 12 行** `<artifactId>ruoyi-common</artifactId>`：模块名称。
- **第 14-16 行** `<description>`：描述——"common通用工具"。
- **没有 `<packaging>`**：默认 `jar`，库模块。
- **没有 `<build>`**：继承根 pom 编译配置。

> 和其他非入口模块的开头完全一样——标准模板。

### 📝 通俗总结

> 标准子模块开头——认爹、报名字、写描述。**永远不需要修改**。

---

## 第二部分：依赖声明（第 18-121 行）

### 对应代码

```xml
<dependencies>
    <!-- 14 个依赖 -->
</dependencies>
```


### 整体说明

ruoyi-common 引入了 **14 个依赖**，全部是第三方库，没有引入任何内部模块——因为它是依赖链的最底层，没有更底层的内部模块可以依赖了。

> 类比：后勤仓库（ruoyi-common）不需要从其他部门领取东西——它自己就是所有工具的源头。它直接从外部供应商（第三方库）采购所有物资，然后分发给其他部门。

### 依赖一：spring-context-support（第 20-24 行）

```xml
<!-- Spring框架基本的核心工具 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-context-support</artifactId>
</dependency>
```


- **spring-context-support**：Spring 框架的"上下文支持包"。
  - **Spring 上下文（Context）是什么？** 可以理解为 Spring 的"大脑"——它负责管理所有 Java 对象（Bean）的创建、销毁、依赖注入。`spring-context-support` 提供了一些额外的工具类，比如邮件发送、缓存抽象、任务调度等支持。
  - 类比：如果 Spring 框架是一个"智能管家"，那 context-support 就是管家工具箱里的"多功能瑞士军刀"——不是每次都用，但需要时很方便。

### 依赖二：spring-web（第 26-30 行）

```xml
<!-- SpringWeb模块 -->
<dependency>
    <groupId>org.springframework</groupId>
    <artifactId>spring-web</artifactId>
</dependency>
```


- **spring-web**：Spring 的 Web 基础模块。提供 HTTP 请求/响应处理、文件上传、REST 模板等基础功能。
- 注意：这是 `spring-web`，不是 `spring-boot-starter-webmvc`。前者是基础库，后者是包含了前者的"全家桶"。ruoyi-common 只需要基础能力就够了，完整的 Web 能力由 ruoyi-framework 引入。
- 类比：spring-web 是"螺丝刀"，spring-boot-starter-webmvc 是"整套工具箱"。仓库里只需要放螺丝刀，整套工具箱放在安保部（framework）那里。

### 依赖三：spring-boot-starter-security（第 32-36 行）

```xml
<!-- spring security 安全认证 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-security</artifactId>
</dependency>
```


- **Spring Security**：Spring 官方的安全框架。负责两件事：
  1. **认证（Authentication）**：验证"你是谁"——用户登录时检查用户名密码是否正确
  2. **授权（Authorization）**：验证"你能做什么"——检查用户是否有权限访问某个接口
- 类比：Spring Security 就是大楼的"门禁系统"——刷卡（认证）确认你是员工，然后检查你的门禁卡等级（授权）决定你能进哪些房间。
- 为什么放在 ruoyi-common 而不是 ruoyi-framework？因为安全认证是**所有模块都需要的基础能力**——放在 common 中，所有模块都能共享。

### 依赖四：pagehelper-spring-boot-starter（第 38-42 行）

```xml
<!-- pagehelper 分页插件 -->
<dependency>
    <groupId>com.github.pagehelper</groupId>
    <artifactId>pagehelper-spring-boot-starter</artifactId>
</dependency>
```


- **PageHelper**：MyBatis 的分页插件，在根 pom.xml 中已介绍过。
- 放在 ruoyi-common 中，因为分页是**所有业务查询都需要的基础功能**——用户列表要分页、角色列表要分页、日志列表要分页……放在 common 中，所有模块都能用。

### 依赖五：spring-boot-starter-validation（第 44-48 行）

```xml
<!-- 自定义验证注解 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-validation</artifactId>
</dependency>
```


- **validation（数据校验）**：Spring 的数据校验框架。
  - 比如用户注册时，用户名不能为空、密码长度至少 6 位、邮箱格式要正确——这些校验规则用注解写在 Java 代码上（如 `@NotBlank`、`@Size`、`@Email`），Spring 自动帮你检查。
  - 类比：就像填表时的"必填项标记"——表格上标了 `*` 的字段必须填，不填就提交不了。validation 就是帮你自动检查这些"必填项"的工具。

### 依赖六：commons-lang3（第 50-54 行）

```xml
<!-- 常用工具类 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-lang3</artifactId>
</dependency>
```


- **Commons Lang3**：Apache 的 Java 语言增强工具库。
  - Java 自带的字符串、数字、日期处理功能比较基础，Commons Lang3 提供了大量便捷方法，比如：判断字符串是否为空（`StringUtils.isEmpty()`）、对象转字符串（`ToStringBuilder`）、日期格式化等。
  - 类比：Java 自带的工具像"基础款文具"——只有铅笔和橡皮；Commons Lang3 像"高级文具套装"——多了尺子、圆规、荧光笔，干活更方便。

### 依赖七：jackson-databind（第 56-60 行）

```xml
<!-- JSON工具类 -->
<dependency>
    <groupId>tools.jackson.core</groupId>
    <artifactId>jackson-databind</artifactId>
</dependency>
```


- **Jackson**：Java 最流行的 JSON 处理库。负责 Java 对象和 JSON 之间的互相转换（序列化/反序列化）。
- 注意：项目中同时有 Jackson 和 FastJSON2 两个 JSON 库。Jackson 是 Spring Boot 默认使用的，FastJSON2 是若依额外引入的（性能更好）。两者共存，各取所长。
- 类比：Jackson 是"官方翻译官"（Spring 默认用），FastJSON2 是"特聘翻译官"（速度更快）。两个翻译官都在仓库里，看哪个方便用哪个。

### 依赖八：fastjson2（第 62-66 行）

```xml
<!-- 阿里JSON解析器 -->
<dependency>
    <groupId>com.alibaba.fastjson2</groupId>
    <artifactId>fastjson2</artifactId>
</dependency>
```


- **FastJSON2**：阿里巴巴的 JSON 解析器，在根 pom.xml 中已详细介绍过。

### 依赖九：commons-io（第 68-72 行）

```xml
<!-- io常用工具类 -->
<dependency>
    <groupId>commons-io</groupId>
    <artifactId>commons-io</artifactId>
</dependency>
```


- **Commons IO**：Apache 的文件操作工具库，在根 pom.xml 中已介绍过。

### 依赖十：poi-ooxml（第 74-78 行）

```xml
<!-- excel工具 -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
</dependency>
```


- **Apache POI**：Excel 操作库，在根 pom.xml 中已介绍过。

### 依赖十一：jjwt（第 80-84 行）

```xml
<!-- Token生成与解析 -->
<dependency>
    <groupId>io.jsonwebtoken</groupId>
    <artifactId>jjwt</artifactId>
</dependency>
```


- **JJWT**：JWT 令牌工具库，在根 pom.xml 中已介绍过。

### 依赖十二：jaxb-api（第 86-90 行）

```xml
<!-- Jaxb -->
<dependency>
    <groupId>javax.xml.bind</groupId>
    <artifactId>jaxb-api</artifactId>
</dependency>
```


- **JAXB API**：Java 对象与 XML 互转的工具，在根 pom.xml 中已介绍过。

### 依赖十三：spring-boot-starter-data-redis（第 92-96 行）

```xml
<!-- redis 缓存操作 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-data-redis</artifactId>
</dependency>
```


- **Spring Data Redis**：Spring 对 Redis 缓存的集成。
  - **Redis 是什么？** 一个"内存数据库"——数据存在内存里而不是硬盘上，读写速度极快。常用来做缓存（把频繁查询的数据暂存在内存里，下次查询直接从内存拿，不用再去数据库查）。
  - 类比：数据库（MySQL）像"档案室"——东西存在柜子里，取出来慢但容量大；Redis 像"办公桌"——东西放在手边，取出来快但容量小。常用的文件放办公桌上（Redis 缓存），不常用的放档案室（MySQL 数据库）。

### 依赖十四：spring-boot-starter-cache（第 98-101 行）

```xml
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-cache</artifactId>
</dependency>
```


- **Spring Cache**：Spring 的缓存抽象层。
  - 它提供了一套统一的注解（`@Cacheable`、`@CacheEvict`、`@CachePut`），让你用注解就能给方法加缓存，不需要关心底层用的是 Redis 还是其他缓存。
  - 类比：Spring Cache 是"遥控器"，Redis 是"空调"。你按遥控器（`@Cacheable` 注解）就能控制空调（Redis 缓存），不需要知道空调内部怎么工作的。
  - 它和上面的 `spring-boot-starter-data-redis` 配合使用——Cache 提供"操作界面"（注解），Redis 提供"存储空间"。

### 依赖十五：commons-pool2（第 103-107 行）

```xml
<!-- pool 对象池 -->
<dependency>
    <groupId>org.apache.commons</groupId>
    <artifactId>commons-pool2</artifactId>
</dependency>
```


- **Commons Pool2**：Apache 的对象池化库。
  - **对象池是什么？** 和数据库连接池类似——预先创建一批对象放在池子里，用的时候从池子里拿，用完放回去，避免频繁创建和销毁对象的开销。
  - 这里主要是给 Redis 客户端（Lettuce）用的——Lettuce 需要对象池来管理 Redis 连接。
  - 类比：对象池就像"共享单车停放点"——单车预先停在那里，你骑走一辆，用完还回来。不用每次都去买一辆新车（创建对象）。

### 依赖十六：yauaa（第 109-113 行）

```xml
<!-- 解析客户端操作系统、浏览器等 -->
<dependency>
    <groupId>nl.basjes.parse.useragent</groupId>
    <artifactId>yauaa</artifactId>
</dependency>
```


- **YAUAA**：浏览器 User-Agent 解析库，在根 pom.xml 中已介绍过。

### 依赖十七：jakarta.servlet-api（第 115-119 行）

```xml
<!-- servlet包 -->
<dependency>
    <groupId>jakarta.servlet</groupId>
    <artifactId>jakarta.servlet-api</artifactId>
</dependency>
```


- **Jakarta Servlet API**：Java Web 的 Servlet 规范接口。
  - **Servlet 是什么？** Java Web 处理 HTTP 请求的"标准接口"。所有 Java Web 框架（Spring MVC 等）都基于 Servlet 规范。
  - `jakarta` 前缀说明这是新版（Java EE 改名为 Jakarta EE 后的版本）。旧版叫 `javax.servlet`。
  - 注意：ruoyi-framework 中用 `<exclusions>` 排除了 kaptcha 自带的旧版 `javax.servlet:servlet-api`，就是为了避免和这个新版 `jakarta.servlet:jakarta.servlet-api` 冲突。
  - 类比：Jakarta Servlet API 是"USB-C 接口标准"（新版），javax.servlet 是"老式 USB-A 接口"（旧版）。项目统一用 USB-C，所以要把老式接口排除掉。

### 与其他模块 pom.xml 的对比

| 模块 | 依赖总数 | 内部模块依赖 | 第三方库依赖 |
|------|---------|------------|------------|
| ruoyi-admin | 6 | 3 个 | 3 个 |
| ruoyi-framework | 6 | 1 个 | 5 个 |
| ruoyi-system | 1 | 1 个 | 0 个 |
| ruoyi-quartz | 2 | 1 个 | 1 个 |
| ruoyi-generator | 3 | 1 个 | 2 个 |
| **ruoyi-common** | **17** | **0 个** | **17 个** |

> ruoyi-common 是依赖最多的模块（17 个），但全部是第三方库——它不依赖任何内部模块。这完美体现了它"最底层地基"的定位：所有外部工具先集中到 common，然后其他模块通过依赖 common 间接获得这些工具。

### 可修改项分析

#### 新增一个第三方库（以引入邮件发送为例）

```xml
<!-- 在 </dependencies> 前新增 -->
<dependency>
    <groupId>org.springframework.boot</groupId>
    <artifactId>spring-boot-starter-mail</artifactId>
</dependency>
```


**影响：**
- 所有模块都能使用邮件发送功能（因为都依赖 ruoyi-common）
- 还需要在 `application.yml` 中配置 SMTP 服务器信息

**实际产品开发中需要改吗？** ✅ **经常需要。** 当项目需要新的通用能力时（如邮件发送、短信发送、文件存储等），优先放在 ruoyi-common 中，让所有模块共享。

#### 删除某个依赖（以删除 poi 为例）

```xml
<!-- 修改前：有 poi -->
<dependency>
    <groupId>org.apache.poi</groupId>
    <artifactId>poi-ooxml</artifactId>
</dependency>

<!-- 修改后：删掉 -->
```


**影响：**
- 所有模块的 Excel 导入导出功能失效
- 若依的 `ExcelUtil` 工具类编译报错
- **外观界面：** "导出"按钮点击后报错

**实际产品开发中需要改吗？** ⚠️ **看情况。** 如果项目完全不需要 Excel 功能，可以删除。但大多数管理系统都需要，所以一般保留。

### 📝 通俗总结

> ruoyi-common 的 `<dependencies>` 区域引入了 **17 个第三方库**，是整个项目中依赖最多的模块。这些依赖按功能可以分为几大类：
>
> - **安全认证类**：Spring Security、JWT、JAXB
> - **数据处理类**：Jackson、FastJSON2、PageHelper、Validation
> - **缓存类**：Redis、Spring Cache、Commons Pool2
> - **工具类**：Commons Lang3、Commons IO、YAUAA
> - **文件操作类**：POI（Excel）
> - **Web 基础类**：Spring Web、Spring Context Support、Jakarta Servlet API
>
> 它不依赖任何内部模块——因为它是依赖链的最底层。所有其他模块（system、framework、quartz、generator）通过依赖 ruoyi-common 间接获得了这 17 个库的能力。
>
> 这个文件在实际开发中**偶尔需要修改**——当你需要给项目引入新的通用能力时（如邮件发送、短信、对象存储等），优先放在 ruoyi-common 中。

---

## 第三部分：构建配置（无）

ruoyi-common **没有 `<build>` 区域**——和其他非入口模块一样，完全继承根 pom 的编译配置。

> 再次印证规律：只有 ruoyi-admin 有自己的构建配置。

---

## 第四部分：与其他模块 pom.xml 的关系

### 在依赖链中的位置

```
ruoyi-admin → ruoyi-framework → ruoyi-system → 【ruoyi-common】
ruoyi-admin → ruoyi-quartz    →              【ruoyi-common】
ruoyi-admin → ruoyi-generator →              【ruoyi-common】
                                                    ↑ 当前位置
```


ruoyi-common 处于依赖链的**最底层**——所有其他内部模块都直接或间接依赖它。

### 谁依赖了 ruoyi-common？

| 模块 | 依赖方式 | 说明 |
|------|---------|------|
| ruoyi-system | 直接依赖 | `<dependency>ruoyi-common</dependency>` |
| ruoyi-quartz | 直接依赖 | `<dependency>ruoyi-common</dependency>` |
| ruoyi-generator | 直接依赖 | `<dependency>ruoyi-common</dependency>` |
| ruoyi-framework | 间接依赖 | 通过 ruoyi-system 间接获得 |
| ruoyi-admin | 间接依赖 | 通过 framework → system → common 间接获得 |

> 类比：后勤仓库（ruoyi-common）有三个"直接客户"——人力资源部（system）、保洁队（quartz）、文印室（generator）。安保部（framework）不直接找仓库领东西，而是通过人力资源部间接获得。前台大门（admin）更不直接找仓库，它通过安保部→人力资源部→仓库这条链间接获得。

### 为什么 ruoyi-common 不依赖其他内部模块？

> 因为如果 ruoyi-common 依赖了 ruoyi-system，而 ruoyi-system 又依赖 ruoyi-common，就形成了**循环依赖**——A 等 B、B 等 A，Maven 构建时直接报错。
>
> 类比：后勤仓库不能依赖人力资源部——因为人力资源部本身就要从仓库领东西。如果仓库说"我要先等人力资源部给我东西我才能给你东西"，而人力资源部说"我要先等仓库给我东西我才能给你东西"，那就死锁了。

### 📝 通俗总结

> ruoyi-common 在整个 pom.xml 体系中是"地基中的地基"——所有内部模块都直接或间接依赖它，但它不依赖任何内部模块。
>
> 它的 17 个第三方库依赖涵盖了安全认证、数据处理、缓存、文件操作、Web 基础等方方面面，为整个项目提供了完整的"工具箱"。
>
> 这种设计的好处是：其他业务模块（system、quartz、generator）的 pom.xml 可以保持非常简洁——它们不需要自己引入第三方库，只需要依赖 ruoyi-common 就够了。这就是"集中管理、分散使用"的设计思想。

---

## 全文总览速查表

| 区域 | 行号 | 核心作用 | 与其他 pom.xml 的关系 |
|------|------|---------|---------------------|
| 文件头 | 1-4 | XML 格式声明 | 所有模块相同 |
| `<parent>` | 5-9 | 继承根 pom 配置 | 所有子模块都有 |
| `artifactId` | 12 | 模块名称 `ruoyi-common` | 根 pom 的 `dependencyManagement` 中声明了它；system、quartz、generator 的 `dependencies` 中引用了它 |
| `description` | 14-16 | "common通用工具" | 各模块内容不同 |
| `dependencies` | 18-121 | 引入 17 个第三方库 | **不依赖任何内部模块**（避免循环依赖）；所有其他内部模块都依赖它 |
| 无 `<build>` | — | 继承根 pom 编译配置 | 只有 ruoyi-admin 有自己的 build 配置 |

### 一句话总结

> **ruoyi-common 的 pom.xml 有 17 个第三方库依赖、0 个内部模块依赖——它是整个项目依赖链的最底层，为所有其他模块提供安全认证、数据处理、缓存、文件操作等通用工具。它不依赖任何其他内部模块（避免循环依赖），但被所有其他内部模块依赖。这种"集中管理、分散使用"的设计，让其他业务模块的 pom.xml 可以保持极简——只需一行 `ruoyi-common` 依赖，就能获得全部通用能力。**