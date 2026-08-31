---
title: Maven 依赖管理机制详解
tags:
  - Maven
  - Java
  - 依赖管理
  - 若依
  - 构建工具
created: 2026-08-22
---
# Maven 依赖管理机制详解

> [!NOTE] 核心定义
> **Maven = 项目对象模型 (POM) + 依赖管理系统 + 构建生命周期**。
> 它的核心目标是**约定优于配置**，解决 Java 项目“需要什么 Jar 包”以及“如何打包”的问题。

## 🔍 一、Maven 是如何找到 Jar 包的？

Maven 通过 **GAV 坐标** 在仓库中唯一定位一个依赖。

### 1. GAV 坐标
在 `pom.xml` 中，每个依赖都由三个元素唯一确定：
- **G**roupId：组织名（如 `org.springframework.boot`）
- **A**rtifactId：项目名（如 `spring-boot-starter-web`）
- **V**ersion：版本号（如 `2.5.15`）
  
  **在 `pom.xml` 中引入 Web 模块：**
- `<dependency>`
- `<groupId>org.springframework.boot</groupId>`
- `<artifactId>spring-boot-starter-web</artifactId>`
- `<version>2.5.15</version>`
- `</dependency>`
### 2. 仓库查找路径
当你点击刷新 Maven 时，查找顺序如下：
1. **本地仓库** (`C:\Users\用户名\.m2\repository`)：先检查本地是否已下载。
2. **远程仓库** (阿里云镜像/中央仓库)：如果本地没有，从远程下载并缓存到本地。

> [!TIP] 若依的私服配置
> 若依通常建议配置阿里云镜像，修改 `settings.xml` 以加速下载。

## 🔗 二、依赖传递与冲突解决

这是 Maven 最强大的功能，也是最容易出问题的地方。

### 1. 依赖传递
你引入了 A，A 内部需要 B，Maven 会自动把 B 也下载下来。
- 例如：若依引入了 `ruoyi-framework`，Maven 会自动下载其依赖的 `spring-boot-starter` 等。

### 2. 依赖冲突（就近原则）
如果 A 依赖 B 的 1.0 版，C 依赖 B 的 2.0 版，Maven 如何处理？
- **路径最近者优先**：谁离项目根 `pom.xml` 路径最短用谁。
- **第一声明者优先**：如果路径长度相同，谁先在 `pom.xml` 中声明用谁。

### 3. 排除依赖 (Exclusions)
如果某个传递过来的依赖有漏洞或冲突，可以手动排除：

`<dependency>`
`<groupId>org.springframework.boot</groupId>`
`<artifactId>spring-boot-starter-web</artifactId>`
`<exclusions>`
`<exclusion>`
`<groupId>org.springframework.boot</groupId>`
`<artifactId>spring-boot-starter-logging</artifactId>`
`</exclusion>`
`</exclusions>`
`</dependency>`
## 🛠️ 三、Maven 在若依项目中的实战

| 命令            | 作用             | 底层逻辑                     |
| :------------ | :------------- | :----------------------- |
| `mvn clean`   | 清理 `target` 目录 | 删除编译生成的文件                |
| `mvn compile` | 编译主代码          | 调用 **JDK 的 javac**       |
| `mvn package` | 打包             | 编译后生成 `ruoyi-admin.jar`  |
| `mvn install` | 安装到本地仓库        | 将项目 Jar 包复制到本地仓库，供其他项目引用 |

> [!EXAMPLE] 若依的多模块结构
> 若依是**聚合工程**：
> - `ruoyi-admin` (入口)
> - `ruoyi-framework` (核心)
> - `ruoyi-system` (业务)
> - `ruoyi-common` (通用工具)
> 
> Maven 通过 `<modules>` 标签管理它们，确保按正确顺序编译。